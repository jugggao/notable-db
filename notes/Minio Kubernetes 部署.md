---
title: Minio Kubernetes 部署
tags: [Minio]
created: 2022-11-03T08:50:08.459Z
modified: 2022-11-03T10:10:40.141Z
---

# Minio Kubernetes 部署

## 安装 Minio Operator

### 安装 Minio Kubernetes 插件

```shell
$ (
set -x; cd "$(mktemp -d)" &&
OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
KREW="krew-${OS}_${ARCH}" &&
curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
tar zxvf "${KREW}.tar.gz" &&
./"${KREW}" install krew
)

$ echo 'export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"' | tee -a $HOME/.bashrc

$ kubectl krew update
$ kubectl krew install minio

$ kubectl minio version
```

### 初始化 Minio Kubernetes Operator

```shell
$ kubectl minio init
```

- 默认会部署至 `minio-operator` 命名空间，如果需要部署到不同的命名空间使用 `kubectl minio init --namespace $NAMESPACE` 指定命名空间的值。
- 默认会使用 `cluster.local` 集群本地域名配置 Operator DNS 主机名，使用 `kubectl minio init --cluster-domain $CLUSTERDOMAIN` 指定集群本地域名的值。

### 验证 Minio Operator 安装

```shell
$ kubectl get all --namespace minio-operator

NAME                                  READY   STATUS    RESTARTS   AGE
pod/console-5bfdb9cd77-5ld9t          1/1     Running   0          5m19s
pod/minio-operator-868fc4755d-vkpsp   1/1     Running   0          5m19s
pod/minio-operator-868fc4755d-xdd8b   0/1     Pending   0          5m19s

NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
service/console    ClusterIP   192.168.236.35   <none>        9090/TCP,9443/TCP   5m19s
service/operator   ClusterIP   192.168.82.189   <none>        4222/TCP,4221/TCP   5m19s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/console          1/1     1            1           5m19s
deployment.apps/minio-operator   1/2     2            1           5m19s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/console-5bfdb9cd77          1         1         1       5m19s
replicaset.apps/minio-operator-868fc4755d   2         2         1       5m19s

NAME                                                                                             AGE
containernetworkfilesystem.storage.alibabacloud.com/cnfs-nas-c2158c95957b2478781c7786d355dc2c1   5d2h
```

### 打开 Minio Operator 控制台

```shell
$ kubectl minio proxy -n minio-operator

Starting port forward of the Console UI.

To connect open a browser and go to http://localhost:9090

Current JWT to login: Token
```

![](https://min.io/docs/minio/kubernetes/upstream/_images/operator-dashboard1.png)

## 使用 MinIO Operator 控制台部署租户

点击 `Create Tenant +` 创建一个 Minio 租户。

### 租户配置 `Setup` 阶段说明

| 属性                | 说明                                 |
| ------------------- | ------------------------------------ |
| `Name`              | 租户名称                             |
| `Namespace`         | 部署的命名空间                       |
| `Storage Class`     | 选择存储类                           |
| `Number of Servers` | 在租户中部署的 Minio Server Pod 数量 |
| `Drives per Server` | 每个 Minio Server 的



### 阿里云香港配置记录表

```shell
# Setup
## Name
gzuic
## Namespace
data-center

# Configure
## Console Domain
https://pre-gzuic-minio.rvedu.com.cn/console
## MinIO Domains
https://pre-gzuic-minio.rvedu.com.cn
```

