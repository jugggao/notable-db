---
title: Shell - 变量
tags: [Shell]
created: 2022-10-25T09:14:13.198Z
modified: 2022-10-26T10:00:40.003Z
---

# Shell - 变量

## 1. 简介

变量（vairable）在编程语言中用来表示数据。它本身是一个标记，指向数据在计算机内存中的一个或一组地址。

变量通常出现在算术运算，数量操作及字符串解析中。

## 2. 使用变量

### 2.1. 变量名

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

### 2.2. 变量赋值

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

### 2.3. 读取变量

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

### 2.4. 删除变量

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

### 2.5. 导出变量

用户创建的变量仅可用于当前 Shell，子 Shell 默认读取不到父 Shell 定义的变量。为了把变量传递给子 Shell，需要使用 `export` 命令导出变量。这样导出的变量，对于子 Shell 来说就是环境变量。

```shell
export vairable
# 变量的赋值和输出可以在一个步骤中完成
export vairable=value
```

脚本只能将变量输出到子进程，即在这个脚本中所调用的命令或程序。在命令行中调用的脚本不能够将变量回传给命令行环境，即子进程不能将变量传回给父进程，所以子 Shell 如果修改继承的变量。

```shell
#!/usr/bin/env bash

echo "The value of \$a is $a" # 如果父 Shell 中有导出的变量 a，则会正确输出变量 a 的值
a="hello"                     # 重新赋值
export a                      # 并导出
declare -p a                  # 查看 a 变量的属性和值
echo "The value of \$a is now $a"

exit 0
```

我们在不同条件的下执行此脚本观察结果：

- 不在父 Shell 初始化变量 `a`。

  ```shell
  $ sh variable_export.sh
  The value of $a is
  declare -x a="hello"
  The value of $a is now hello

  $ declare -p a
  -bash: declare: a: not found
  ```

  可以看到，在子 Shell 中最开始无法读取到变量 `a` 的值，然后赋值并导出了变量 `a`，但是对父 Shell 不生效。

- 在父 Shell 赋值变量 `a` 但不导出。

  ```shell
  $ a=23

  $ sh variable_export.sh
  The value of $a is
  declare -x a="hello"
  The value of $a is now hello

  $ declare -p a
  declare -- a="23"
  ```

  可以看到，如果父 Shell 仅对变量赋值而不导出，子 Shell 依然无法读取到变量 `a`。

- 在父 Shell 赋值并导出变量 `a`。

  ```shell
  $ export a=23

  $ sh variable_export.sh
  The value of $a is 23
  declare -x a="hello"
  The value of $a is now hello

  $ declare -p a
  declare -x a="23"
  ```

  可以看到，如果父 Shell 导出了变量，子 Shell 能够读取到正确的值，但是在子 Shell 中的变量操作依然无法影响父 Shell。

## 3. 变量类型

Bash 是一门弱类型语言，不区分变量的类型。无论你输入的是字符串、数字，在 Bash 中都按照字符串类型来存储。所以，Bash 变量就是字符串。但具体是什么类型，Bash 会根据上下文去确定。

以下是字符串与整数之间的自动转换。

```shell
#!/usr/bin/env bash

a=hello # a 为字符串

b=$((a += 2334))                  # 字符串与整数相加
echo "\$b (a string) + 2334 = $a" # Bash 会认为字符串的整数值为 0
((b += 1))                        # 整数与整数相加
echo "\$b = $a"                   # $b = 2335

c=${b/23/BB}        # 将 "23" 替换为 "BB"
echo "\$c = $c"     # $b = BB35
declare -i c        # 将变量 b 声明为整数
echo "\$c = $c"     # $b = BB35 没有改变 b 的值
c="a string"        # 将变量 b 重新赋值为字符串，会报错 a string: syntax error in expression (error token is "string")
((c += 1))          # 但依然可以在算术表达式中计算
echo "\$c = $c"     # $c = 1
((c += "a string")) # 如果算术运算中使用字符串
echo "\$c = $c"     # $c = 1，字符串的整数值会被认为是 0

# 未声明的变量或变量为空值
d=''            # 也可以是 d="" 或 d=
echo "\$d = $d" # $d =
((d += 1))      # 空值也可以进行算术运算
echo "\$d = $d" # %d = 1，空值变为了一个整数

echo "\$e = $e" # $e =
((e += 1))      # 未声明的变量也可以进行算术运算
echo "\$e = $e" # $e = 1，未声明的变量变为了一个整数

((e /= "a string")) # 在这里字符串并没有被设置为 0，会报错为 e /=  : syntax error: operand expected (error token is "/=  ")
((e /= 0))          # 预期之中，报错为 e /= 0: division by 0 (error token is "0")

exit $?
```

通过上面的示例，可以发现在执行算术运算时，Bash 通常将字符串或空值设为 0，但是不要这样做，因为这样会导致一些意外的后果。

在 `if`、`while`、`until` 表达式中，字符串只有为 `"true"` 或者 `"false"` 才能转换为布尔值，否则会进行命令替换。

```shell
if $result; then
    echo "true"
else
    echo "false"
fi # true，空命令

result=''
if $result; then
    echo "true"
else
    echo "false"
fi # true，空命令

result="false"

if $result; then
    echo "true"
else
    echo "false"
fi # false

while $result; do
    echo "true"
    break
done # true

result="a string"
if $result; then
    echo "true"
else
    echo "false"
fi # a: command not found
```

## 4. 特殊变量

### 4.1. 局部变量

仅在代码块或函数中才可见的变量。

### 4.2. 环境变量
