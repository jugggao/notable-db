---
title: Shell 内建命令 - 输入输出 - echo
title_custom: true
tags: [Shell/Command]
created: 2022-10-10T08:56:55.447Z
modified: 2022-10-10T09:08:54.486Z
---

# Shell 内建命令 - 输入输出 - `echo`



## 1. 用法

### 1.1. 语法

```shell
echo [选项]... [字符串或变量]...
```

### 1.2. 作用

打印一个字符串或变量到标准输出 `stdout`。

### 1.3. 选项

```
  -n	结尾不换行
  -e	启用转义字符
  -E	不启用转义字符（默认）
```

### 1.4. 转义符

使用 `-e` 选项时，若字符串出现以下字符，则进行特殊处理：

```
  \\      \ 字符
  \a      发出警告声音（BEL）
  \b      删除前一个字符
  \c      直接结尾并不换行（后面的字符串不会输出）
  \e      删除后一个字符
  \f      换页
  \n      换行
  \r      回车符
  \t      水平制表符
  \v      垂直制表符
```

## 2. 用例

### 2.1. 禁止换行

默认情况下，每一个 `echo` 命令都会在最后打印一个终端换行符，使用 `-n` 可以禁止这个行为。

```shell
$ echo -n 'Hello'
# 等同于
$ echo -e 'Hello\c'
```

### 2.2. 通过管道给一系列命令提供值

案例：判断变量是否包含字符串

```shell
if echo "$VAR" | grep -q txt; then # if [[ $VAR = *txt* ]]; then
    echo "$VAR contains the substring sequence \"txt\""
fi
```

### 2.3. 与命令替换结合用于给变量赋值

案例：在当前目录更改所有文件名为小写

```shell
#!/usr/bin/env bash
#
# 将工作目录中的每个文件名更改为小写

for filename in *; do # 遍历目录下所有文件
    fname=$(basename "$filename")
    n=$(echo "$fname" | tr '[:upper:]' '[:lower:]') # 将名称标为小写
    [[ "$fname" == "$n" ]] || mv "$fname" "$n"      # 仅改变非全部小写的文件名
done

exit $?
```

## 3. 注意

### 3.1. 使用 `IFS` 输出与原始输出

`echo` 展开变量替换或者命令替换时，如果未使用双引号引用，则会使用 `IFS` 来连接输出内容。如果想输出原始内容，则需要加上双引号。

```shell
$ echo $(ls -l)
total 16 drwxr-xr-x 3 3434 3434 4096 2022-06-22 16:32 alertmanager drwxr-xr-x 3 root root 4096 2022-07-07 13:58 cni drwx--x--x 4 root root 4096 2022-07-11 15:48 containerd drwxr-xr-x 2 3434 3434 4096 2021-12-05 19:15 node_exporter

$ echo "$(ls -l)"
total 16
drwxr-xr-x 3 3434 3434 4096 2022-06-22 16:32 alertmanager
drwxr-xr-x 3 root root 4096 2022-07-07 13:58 cni
drwx--x--x 4 root root 4096 2022-07-11 15:48 containerd
drwxr-xr-x 2 3434 3434 4096 2021-12-05 19:15 node_exporter
```

