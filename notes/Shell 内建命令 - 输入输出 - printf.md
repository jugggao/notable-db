---
title: Shell 内建命令 - 输入输出 - printf
title_custom: true
tags: [Shell/Command]
created: 2022-10-10T09:07:04.876Z
modified: 2022-10-10T09:10:07.211Z
---

# Shell 内建命令 - 输入输出 - `printf`

## 1. 作用

格式化打印。

## 2. 用法

### 2.1. 语法

```shell
printf [-v var] format [arguments]
```

### 2.2. 选项

```
-v var  将结果输出到变量 var 中而不是标准输出（stdout）
```

### 2.3. 转义符

```
\"          双引号
\\          反斜杠
\a          响铃
\b          退格
\c          截断输出
\e          退出
\f          翻页
\n          换行
\r          回车
\t          水平制表符
\v          竖直制表符
\NNN        八进制数 (1到3位数字)
\xHH        十六进制数 (1到2位数字)
\uHHHH      Unicode 字符附加 4 位十六进制数字
\UHHHHHHHH  Unicode 字符附加 8 位十六进制数字
%%          百分号
```

### 2.4. 转换说明符

```
%a, %A          浮点数，十六进制 p-（%A 为 P-）记数法
%c              ASCII 字符，显示相对应参数的第一个字符
%d, %i          十进制整数
%e, %E          浮点数，分别为 e-（%E 为 E-）记数法
%f, %F          浮点数，十进制记数法
%g, %G          浮点数，根据数值不同自动选择 %f（%G 为 %F）或者 %e（%G 为 %E）。%e（%G 为 %E）格式在指数小于 -4 或者大于等于精度时使用
%s              字符串
%o              无符号八进制整数
%u              无符号十进制整数
%x, %X          十六进制数字 0f（%X 为 0F）的无符号十六进制整数
%%              百分号
%b              展开参数中转义符
%q              将参数反转义用作 Shell 输入
%(fmt)T         根据 strftime(3) 中的转义字符来输出日期时间字符串
```

### 2.5. 长度修饰符

「整数转换说明符」包含 `%d`、`%i`、`%o`、`%u`、`%x` 和 `%X`。

「浮点转换说明符」包含 `%a`、`%A`、`%e`、`%E`、`%f`、`%F`、`%g`、`%G`。

```
digit(s)    字段宽度的最小值。如果该字段不能容纳要打印的数或者字符串，系统会使用更宽的字段
            示例：%4d
.digit(s)   精度。只使用 . 表示其后跟随一个零，所以 %.f 与 %.0f 相同
            示例：%5.2f
h           和整数转换说明符一起使用，表示一个 short char 或者 unsigned short int 类型数值
            示例：%hu、%hx、%6.4hd
hh          和整数转换说明符一起使用，表示一个 signed char 或者 unsigned char 类型数值
            示例：%hhu、%hhx、%6.4hhd
l           和整数转换说明符一起使用，表示一个 long int 或者 unsigned long int 类型数值
            示例：%ld、%8lu
ll          和整数转换说明符一起使用，表示一个 long long int 或 unsigned long long int 类型数值
            示例：%lld、%8llu
L           和浮点转换说明符一起使用，表示一个 long double 类型数值
            示例：%Lf、%10.4Le
j           和整数转换说明符一起使用，表示一个 intmax_t 或 uintmax_t 类型值
            示例：%jd、%8jX
z           和整数转换说明符一起使用，表示一个 size_t 或 ssize_t 类型值
            示例：%zd、%12zx
t           和整数转换说明符一起使用，表示一个 ptrdiff_t 类型值（与两个指针之间的差相对应的类型)
            示例：%td、%12ti
```

### 2.6. 标识符

```
#           使用转换说明符的可选形式。若为 %o，则以 0 开始；若为 %x 和 %X 格式，则以 0x 或 0X 开始；对于所有的浮点数形式，即使后面没有任何数字也将始终包含小数点；对于 %g 和 %G 格式，将防止尾随零被删除
            示例：%#o、%#8.0f、%+#10.3E
' '         有符号的值若为正，则显示时带前导空格，但不显示符号；若为负，则带减号符号。+ 标志会覆盖空格标志
            示例：% 0.2f
0           对于所有数字格式，用前导零而不是用空格填充字段宽度。如果出现 - 标志或指定了精度（对于整数）则忽略该标志
            示例：%010d、%08.3f
-           左对齐
            示例：%-20s
+           有符号的值若为正，则显示带加号的符号；若为负，则带减号的符号
            示例：%+6.2f
'           将十进制转换后的结果添加千分位
            示例：%'u
```

## 3. 用例

### 3.1. 将格式化内容赋值给变量

模拟 C 语言函数 `sprintf()`：

```shell
$ printf -v PI "%1.2f" 3.1415926
$ echo $PI
3.14
```

### 3.2. 格式化错误消息

```shell
#!/usr/bin/env bash

E_BADDIR=65

var=nonexistent_directory

error() {
    printf "$@" >&2
    echo
    exit $E_BADDIR
}

cd $var || error "Can't cd to %s." "$var"
```

### 3.3. 表格对齐

```shell
$ printf "%-5s %-10s %-4s\n" NO Name Mark
printf "%-5s %-10s %-4.2f\n" 01 Tom 90.3456
printf "%-5s %-10s %-4.2f\n" 02 Jack 89.2345
printf "%-5s %-10s %-4.2f\n" 03 Jeff 98.4323
NO    Name       Mark
01    Tom        90.35
02    Jack       89.23
03    Jeff       98.43
```

### 3.4. 输出时间

```shell
$ printf "%(%F %T %z%n)T"
2022-10-10 14:42:35 +0800

$ printf "%(%m-%Y %H:%M)T" $(date +%s)
10-2022 14:43
```

