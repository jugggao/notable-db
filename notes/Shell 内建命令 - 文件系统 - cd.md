---
title: Shell 内建命令 - 文件系统 - cd
tags: [Shell/Command]
created: 2022-10-11T06:05:14.554Z
modified: 2022-10-11T06:36:05.348Z
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
-L      （默认值）如果要切换到的目标目录是一个符号链接，那么切换到符号链接的目录
-P      如果要切换到目标目录是一个符号链接，那么切换到它指向的物理位置目录
```

## 用例

### 常用符号

```shell
cd       # 进入用户主目录
cd /     # 进入根目录
cd ~     # 进入用户主目录
cd ..    # 返回上级目录
cd ../.. # 返回上两级目录
cd -     # 切换到 $OLDPWD
```

### 切换工作目录并执行命令

```shell
(cd /source/directory && tar cf - .) | (cd /dest/directory && tar xpvf -)
# 将整个文件目录下的内容移动到另一个文件目录

# 1. cd /source/directory
#    切换至源目录
# 2. &&
#    如果 cd 命令执行成功才执行下一步操作
# 3. tar cf - .
#    c 选项创建一个归档，f - 指定目标文件为当前标准输出，. 表示在当前目录中执行
# 4. ( ... )
#    表示一个子 Shell
# 5. |
#    管道
# 6. cd /dest/directory
#    切换到目标目录
# 7. tar xpvf -
#    x 命令解压，p 选项保留文件所有者与权限，v 选项打印详细消息，f - 从标准输入中读取

# 等同但比以下代码更优雅：
# cp -a /source/directory/* /dest/directory
# 如果包含隐藏文件及目录：
# cp -a /source/directory/* /source/directory/.[^.]* /dest/directory
```

## 注意

### 使用 `cd ...|| exit` 

有问题的代码：

```shell
cd generated_files
rm -r *.c
```

```
```