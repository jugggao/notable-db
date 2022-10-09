---
title: Debian 修改软件默认配置
tags: [Linux]
created: 2022-09-26T08:53:59.543Z
modified: 2022-10-09T07:24:23.517Z
---

# Debian 修改软件默认配置

`dpkg-reconfigure` 命令是 Debian 系 Linux 中用来重新配置软件包的命令，运行该命令可以重新配置软件包第一次安装后的配置问题。

## 修改默认编辑器

```shell
update-alternatives --config editor
```

## 修改 Shell

在 Debian 中 `/bin/sh` 是 `dash` 的软连接，如果想使用 `bash` 作为默认 Shell 的话，执行以下命令：

```shell
sudo dpkg-reconfigure dash
```

## 修改时区

```shell
sudo dpkg-reconfigure tzdata
```

## 修改语言

```shell
sudo dpkg-reconfigure locales
```

