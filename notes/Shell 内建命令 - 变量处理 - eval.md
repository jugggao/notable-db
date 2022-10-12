---
title: Shell 内建命令 - 变量处理 - eval
tags: [Shell/Command]
created: 2022-10-12T06:03:26.460Z
modified: 2022-10-12T07:18:48.851Z
---

# Shell 内建命令 - 变量处理 - `eval`

## 用法

### 语法

```shell
eval [arg ...]
```

### 作用

`eval` 获取其参数，将它们用空格连接起来，其中包含的任何变量或表达式都会被展开，然后再当前执行环境中将生成的字符串作为 Bash 代码执行。对于从命令行或脚本中生成代码很有用。

同样， `eval` 命令适用于那些一次扫描无法实现其功能的变量。

`eval` 在 Bash 中的工作方式与大多数其他具有 `eval` 功能的语言基本相同。

## 用例

### 展开变量或表达式后再执行

`eval` 先展开参数中的变量或表达式，然后再执行此命令。

```shell
$ pipe="|"
# 直接执行 ls $pipe wc -l 会报错
# 因此需要通过 eval 将 ls $pipe wc -l 中的变量展开，然后再执行此命令
$ eval ls $pipe wc -l
29
```

```shell
$ process=nginx
$ show_process="eval ps ax | grep $process"
$ $show_process
2131281 pts/0    S+     0:00 grep nginx
2845689 ?        Ss     0:00 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
2845690 ?        S      0:00 nginx: worker process
2845691 ?        S      0:00 nginx: worker process
2845692 ?        S      0:00 nginx: worker process
2845693 ?        S      0:00 nginx: worker process
```

```shell
$ ssh-agent -s
SSH_AUTH_SOCK=/tmp/ssh-lSoBMuHdwoP8/agent.2131386; export SSH_AUTH_SOCK;
SSH_AGENT_PID=2131387; export SSH_AGENT_PID;
echo Agent pid 2131387;

# 如果直接执行 ssh-agent -s，尽管运行成功了，但是输出结果中的环境变量并没有导出，上面的结果只是显示给用户看的
# 所以需要使用 eval 来执行 ssh-agent，使其输出的内容能够执行
$ eval $(ssh-agent -s)
Agent pid 2131483
```



