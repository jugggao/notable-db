---
title: Shell 内建命令 - 变量处理 - set
tags: [Shell/Command]
created: 2022-10-13T03:10:30.319Z
modified: 2022-10-13T03:10:34.462Z
---

# Shell 内建命令 - 变量处理 - `set`

## 1. 用法

### 1.1. 语法

```shell
set [-abefhkmnptuvxBCHP] [-o option-name] [--] [arg ...]
```

### 1.2. 作用

`set` 主要有以下作用：

- 显示系统中已初始化的环境变量和其他变量
- 将位置参数设置为 `arg ...`
- 通过选项（短选项和长选项）设置 Shell 属性

### 1.3. 选项

`set` 分为长选项和短选项，长选项可与 `-o` 一起使用。

可通过 `-`、`+` 打开或关闭指定的 Shell 属性：

- `set -<short-option>` 启动指定 Shell 属性
- `set +<short-option>` 关闭指定 Shell 属性
- `set -o <option-name>` 启动指定 Shell 属性
- `set +o <option-name>` 关闭指定 Shell 属性

```
-a  allexport       export 所有定义过的变量（设置为环境变量）
-b  notify          当后台运行的作业终止时, 给出通知（脚本中并不常见）
-e  errexit         当一个命令返回非零值时, 就退出脚本（除了 until、while loops、if-tests、list constructs）
-f  noglob          禁用文件名扩展（即禁用 globbing）
-h  hashall         记录命令所在的位置（将命令路径 hash 下来），避免再次查询，默认开启
-k  keyword         看可以将环境变量设置放在命令行的任何位置，而不是必须在命令的前面（很少会用到，设置临时环境变量放在命令行前面就行）
-m  monitor         开启监控模式，可以通过 Job Control 来控制进程的停止、继续，后台或前台执行等（此模式在交互式 Shell 才会用到，脚本中不会使用）
-n  noexec          从脚本中读取和解析命令，但不执行（语法检测）
-p  privileged      开启特权模式（即以 suid 的身份运行脚本，谨慎使用）
-t  onecmd          读取并执行随后的一个命令后退出 Shell
-u  nounset         如果尝试使用了未定义的变量, 就会输出一个错误消息, 然后强制退出
-v  verbose         在执行每个命令之前, 把每个命令打印至标准输出（调试使用）
-x  xtrace          与 -v 选项类似, 但是会打印完整命令，即完成所有扩展和替换（调试使用）
-B  braceexpand     开启大括号扩展，默认开启
-C  noclobber       防止重定向时覆盖文件，需要时可以通过 >| 重定向运算符进行覆盖
-E  errtrace        开启后 ERR trap 都会被 Shell 函数、命令替换和子 Shell 中执行的命令继承
-H  histexpand      启用 ! 风格历史扩展
-P  physical        开启后，执行命令时会使用实际的文件和目录代替符号链接
-T  functrace       与 -E 类似，开启后 DEBUG trap 与 RETURN trap 会被后续的环境继承
-                   选项结束标志，后面的参数为位置参数
--                  如果后面没有参数，则释放（unset）位置参数；如果后面有参数，则位置参数会依次设置到参数列表中
    emacs           使用 emacs 样式命令行编辑界面（此模式在交互式 Shell 才会用到，脚本中不会使用）
    history         开启历史命令（此模式在交互式 Shell 才会用到，脚本中不会使用）
    ignoreeof       开启后读取 EOF interactive-comments 时不会退出，效果与命令 IGNOREEOF=10 相同（避免使用此选项，使用后者来配置）
    pipefail        开启后，只要一个子命令失败，整个管道命令就失败，脚本就会退出
    posix           开启后，以 POSIX 模式运行
    vi              启用 vi 样式的命令行编辑界面（此模式在交互式 Shell 才会用到，脚本中不会使用）
```

## 2. 查看系统配置示例

不带任何选项或参数调用 `set` 会列出所有已初始化的环境变量和其他变量。

