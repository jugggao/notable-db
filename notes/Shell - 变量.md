---
title: Shell - 变量
tags: [Shell]
created: 2022-10-25T09:14:13.198Z
modified: 2022-10-26T10:00:40.003Z
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

一个未被赋值或未初始化的变量拥有空值（null value），但在算术运算中使用未赋值变量是可行的，会被作为 0 处理。

```shell
if [ -z "$unassigned" ]; then
    echo "\$unassigned is NULL."
fi

echo "$uninitialized" # 空行
((uninitialized += 5))
echo "$uninitialized" # 5
```

### 读取变量

读取变量的时候，在变量名前面加上 `$`（取值符）来进行变量读取。

```shell
vairable1=23
echo $vairable1
```

实际上，`$variable` 这种写法是 `${variable}` 的简化形式。在某些情况下，使用 `$variable` 写法会造成语法错误，使用完整形式会更好。

```shell
echo "$USER_on_$HOSTNAME"     # ✕ 读取变量的时候，Bash 会先读取 USER_on_ 这个变量
echo "${USER}_on_${HOSTNAME}" # ✓ 用 ${USER} 才会正常读取 USER 变量
```

如果变量值包含空白符（空格、制表符和换行符），最好放在双引号里面。

```shell
a="A B  C    D"
echo $a   # A B C D
echo "$a" # A B  C    D
```

如果变量值的本身也是变量，可以用 `${!variable}` 读取最终的值。但是不推荐使用这种方法读取被引用的变量，推荐使用 `declare -n` 来定义引用变量，之后我们会详细说明。

```shell
a=3
b=a
c=b
echo $a    # 3
echo $b    # a
echo $c    # b
echo ${!b} # 3
echo ${!c} # a 这里只能读取到 b 变量的值，不会继续读取 a 变量的值
```

### 删除变量

`unset` 命令用来删除一个变量，但是不能使用 `unset` 命令删除只读变量。

```shell
unset variable
```

将一个变量设置为空与删除（`unset`）它不同，尽管它们的表现形式相同。

```shell
a="a string"
echo "\$a = $a"
a=
echo "\$a (null value) = $a" # $a (null value) =

a="a string"
unset a
echo "\$a (uninitialized variable) = $a" # a (uninitialized variable) =
```

### 输出变量

用户创建的变量仅可用于当前 Shell，子 Shell 默认读取不到父 Shell 定义的变量。为了把变量传递给子 Shell，需要使用 `export` 命令输出变量。这样输出的变量，对于子 Shell 来说就是环境变量。

```shell
export vairable
# 变量的赋值和输出可以在一个步骤中完成
export vairable=value
```

脚本只能将变量输出到子进程，即在这个脚本中所调用的命令或程序。在命令行中调用的脚本不能够将变量回传给命令行环境，即子进程不能将变量传回给父进程，所以子 Shell 如果修改继承的变量。

```shell
#!/usr/bin/env bash

```