---
title: xargs
tags: [Shell/Command]
created: 2022-10-08T08:28:30.358Z
modified: 2022-10-10T08:52:03.161Z
---

# xargs

## 1. 简介

### 1.1. 作用

Unix 命令都带有参数，有些命令可以接受「标准输入（stdin）」作为参数：

```shell
$ cat /etc/passwd | grep root
```

上面的代码使用了管道命令（`|`）。管道命令的作用，是将左侧命令（`cat /etc/passwd`）的标准输出转换为标准输入，提供给右侧命令（`grep root`）作为参数。

因为 `grep` 命令可以接受标准输入作为参数，所以上面的代码等同于下面的代码：

```shell
$ grep root /etc/passwd
```

但是，大多数命令都不接受标准输入作为参数，只能直接在命令行输入参数，这导致无法用管道命令传递参数。举例来说，`echo` 命令就不接受管道传参。

```shell
$ echo "hello world" | echo
```

上面的代码不会有输出。因为管道右侧的 `echo` 不接受管道传来的标准输入作为参数。

而 `xargs` 命令则可以将标准输入转为命令行参数：

```shell
$ echo "hello world" | xargs echo
hello world
```

上面的代码将管道左侧的标准输入，转为命令行参数 `hello world`，传给第二个 `echo` 命令。

因此 `xargs` 的作用在于，大多数命令（比如 `rm`、`mkdir`、`ls`）与管道一起使用时，都需要 `xargs` 将标准输入转换为命令行参数。

```shell
$ echo "one two three" | xargs mkdir
```

上面的代码等同于 `mkdir one two three`。如果不加 `xargs` 就会报错，提示 `mkdir` 缺少操作参数。

## 2. 单独使用

> `xargs` 一般不会单独使用，一般都是和管道符（`|`）一起使用。

`xargs` 后面的命令默认是 `echo`。

输入 `xargs` 按下回车以后，命令行就会等待用户输入，作为标准输入。你可以输入任意内容，然后按下 `Ctrl + d` ，表示输入结束，这时 `echo` 命令就会把前面的输入打印出来。

```shell
$ xargs
hello (Ctrl + d)
hello
```

再看一个例子，输入 `xargs find -name` 以后，命令行会等待用户输入所要搜索的文件。用户输入 `*.txt`，表示搜索当前目录下的所有 TXT 文件，然后按下 `Ctrl + d`，表示输入结束。这时就相当执行 `find -name *.txt`。

```shell
$ xargs find -name
"*.txt" (Ctrl + d)
./foo.txt
./hello.txt
```

## 3. 选项

```
-0, --null
    如果输入的 stdin 含有特殊字符，例如反引号 `、反斜杠 \、空格等字符时，xargs 将它还原成一般字符。为默认选项
-a, --arg-file=FILE
    从指定的文件 FILE 中读取输入内容而不是从标准输入
-d, --delimiter=DEL
    指定 xargs 处理输入内容时的分隔符。xargs 处理输入内容默认是按空格和换行符作为分隔符，输出 arguments 时按空格分隔
-E EOF_STR
    EOF_STR 是 end of file string，表示输入的结束
-e, --eof[=EOF_STR]
    作用等同于 -E 选项，与 -E 选项不同时，该选项不符合 POSIX 标准且 EOF_STR 不是强制的。如果没有 EOF_STR 则表示输入没有结束符
-I REPLACE_STR
    将 xargs 输出的每一项参数单独赋值给后面的命令，参数需要用指定的替代字符串 REPLACE_STR 代替。REPLACE_STR 可以使用 {} $ @ 等符号，其主要作用是当 xargs command 后有多个参数时，调整参数位置。例如备份以 txt 为后缀的文件：find . -name "*.txt" | xargs -I {}  cp {} /tmp/{}.bak
-i, --replace[=REPLACE_STR]
    作用同 -I 选项，参数 REPLACE_STR 是可选的，缺省为 {}。建议使用 -I 选项，因为其符合 POSIX
-L MAX_LINES
    限定最大输入行数。隐含了 -x 选项
-l, --max-lines[=MAX_LINES]
    作用同 -L 选项，参数 MAX_LINES 是可选的，缺省为 1。建议使用 -L 选项，因为其符合 POSIX 标准
-n, --max-args=MAX_ARGS
    表示命令在执行的时候一次使用参数的最大个数
