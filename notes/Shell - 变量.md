---
title: Shell - 变量
tags: [Shell]
created: 2022-10-25T09:14:13.198Z
modified: 2022-10-25T09:53:44.169Z
---

# Shell - 变量

## 简介

变量（vairable）在编程语言中用来表示数据。它本身是一个标记，指向数据在计算机内存中的一个或一组地址。

变量通常出现在算术运算，数量操作及字符串解析中。

## 使用变量

### 变量名

变量名必须遵守下面的规则。

- 字母、数字和下划线字符组成。
- 第一个字符必须时一个字母或一个下划线，不能数数字。
- 不允许出现空格和标点符号。

以下示例是有效的变量名称。

```shell
_ALI
Token_A
var_1
var2
```

以下示例是无效的变量名称。

```shell
2_VAR
-variable
var1-var2
VAR_A!
```

### 变量赋值

定义变量使用 `=`（赋值操作符）来为变量进行赋值，语法如下。

```shell
variable_name=variable_value
```

> 注意：`=` 根据场景即可作为赋值操作符，也可作为比较操作符。

除了使用 `=` 为变量赋值，还有一些其他的变量赋值形式。

```shell
#!/usr/bin/env bash

# 使用赋值操作符（`=`）赋值
a=879
echo "The value of \"a\" is $a."

# 使用 `let` 进行赋值（不推荐，推荐使用算术表达式作为变量值）
let "a=16 + 5"
echo "The value of \"a\" is now $a."

# 在 `for` 循环中赋值（隐式赋值）
echo -n "Values of \"a\" in the loop are: "
for a in 7 8 9 11; do
    echo -n "$a "
done
echo

# 在 `read` 表达式中赋值
read -rp "Enter \"a\" " a
echo "The value of \"a\" is now $a."

exit 0
```

变量值也有多种形式。

```shell
a=string            # 变量 a 赋值为字符串 string
b="a string"        # 如果变量值包含空格，必须放在引号里面
c="a string and $b" # 变量值可以引用其他变量的值
d="\ta string\n"    # 变量值可以使用转义字符
e=$(ls -l)          # 变量值可以是命令替换（命令执行的结果）
f=$((5 * 7))        # 变量值可以是算术运算的结果
```

## 读取变量

读取变量的时候，在变量名前面加上 `$`（取值符）

### 读取变量形式

-
