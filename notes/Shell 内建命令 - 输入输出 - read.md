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

### 为变量赋值

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

### 
