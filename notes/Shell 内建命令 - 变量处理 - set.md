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
-h  hashall         记录命令所在的位置（将命令路径 hash 下来），避免再次查询
-k  keyword         看可以将环境变量设置放在命令行的任何位置，而不是必须在命令的前面（很少会用到，设置临时环境变量放在命令行前面就行）
-m  monitor         开启监控模式，可以通过 Job Control 来控制进程的停止、继续，后台或前台执行等（此模式在交互式 Shell 才会用到，脚本中不会使用）
-n  noexec          
```






