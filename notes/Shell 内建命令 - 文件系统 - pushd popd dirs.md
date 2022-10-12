---
title: Shell 内建命令 - 文件系统 - pushd popd dirs
tags: [Shell/Command]
created: 2022-10-11T09:06:08.915Z
modified: 2022-10-12T01:40:37.848Z
---

# Shell 内建命令 - 文件系统 - `pushd` `popd` `dirs`

## 1. 作用

该命令集时一种为工作目录添加书签的机制，提供了一种有序的在目录中来回移动的方法。

- `pushd` - 将目录添加到目录堆栈顶部，并切换当前工作目录到该目录
- `popd` - 从目录堆栈中删除顶级目录，并切换当前工作目录切换到被删除的目录
- `dirs` - 显示目录堆栈


## 2. 用法

### 2.1. 语法

```shell
pushd [-n] [+N | -N | dir]

popd [-n] [+N | -N]

dirs [-clpv] [+N] [-N]
```

### 2.2. 选项

```
-----
pushd
-----
-n      添加工作目录到目录堆栈但不切换工作目录
+N      从左侧将目录堆栈中的第 N 个目录（从 0 开始计数）至于顶部，并切换当前目录至该目录
-N      从右侧将目录堆栈中的第 N 个目录（从 0 开始计数）至于顶部，并切换当前目录至该目录

----
popd
----
-n      移除目录堆栈中的工作目录但不切换工作目录
+N      从左侧将目录堆栈中的第 N 个目录（从 0 开始计数）移除，并切换当前目录至该目录
-N      从右侧将目录堆栈中的第 N 个目录（从 0 开始计数）移除，并切换当前目录至该目录

----
dirs
----
-c      清空目录堆栈
-l      将目录堆栈内的以 ~ 开头的目录展开
-p      按行显示堆栈中的目录
-v      按行显示堆栈中的目录并添加位置编号
```

## 3. 用例

### 3.1. 切换当前工作目录

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

## 4. 注意

参考 `cd` 命令。