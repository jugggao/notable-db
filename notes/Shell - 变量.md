---
title: Shell - 变量
tags: [Shell]
created: 2022-10-25T09:14:13.198Z
modified: 2022-10-25T09:53:44.169Z
---

# Shell - 变量

## 简介

变量（vairable）在编程语言中用来表示数据。它本身是一个标记，指向数据在计算机内存中的一个或一组地址。

变量通常出现在算术运算，数量操作及字符串解析中。

## 变量赋值（创建变量）

变量赋值时，变量名必须遵守下面的规则。

- 字母、数字和下划线字符组成。
- 第一个字符必须时一个字母或一个下划线，不能数数字。
- 不允许出现空格和标点符号。

### 变量赋值形式

变量赋值有以下形式：

- 使用赋值操作符（`=`）赋值

  ```shell
  a=879
  echo "The value of \"a\" is $a."
  ```

  > 注意： `=` 根据使用场景即可作为赋值操作符，也可作为比较操作符。

- 使用 `let` 进行赋值（不推荐，推荐使用算术表达式作为变量值）

  ```shell
  let a=16+5 # 推荐使用 a=$((16 + 5))
  echo "The value of \"a\" is now $a."
  ```

- 在 `for` 循环中赋值（隐式赋值）

  ```shell
  echo -n "Values of \"a\" in the loop are: "
  for a in 7 8 9 11; do
      echo -n "$a "
  done
  echo
  ```

- 在 `read` 表达式中赋值

  ```shell
  read -rp "Enter \"a\" " a
  echo "The value of \"a\" is now $a."
  ```

### 变量值的形式

变量值可以有以下形式：

- 值为字符串

  ```shell
  a=23
  ```

- 如果变量值包含空格，必须放在双引号里面

  ```shell
  b="a string"
  ```

- 变量值可以引用其他变量的值

  ```shell
  c=$a
  ```

- 变量值可以使用转义字符

  ```shell
  d="\ta string\n"
  ```

- 变量值可以是命令替换（命令执行的结果）

  ```shell
  e=$(ls -l)
  arch=$(uname -m)
  ```

- 变量值可以是算术运算的结果

  ```shell
  f=$((5 * 7))
  ```

## 变量替换（引用变量）

变量名是其所指向值的一个占位符（placeholder）。引用变量值的过程我们称之为变量替换（variable substitution）。

引用变量的时候，直接在变量名前面加上 `$` 就可以了，例如如果变量名是 `variable1`，那么 `$variable1` 就是对变量值的引用。

### 引用变量形式

- 
