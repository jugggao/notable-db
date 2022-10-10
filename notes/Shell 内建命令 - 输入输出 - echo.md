---
title: Shell 内建命令 - 输入输出 - echo
title_custom: true
tags: [Shell/Command]
created: 2022-10-10T08:56:55.447Z
modified: 2022-10-10T09:08:54.486Z
---

# Shell 内建命令 - 输入输出 - `echo`

## 1. 作用

打印一个字符串或变量到标准输出 `stdout`。

## 2. 用法

### 2.1. 语法

```shell
echo [选项]... [字符串或变量]...
```

### 2.2. 选项

```
  -n	结尾不换行
  -e	启用转义字符
  -E	不启用转义字符（默认）
```

### 2.3. 转义符

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

## 3. 用例

### 3.1. 禁止换行

默认情况下，每一个 `echo` 命令都会在最后打印一个终端换行符，使用 `-n` 可以禁止这个行为。

```shell
$ echo -n 'Hello'
# 等同于
$ echo -e 'Hello\c'
```

### 3.2. 通过管道给一系列命令提供值

案例：判断变量是否包含字符串

```shell
if echo "$VAR" | grep -q txt; then # if [[ $VAR = *txt* ]]; then
    echo "$VAR contains the substring sequence \"txt\""
fi
```

### 3.3. 与命令替换结合用于给变量赋值

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
