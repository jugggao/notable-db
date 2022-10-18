---
title: Shell 内建命令 - 变量处理 - set
tags: [Shell/Command]
created: 2022-10-13T03:10:30.319Z
modified: 2022-10-13T03:10:34.462Z
---

# Shell 内建命令 - 变量处理 - `set`

## 用法

### 语法

```shell
set [-abefhkmnptuvxBCHP] [-o option-name] [--] [arg ...]
```

### 作用

`set` 主要作用有两个：

- 将位置参数设置为 `arg ...`
- 通过选项（短选项和长选项）设置 Shell 属性
- 显示系统中已初始化的环境变量和其他变量

### 选项

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
    pipefail        

```