```shell
$ set
BASH=/bin/bash
BASHOPTS=checkwinsize:cmdhist:complete_fullquote:expand_aliases:extglob:extquote:force_fignore:globasciiranges:histappend:interactive_comments:login_shell:progcomp:promptvars:sourcepath
BASH_ALIASES=()
BASH_ARGC=([0]="0")
BASH_ARGV=()
BASH_CMDS=()
BASH_COMPLETION_VERSINFO=([0]="2" [1]="11")
BASH_LINENO=()
BASH_REMATCH=()
BASH_SOURCE=()
BASH_VERSINFO=([0]="5" [1]="1" [2]="4" [3]="1" [4]="release" [5]="x86_64-pc-linux-gnu")
BASH_VERSION='5.1.4(1)-release'
COLUMNS=178
...
```

## 3. 设置位置参数示例

### 3.1. 使用 `set ` 分配位置参数

```shell
#!/usr/bin/env bash

variable="one two three four five"
read -ra variable_array <<< "$variable"
set -- "${variable_array[@]}"
# 设置位置参数分配 $variable 中的内容

first_param=$1
second_param=$2
shift 2 # 移除前两个位置参数
remaining_params="$*"

echo "first parameter = $first_param"           # one
echo "second parameter = $second_param"         # two
echo "remaining parameters = $remaining_params" # three four five

# 重新设置位置变量
set -- "${variable_array[@]}"
first_param=$1
second_param=$2
echo "first parameter = $first_param"   # one
echo "second parameter = $second_param" # two

# 释放位置变量
set --
first_param=$1
second_param=$2
echo "first parameter = $first_param"   # (null value)
echo "second parameter = $second_param" # (null value)

exit 0
```

### 3.2. 使用 `set` 将命令输出作为位置参数

```bash
#!/usr/bin/env bash
# script "set_test"
# 使用三个命令行参数调用此脚本
# 例如：sh set-test one two three

echo "Positional parameters before set \$(uname -a):"
echo "Command-line argument #1 = $1"
echo "Command-line argument #2 = $2"
echo "Command-line argument #3 = $3"

# 以下两个步骤等同于 set $(uname -a)，建议使用以下的步骤
read -ra uname_array < <(uname -a)
set -- "${uname_array[@]}"

echo "$_"

echo "Positional parameter after set \$(uname -a):"
# $1, $2 $3 等重新赋值为数组中的元素
echo "Field #1 of 'uname -a' = $1"
echo "Field #2 of 'uname -a' = $2"
echo "Field #3 of 'uname -a' = $3"
echo "Field #4 of 'uname -a' = $4"
echo "Field #5 of 'uname -a' = $5"
echo "Field #6 of 'uname -a' = $6"
echo "Field #7 of 'uname -a' = $7"
echo "Field #8 of 'uname -a' = $8"
echo "Field #9 of 'uname -a' = $9"
echo "Field #10 of 'uname -a' = ${10}"
echo "Field #11 of 'uname -a' = ${11}"
echo ---
echo "$_"

exit 0
```

### 3.3. 反转脚本参数

```shell
#!/usr/bin/env bash
set -- "a b" "c" "d e"

OIFS=$IFS
IFS=:

until [ $# -eq 0 ]; do
    echo "### k0 = "$k"" # $k 赋值之前
    k=$1:$k              # 将位置参数附加到 $k 之前
    echo "### k = "$k""  # $k 赋值之后
    shift
done

# echo
read -ra params <<< "$k"
echo
set -- "${params[@]}"
echo -
echo $#
echo -
echo

for i; do     # 省略 'in list' 则会循环位置参数
    echo "$i" # 查看新的位置参数
done

IFS=$OIFS
exit 0
```

执行此脚本：

```shell
$ sh revposparams.sh 
### k0 = 
### k = a b 
### k0 = a b 
### k = c a b 
### k0 = c a b 
### k = d e c a b 

-
3
-

d e
c
a b
```

