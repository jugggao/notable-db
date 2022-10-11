---
title: Shell 内建命令 - 文件系统 - cd
tags: [Shell/Command]
created: 2022-10-11T06:05:14.554Z
modified: 2022-10-11T06:36:05.348Z
---

# Shell 内建命令 - 文件系统 - `cd`

## 1. 作用

更改当前目录至指定目录，不指定目录时会使用 `HOME` 系统变量。

## 2. 用法

### 2.1. 语法

```shell
cd: cd [-L|[-P [-e]] [-@]] [dir]
```

### 2.2. 选项

```shell
-L      （默认值）如果要切换到的目标目录是一个符号链接，那么切换到符号链接的目录
-P      如果要切换到目标目录是一个符号链接，那么切换到它指向的物理位置目录
```

## 3. 用例

### 3.1. 常用符号

```shell
cd       # 进入用户主目录
cd /     # 进入根目录
cd ~     # 进入用户主目录
cd ..    # 返回上级目录
cd ../.. # 返回上两级目录
cd -     # 切换到 $OLDPWD
```

### 3.2. 切换工作目录并执行命令

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

## 4. 注意

### 4.1. 使用 `cd ...|| exit` 防止 `cd` 出现问题

有问题的代码：

```shell
cd generated_files
rm -r *.c
```

```shell
func(){
  cd foo || return
  do_something
}
```

正确的代码：

```shell
cd generated_files || exit
rm -r *.c
```

```shell
# 对于函数，你也许想使用 return
func(){
  cd foo || return
  do_something
}
```

`cd` 可能由于各种原因而失败：路径拼写错误、目录错误、权限错误、符号链接断开等。

如果出现错误而不处理时，脚本将继续运行并在错误的目录中执行操作。这样会导致混乱，特别是操作涉及创建或删除大量文件时。

为避免这种情况，请确保在 `cd` 出现错误时处理这种情况。处理方法包括：

- `cd foo || exit` - `cd` 错误时直接终止退出
- `cd foo || { echo "Failure"; exit 1; }` - 输出错误消息然后终止推出
- `cd foo || ! echo "Failure"` - 上个代码的简化版
- `if cd foo; then echo "OK"; else echo "Fail"; fi` - 使用判断自定义处理
- `<(cd foo && cmd)` - 在 `<(cd foo || exit; cmd)`、`$(cd foo || exit; cmd)`、`(cd foo || exit; cmd)` 使用 `cd foo && cmd` 作为其替代

### 4.2. 使用 `( subshell )` 避免 `cd` 返回问题

有问题的代码：

```shell
for dir in */; do
  cd "$dir"
  convert index.png index.jpg
  cd ..
done
```

正确的代码：

```shell
for dir in */; do
    (
        cd "$dir" || exit
        convert index.png index.jpg
    )
done
```

或

```shell
for dir in */; do
    cd "$dir" || exit
    convert index.png index.jpg
    cd ..
done
```

当执行 `cd dir; somestuff; cd ..` 时， `cd dir` 可能会失败。在这种情况下，`somestuff` 将在错误的目录中运行，`cd ..` 会切换到一个更错误的目录。在循环中，这可能会导致下一个 `cd` 也失败。

因此我们需要添加 `cd` 失败时的退出状态或使用子 Shell 来限制 `cd` 的影响。并且在子 Shell 中运行，不必在返回上一级工作目录了。

## 5. 参考

- https://linux.die.net/abs-guide/internal.html
- https://www.shellcheck.net/wiki/SC2164
- https://www.shellcheck.net/wiki/SC2103