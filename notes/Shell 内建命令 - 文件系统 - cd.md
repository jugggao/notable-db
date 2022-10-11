---
title: Shell 内建命令 - 文件系统 - cd
tags: [Shell/Command]
created: 2022-10-11T06:05:14.554Z
modified: 2022-10-11T06:05:19.268Z
---

# Shell 内建命令 - 文件系统 - `cd`

## 作用

更改当前目录至指定目录，不指定目录时会使用 `HOME` 系统变量。

## 用法

### 语法

```shell
cd: cd [-L|[-P [-e]] [-@]] [dir]
```

### 选项

```shell
      -L	force symbolic links to be followed: resolve symbolic
    		links in DIR after processing instances of `..'
      -P	use the physical directory structure without following
    		symbolic links: resolve symbolic links in DIR before
    		processing instances of `..'
      -e	if the -P option is supplied, and the current working
    		directory cannot be determined successfully, exit with
    		a non-zero status
      -@	on systems that support it, present a file with extended
    		attributes as a directory containing the file attributes
```