## 4. 设置子 Shell 环境的运行参数

### 4.1. `set -e` - 脚本中命令出错即终止执行

脚本里有运行失败的命令（返回值非 0），Bash 会默认继续执行后面的命令。

```shell
#!/usr/bin/env bash

cd notexistdir
echo "The current directory is $(pwd)"
```

使用 `set -e` 可使脚本只要发生错误，就终止执行。

```shell
#!/usr/bin/env bash
set -e

cd notexistdir
echo "The current directory is $(pwd)"
```

以上代码执行时不会在执行 `echo` 指令。

如果希望在某些命令失败的情况下继续执行，可以临时关闭此属性。

```shell
set +e
command1
command2
set -e
```

还有一种办法是使用 `command || true`，使命令执行失败后也不会返回非 `0`。

```shell
#!/usr/bin/env bash
set -e

cd notexistdir || true
echo "The current directory is $(pwd)"
```

### 4.2. `set -o pipefail` - 脚本管道命令出错即终止运行

`set -e` 有一个例外情况，就是不适用于管道命令。因为 Bash 会把管道命令中最后一个子命令的返回值作为整个命令的返回值。也就是说，只要最后一个命令不失败，管道命令总是会执行成功，因此它后面的命令依然会执行。

```shell
#!/bin/bash
set -e

cd notexistdir | echo a
echo "The current directory is $(pwd)"
```

使用 `set -o pipefail` 可以使管道命令中只要一个子命令执行失败，整个管道命令就失败，脚本就会终止执行。

```shell
#!/bin/bash
set -eo pipefail

cd notexistdir | echo a
echo "The current directory is $(pwd)"
```

运行后结果如下：

```shell
$ sh script.sh
a
set_e.sh: line 4: cd: notexistdir: No such file or directory
```

可以看到，最后的 `echo` 没有执行。


### 4.3. `set -E` - 脚本中函数能够继承 `trap` 命令

一旦设置了 `set -e`，会导致函数内的错误不会被 `trap` 命令捕获。`set -E` 可以纠正这个行为，使函数也能继承 `trap` 命令。

```shell
#!/usr/bin/env bash
set -e

trap "echo ERR trap fired!" ERR

myfunc() {
    # 'foo' 使一个不存在的命令
    foo
}

myfunc
```

运行结果如下：

```shell
$ sh set_flags.sh 
set_flags.sh: line 9: foo: command not found
```

由于设置了 `set -e`，函数内部的报错并没有被 `trap` 命令捕获，需要加上 `set -E` 参数才可以。

```shell
#!/usr/bin/env bash

set -eE

trap "echo ERR trap fired!" ERR

myfunc() {
    # 'foo' 使一个不存在的命令
    foo
}

myfunc
```

执行脚本，可以看到 `trap` 命令生效了：

```shell
$ sh set_flags.sh 
set_flags.sh: line 9: foo: command not found
ERR trap fired!
```

### 4.4. `set -u` - 脚本中使用不存在的变量即终止运行

执行脚本时，如果遇到不存在的变量，Bash 默认忽略它。

```shell
#!/bin/bash

a='Bash'
echo $a $b
```

运行时，执行结果如下：

```shell
$ sh set_flags.sh
Bash
```

可以看到，Bash 会忽略未分配的变量而不会出现任何错误。

`set -u` 就用来改变这种行为，使脚本遇到不存在的变量会报错，并停止执行。

```shell
#!/bin/bash
set -u

a='Bash'
echo $a $b
```

运行结果如下：

```shell
$ sh set_flags.sh 
set_flags.sh: line 5: b: unbound variable
```

可以看到，脚本报错，并且不再执行后面的语句。

### 4.5. `set -v` - 调试脚本时回显输入的命令

`set -v` 在执行脚本时会回显所输入的命令，打印的是命令本身，不进行任何扩展。

