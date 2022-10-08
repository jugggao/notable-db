---
title: Bash 开发工具介绍
tags: [Bash]
created: 2022-10-08T06:47:02.749Z
modified: 2022-10-08T07:30:14.554Z
---

# Bash 开发工具介绍

## `shfmt`

shell 格式化工具，在 VS Code 插件 shell-format 中引用此工具进行 Bash 语言格式化。

使用命令行或 VS Code 插件中可以配置一些选项来控制格式化。

### 通用选项

```shell
-l, --list
	列出未格式化的文件

-w, --write
	将结果写入文件而不是标准输出

-d, --diff
	显示差异

-s, --simplify
	简化代码

-mn, --minify
	缩小代码
```

### 解析选项

```shell
-ln, --language-dialect <str>
	指定解析的语言

-p, --posix
	-ln=posix 的简写

-filename str
	指定标准输入文件名
```

### 格式化输出选项

```shell
-i, --indent <uint>
	<uint> 为 0 时为 tab，大于 0 时表示空格数

-bn, --binary-next-line
	像 && 和 | 这样的二进制操作可以开始一行

-ci, --switch-case-indent
	Switch cases 开启缩进

-sr, --space-redirects
	重定向运算符后将跟一个空格

-kp, --keep-padding
	保留对齐填充

-fn, --func-next-line
	函数起始大括号放置在单独的行上
```

### 参考

https://www.mankier.com/1/shfmt