-o, --open-tty
    在执行命令之前，在子进程中重新打开stdin作为/dev/TTY。如果您希望xargs运行交互式应用程序，这是非常有用的
-P, --max-procs=MAX_PROCS
    每次运行最大进程；缺省值为 1。如果 MAX_PROCS 为 0，xargs 将一次运行尽可能多的进程。一般和 -n 或 -L 选项一起使用
-p, --interactive
    当每次执行一个 argument 的时候询问一次用户
--process-slot-var=NAME
    将指定的环境变量设置为每个正在运行的子进程中的唯一值。一旦子进程退出，将重用该值。例如，这可以用于初始负荷分配方案
-r, --no-run-if-empty
    当 xargs 的输入为空的时候则停止 xargs，不用再去执行后面的命令了。为默认选项
-s, --max-chars=MAX_CHARS
    命令行的最大字符数，指的是 xargs 后面那个命令的最大命令行字符数，包括命令、空格和换行符。每个参数单独传入 xargs 后面的命令
--show-limits
    显示操作系统对命令行长度的限制
-t， --verbose
    先打印命令到标准错误输出，然后再执行
--help
    显示帮助信息并退出
--version
    显示版本信息并退出
-x, --exit
    配合 -s 使用，当命令行字符数大于 -s 指定的数值时，退出 xargs
```

### 3.1. 更换分隔符

默认情况下，`xargs` 将换行符和空格作为分隔符，把标准输入分解成一个个命令行参数。

而 `-d` 参数可以更改分隔符。

```shell
$ echo -e "a\tb\tc" | xargs -d "\t" echo
a b c
```

### 3.2. 预览需要的执行命令

使用 `xargs` 命令以后，由于存在转换参数过程，有时需要确认一下到底执行的是什么命令。

`-p` 参数打印出要执行的命令，询问用户是否要执行。

```shell
$ echo 'one two three' | xargs -p touch
touch one two three ?...
```

上面的命令执行以后，会打印出最终要执行的命令，让用户确认。用户输入 `y` 以后（大小写皆可），才会真正执行。

`-t` 参数则是打印出最终要执行的命令，然后直接执行，不需要用户确认。

```shell
$ echo 'one two three' | xargs -t rm
rm one two three
```

### 3.3. 使用 `null` 分隔符

由于 `xargs` 默认将空格作为分隔符，所以不太适合处理文件名，因为文件名可能包含空格。

`find` 命令有一个特别的参数 `-print0`，指定输出的文件列表以 `null` 分隔。然后，`xargs` 命令的 `-0` 参数表示用 `null` 当作分隔符。

```shell
$ find /path -type f -print0 | xargs -0 rm
```

上面命令删除 `/path` 路径下的所有文件。由于分隔符是 `null`，所以处理包含空格的文件名，也不会报错。

### 3.4. 处理多行与多项参数

`-L` 参数指定多少行作为一个命令行参数。

```shell
$ echo -e "a\nb\nc\nd" | xargs -L 1 echo
a
b
c

$ echo -e "a\nb\nc\nd" | xargs -L 2 echo
a b
c
```

当指定 `-L 1` 时，`echo` 执行了三次，输出了三行，当指定 `-L 2` 时，`echo` 执行了两次，输出了两行。

`-n` 参数指定每次将多少项，作为命令参数。

```shell
$ echo {0..9} | xargs -n 2 echo
0 1
2 3
4 5
6 7
8 9
```

### 3.5. 将参数传递给多个命令或指定参数位置

`-I REPLACE_STR` 指定每一项命令参数的替代字符串，`REPLACE_STR` 是必选的。

`-i REPLACE_STR` 指定每一项命令参数的替代字符串，`REPLACE_STR` 是可选的，缺省为 `{}`。

```shell
echo "one two three" | xargs -i sh -c 'echo {} && touch {}'

echo "one two three" | xargs -I file sh -c 'echo file && rm file'
```

上面代码中，`{}` 与 `file` 是命令行参数的替代字符串，在执行命令时可以插入任意位置，也可以使用多次。

### 3.6. 并行执行命令

`-P MAX_PROCS` `--max-procs MAX_PROCS` 参数指定同时用多少个进程并行执行命令。`MAX_PROCS` 为 0 时表示不限制进程数。

```shell
$ docker ps -q | xargs -n 1 --max-procs 0 docker kill
```

上面命令表示，同时关闭尽可能多的 Docker 容器，这样运行速度会快很多。