```shell
#!/usr/bin/env bash

set -v
n=3
while [ $n -gt 0 ]; do
    n=$((n - 1))
    echo $n
    sleep 1
done

echo -e "\n---\n"

for ((i = 3; i >= 0; i--)); do
    echo $((i * 2))
    sleep 1
done

exit 0
```

输出结果如下：

```shell
$ sh set_flags.sh 
n=3
while [ $n -gt 0 ]; do
    n=$((n - 1))
    echo $n
    sleep 1
done
2
1
0

echo -e "\n---\n"

---


for ((i = 3; i >= 0; i--)); do
    echo $((i * 2))
    sleep 1
done
6
4
2
0

exit 0
```

### 4.6. `set -x` - 调试脚本时打印具体执行的命令及命令参数

默认情况下，脚本执行后只输出结果，没有其他内容。如果多个命令连续执行，它们的运行结果就会连续输出。有时候会分不清，某一段内容时什么命令产生的。

`set -x` 用来在运行结果之前，先输出执行那一行的命令。与 `set -v` 不同的是，`set -x` 会打印具体执行的命令，以及命令的参数。这些参数是经过 Bash 扩展后的参数，可以方便看到各个变量值扩展后的结果是什么、某个变量是否扩展为空导致参数个数发生变化等等。

```shell
#!/usr/bin/env bash

set -x
n=3
while [ $n -gt 0 ]; do
    n=$((n - 1))
    echo $n
    sleep 1
done
set +x

echo -e "\n---\n"

set -x
for ((i = 3; i >= 0; i--)); do
    echo $((i * 2))
    sleep 1
done
set +x

exit 0

```

输出结果如下：

```shell
$ sh set_flags.sh 
+ n=3
+ '[' 3 -gt 0 ']'
+ n=2
+ echo 2
2
+ sleep 1
+ '[' 2 -gt 0 ']'
+ n=1
+ echo 1
1
+ sleep 1
+ '[' 1 -gt 0 ']'
+ n=0
+ echo 0
0
+ sleep 1
+ '[' 0 -gt 0 ']'
+ set +x

---

+ (( i = 3 ))
+ (( i >= 0 ))
+ echo 6
6
+ sleep 1
+ (( i-- ))
+ (( i >= 0 ))
+ echo 4
4
+ sleep 1
+ (( i-- ))
+ (( i >= 0 ))
+ echo 2
2
+ sleep 1
+ (( i-- ))
+ (( i >= 0 ))
+ echo 0
0
+ sleep 1
+ (( i-- ))
+ (( i >= 0 ))
+ set +x
```

上面的例子中，在调试过程中我们只对特定的代码打开命令输出。

### 4.7. `set -C` - 禁止覆盖相同名称的现有文件

`set -C` 可以防止使用重定向运算符 `>` 覆盖已经存在的文件。

```shell
#!/usr/bin/env bash

echo 'New file' > myfile

set -C
echo 'Add context to file' >> myfile
echo 'An existing file' > myfile

exit 0
```

输出结果如下：

```shell
$ sh set_flags.sh 
set_flags.sh: line 7: myfile: cannot overwrite existing file
```

### 4.8. `set -f` - 禁止使用通配符进行文件名扩展

默认情况下，我们可以使用通配符例如（`?`、`*` 或 `[]`）搜索文件。Bash 使用指定的通配符生成模式并将它们与文件名进行匹配。此功能成为 [globbing](https://en.wikipedia.org/wiki/Glob_(programming))。

`set -f` 表示不对通配符进行文件名扩展。

```shell
#!/usr/bin/env bash

ls -- *file

set -f
ls -- *file

exit 0
```

输出结果如下：

```shell
$ sh set_flags.sh 
myfile
ls: cannot access '*file': No such file or directory
```

可以看到，`set -f` 后面的 `ls` 命令没有使用通配符来搜索文件

## 5. 参考

- `https://linux.die.net/abs-guide/options.html`
- `https://wangdoc.com/bash/set.html`