---
title: Shell 内建命令 - 文件系统 - pushd popd dirs
tags: [Shell/Command]
created: 2022-10-11T09:06:08.915Z
modified: 2022-10-11T09:06:16.575Z
---

# Shell 内建命令 - 文件系统 - `pushd` `popd` `dirs`

## 作用

该命令集时一种为工作目录添加书签的机制，提供了一种有序的在目录中来回移动的方法。

- `pushd` - 将目录添加到目录堆栈顶部，并切换当前工作目录到该目录
- `popd` - 从目录堆栈中删除顶级目录，并切换当前工作目录切换到被删除的目录
- `dirs` - 显示目录堆栈


## 用法

### `pushd`

```shell
pushd [-n] [+N | -N | dir]
```


