---
title: Shell 内建命令 - 输入输出 - read
tags: [Shell/Command]
created: 2022-10-10T09:11:19.339Z
modified: 2022-10-10T09:11:36.077Z
---

# Shell 内建命令 - 输入输出 - `read`

## 作用

从标准输入读取变量的值。也就时说，以交互式从键盘获取输入。

## 用法

### 语法

```
read [-ers] [-a array] [-d delim] [-i text] [-n nchars] [-N nchars] [-p prompt] [-t timeout] [-u fd] [name ...]
```

### 选项

```
-a array        把读取的数据赋值给数组 array，从下标 0 开始
-d delim        指定界定符，用字符串 delim 指定读取结束的位置，而不是换行符
-e              获取用户输入时，使用 Bash 内置的 Readline 库读取行。即对功能键进行编码转换，不会直接显示功能键对应的字符，使其可以使用功能键（例如 Home End Tab 键等）
-i text         使用 text 作为 Readline 的初始文本，只能与 -e 结合使用
-n nchars       最多接受 nchars 个字符的输入
-N nchars       只有读取到 nchars 个字符后读取才结束，除非遇到 EOF 或读取超时
-p prompt       显示 prompt 提示信息
-r              不进行转义
-s              不会在屏幕上显示输入的字符
-t timeout      设置超时时间，单位为秒
-u fd           使用文件描述符 fd 作为输入源，而不是标准输入，类似于重定向
```

## 用例

### 使用 `read` 为变量赋值

```shell
#!/usr/bin/env bash
# "Reading" variable.

read -rp "Enter the value of vairable 'var1': " var1 # 注意 var1 前面没有 $ 符号，因为正在设置它
echo "var1 = $var1"

echo

# 单个 read 语句可以设置多个变量
read -rp "Enter the values of vairables 'var2' and 'var3'\n(separated by a space or tab): " var2 var3
echo "var2 = $var2      var3 = $var3"
# 如果只设置了一个值，其他变量将不设置（null）

exit 0
```

### 使用 `read` 不设置变量名

```shell
#!/usr/bin/env bash
# read-novar.sh

read -rp "Enter a value: " var
echo "\"var\" = $var"

echo

read -rp "Enter another: " # 没有为 read 提供变量名
var="$REPLY"               # 读取到的输入会分配给内置变量 $REPLY
echo "\"var\" = $var"

echo

exit 0
```

### 使用 `read` 读取多行输入

```shell
#!/usr/bin/env bash

echo "Enter a string terminated by a \\, then press <ENTER>."
echo "Then, enter a second string, and again press <ENTER>."
read var1       # 当读取 $var1 时，输入
                # first line \
                # second line 
echo "var1 = $var1"
# 此时将会输出 var1 = first line second line
# 因为对于 \ 结尾的每一行，都会在一下行收到提示，要求继续输入

echo

echo "Enter another string terminated by a \\ , then press <ENTER>."
read -r var2    # -r 选项可以使 \ 按照字面量读取
                # 输入 first line \
echo "var2 = $var2"
# 此时将会输出 var2 = first line \
# 也可以使用 \ 为结尾来结束输入了

echo

exit 0
```

### 使用 `read` 检测功能键

```shell
#!/usr/bin/bash env

SUCCESS=0
OTHER=65

uparrow=$'\x1b[A'
downarrow=$'\x1b[B'
leftarrow=$'\x1b[D'
rightarrow=$'\x1b[C'
uparrow=$'\x1b[A'
home=$'\x1b[1'
end=$'\x1b[4'
pgup=$'\x1b[5'
pgdn=$'\x1b[6'

read -r -s -n3 -p "Hit an arrow key: " x

case "$x" in
"$uparrow")
    echo "You pressed up-arrow"
    ;;
"$downarrow")
    echo "You pressed down-arrow"
    ;;
"$leftarrow")
    echo "You pressed left-arrow"
    ;;
"$rightarrow")
    echo "You pressed right-arrow"
    ;;
"$home")
    echo "You pressed Home"
    ;;
"$end")
    echo "You pressed End"
    ;;
"$pgup")
    echo "You pressed PgUp"
    ;;
"$pgdn")
    echo "You pressed PgDn"
    ;;
*)
    echo "Some other key pressed"
    exit $OTHER
    ;;
esac

exit $SUCCESS
```


### 设置 `read` 读取超时时间

```shell
#!/usr/bin/env bash

TIMELIMIT=4 # 4 seconds

read -r -t $TIMELIMIT variable

if [ -z "$variable" ]; then
    echo "Timed out, variable still unset."
else
    echo "variable = $variable"
fi

exit 0
```