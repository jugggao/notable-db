---
title: Shell 内建命令 - 文件系统 - pwd
tags: [Shell/Command]
created: 2022-10-11T08:21:57.106Z
modified: 2022-10-11T08:24:02.991Z
---

# Shell 内建命令 - 文件系统 - `pwd`

## 作用

打印当前工作目录，效果与读取内置变量 `$PWD` 的值一样。

## 用法

### 语法

```shell
pwd [-LP]
```

### 选项

```shell
-L      （默认值）打印环境变量 $PWD 的值，可能为符号链接
-P      打印当前工作目录的物理位置
```






