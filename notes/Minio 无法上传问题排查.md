---
title: Minio 无法上传问题排查
tags: [Minio]
created: 2022-09-27T06:31:00.251Z
modified: 2022-09-27T08:32:39.770Z
---

# Minio 无法上传问题排查

## 1. 背景

- 两个 Minio 服务器做 Site Replication，一个服务器部署模式为单节点纠删卷，另一个集群单节点单磁盘，数据无法正常同步。
- 结束同步后，单节点单磁盘的 Minio 服务器，无法上传文件。

日志报错如下：

```subrip-text
API: SYSTEM()
Time: 06:35:42 UTC 09/27/2022
DeploymentID: f57795d0-a97f-4794-aa20-276727947ff1
Error: : ConfigureReplication: site replication service account not found (*fmt.wrapError)
       4: internal/logger/logger.go:278:logger.LogIf()
       3: cmd/site-replication.go:4136:cmd.(*SiteReplicationSys).healBucketReplicationConfig()
       2: cmd/site-replication.go:3571:cmd.(*SiteReplicationSys).healBuckets()
       1: cmd/site-replication.go:3497:cmd.(*SiteReplicationSys).startHealRoutine()
```

## 2. 解决过程

1. 查看 Minio Replication 状态

   ```shell
   $ mc admin replicate info minio

   SiteReplication enabled for:

   Deployment ID                        | Site Name       | Endpoint
   ```

   没有 Replication 配置。

2. 查看当前 Bucket replication `ARN` 资源状态

   ```shell
   $ mc admin bucket remote ls minio
   Remote URL                  Source               ->Target                ARN                                                                               SYNC PROXY
   https://inner-minio.oook.cn vmc-infra            ->vmc-infra             arn:minio:replication::d578d8b6-8c00-41f5-ae06-0d981142cce0:vmc-infra                  proxy
   https://inner-minio.oook.cn zsdtest              ->zsdtest               arn:minio:replication::5c224f03-7262-4a16-a406-6ac5aca44d60:zsdtest                    proxy
   https://inner-minio.oook.cn heroku-buildpack-ruby->heroku-buildpack-ruby arn:minio:replication::0adfa312-73ba-4295-8b91-137ca43c8710:heroku-buildpack-ruby      proxy
   https://inner-minio.oook.cn packages-backup      ->packages-backup       arn:minio:replication::b801ea4e-0e00-45ab-ab50-7e95ed300fb2:packages-backup            proxy
   https://inner-minio.oook.cn maylogs              ->maylogs               arn:minio:replication::f40c1160-753e-4759-bf8f-d95037946f0d:maylogs                    proxy
   https://inner-minio.oook.cn minio-02             ->minio-02              arn:minio:replication::056b111d-a3c7-43e8-863a-4c221ab7ecd7:minio-02                   proxy
   https://inner-minio.oook.cn test                 ->test                  arn:minio:replication::7aab1e9a-c397-4e0b-a8d6-727d61d6c149:test                       proxy
   https://inner-minio.oook.cn oook                 ->oook                  arn:minio:replication::02188405-796a-4ab9-b1e6-5b3bda22d72f:oook                       proxy
   https://inner-minio.oook.cn ebopo                ->ebopo                 arn:minio:replication::1ef16929-b0db-409f-9f44-c07c20e442d4:ebopo                      proxy
   https://inner-minio.oook.cn oooklive             ->oooklive              arn:minio:replication::df0d22cb-6b54-4c24-ab87-2e965bdfff82:oooklive                   proxy
   https://inner-minio.oook.cn images-backup        ->images-backup         arn:minio:replication::e2e669e9-a23d-4aae-8fec-fb19a31c8a68:images-backup              proxy
   https://inner-minio.oook.cn sql-backup           ->sql-backup            arn:minio:replication::e5c5327c-09e6-4fa9-b626-7911f07e261b:sql-backup                 proxy
   https://inner-minio.oook.cn sql-record           ->sql-record            arn:minio:replication::2892582c-14e1-4319-a999-8528bce3457e:sql-record                 proxy
   ```

   发现还有很多正在同步的数据，逐一删掉。

3. 关闭单节点单磁盘服务器上的 Bucket 版本控制

   经过查看 https://github.com/minio/mc/issues/3370，判断的原因是单机单磁盘的服务器上的 Bucket 开启了版本控制功能（之前双向同步导致的），但是单磁盘不支持版本控制，所以导致无法上传。

   ```shell
   $ mc version  info  minio/vmc-infra
   minio/vmc-infra versioning is enabled
   ```

   关闭 Bucket 的版本控制功能即可正常上传：

   ```shell
   $ mc version suspend minio/vmc-infra
   minio/vmc-infra versioning is suspended
   ```
