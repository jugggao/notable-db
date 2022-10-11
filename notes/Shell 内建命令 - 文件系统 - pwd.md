---
title: Shell 内建命令 - 文件系统 - pwd
tags: [Shell/Command]
created: 2022-10-11T08:21:57.106Z
modified: 2022-10-11T08:24:02.991Z
---

# Shell 内建命令 - 文件系统 - `pwd`

## 1. 作用

打印当前工作目录，效果与读取内置变量 `$PWD` 的值一样。

## 2. 用法

### 2.1. 语法

```shell
pwd [-LP]
```

### 2.2. 选项

```shell
-L      （默认值）打印环境变量 $PWD 的值，可能为符号链接
-P      打印当前工作目录的物理位置
```

> 外部命令 `/usr/bin/pwd` 中 `-P` 为默认选项

## 3. 用例

### 3.1. 输出当前工作目录

```shell
#!/usr/bin/env bash

E_BADDIR=65

dir1=/usr/local
dir2=/var/spool

error() {
    printf "$@" >&2
    echo
    exit $E_BADDIR
}

pushd $dir1 || error "Can't pushd to %s." $dir1
# 切换到 'dir1' 目录，并将目录堆栈打印至标准输出
echo "Now in directory $(pwd)."

pushd $dir2 || error "Can't pushd to %s." $dir2
echo "Now in directory $(pwd)."

echo "The top entry in the DIRSTACK array is ${DIRSTACK[0]}"
popd || error "Can't popd to %s" "${DIRSTACK[0]}"
echo "Now back in directory $(pwd)."

popd || error "Can't popd to %s" "${DIRSTACK[0]}"
echo "Now back in original working directory $(pwd)"

exit 0

# 如果不使用 `popd` 会发生什么？
# 您最终会进入到哪个目录当中？
```



