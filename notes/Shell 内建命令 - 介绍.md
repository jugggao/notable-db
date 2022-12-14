---
title: Shell 内建命令 - 介绍
title_custom: true
tags: [Shell/Command]
created: 2022-10-09T02:53:09.916Z
modified: 2022-10-10T09:08:29.450Z
---

# Shell 内建命令 - 介绍

内建命令 `builtin` 是包含在 Bash 工具集中的命令。使用内建命令通常出于性能原因或是需要直接存取 shell 内部变量的考量。内建命令执行速度比外部命令的执行速度快，因为外部命令通常需要派生出一个单独的进程执行。

命令或 shell 本身启动或生成一个子进程用于执行任务的操作被称为派生 forking。新生成的进程被称作子进程，而派生出子进程的进程被称作父进程。当子进程在执行任务时，父进程也仍在运行。

```shell
#!/usr/bin/env bash
# spawn.sh

PIDS=$(pidof sh "$0")                  # 脚本的多个实例的进程 ID
IFS=" " read -r -a P_ARRAY <<< "$PIDS" # 将进程 ID 放置到数组中（为什么？）
echo "$PIDS"

((instances = ${#P_ARRAY[*]} - 1))

echo "$instances instance(s) of this script running."
echo "[Hit Ctl-C to exit.]"
echo

sleep 1 # 闲置等待
sh "$0" # 再运行一次

exit 0 # 这不是必须的；脚本永远不会执行到这里。为什么不是必须的？

#  在键入 Ctl-C 退出脚本后，是否所有生成的脚本实例都会终止？
#  如果是，为什么？

# 注意：
# ----
# 注意不要长时间运行这个脚本。
# 它最终会占用大量的系统资源。

#  你认为让脚本生成大量的自身实例，是否是一个可取的编写脚本的技巧？
#  为什么？
```

通常来说，Bash 的内建命令不会在脚本中派生子进程来执行。而在脚本中调用外部系统命令或筛选器通常需要派生子进程。

一些内建命令可能与系统重名，但是这些都是在 Bash 内部重新实现后的命令。例如 Bash 中的 `echo` 命令与系统命令 `/bin/echo` 的功能基本一致，但是他们本质上并不一样。

```shell
$ type  -t echo
builtin

$ type -t /bin/echo
file
```

关键词 `keyword` 是保留使用的词汇、标记或运算符。关键词在 Shell 中具有特殊意义，是 Shell 语法的组成部分。例如 `for`、`while`、`do` 和 `!` 都是关键词。关键词与内建命令相似，它们都是硬编码到 Bash 中的。但是关键词本身不是命令，而是构成命令的子单位。

