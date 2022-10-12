---
title: Minio 创建权限和用户
tags: [Minio]
created: 2022-10-12T09:41:05.962Z
modified: 2022-10-12T09:42:22.330Z
---

# Minio 创建权限和用户

```shell
mc config host list
mc admin policy list dr-site-cn
mc admin user add dr-site-cn developer MQLG67DoUHQGhCm4rmUuYXMA

mc admin policy add dr-site-cn developer developer-policy.json
mc admin policy set dr-site-cn developer user=developer
```

`developer-policy.json`：

```json
{
   "Version" : "2012-10-17",
   "Statement" : [
      {
         "Effect" : "Allow",
         "Action" : [
            "s3:ListAllMyBuckets",
            "s3:GetBucketLocation",
            "s3:ListBucket",
            "s3:GetObject",
            "s3:PutObject",
            "s3:PutObjectTagging",
            "s3:GetObjectTagging",
            "s3:DeleteObject",
            "s3:ListMultipartUploadParts",
            "s3:ListBucketMultipartUploads",
            "s3:GetBucketNotification",
            "s3:PutBucketNotification",
            "s3:ListenNotification",
            "s3:ListenBucketNotification",
            "s3:PutLifecycleConfiguration",
            "s3:GetLifecycleConfiguration",
            "s3:PutEncryptionConfiguration",
            "s3:GetEncryptionConfiguration"

         ],
         "Resource" : "arn:aws:s3:::oook/*"
      }
   ]
}
```
