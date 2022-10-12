---
title: Shell 内建命令 - 变量处理 - let
title_custom: true
tags: [Shell/Command]
created: 2022-10-12T02:21:00.002Z
modified: 2022-10-12T02:34:25.991Z
---

# Shell 内建命令 - 变量处理 - `let`

## 1. 作用

对变量执行算数运算。大多数情况下，它作为 `expr` 的简单版本。

## 2. 用法

### 2.1. 语法

```shell
let arg [arg ...]
```

### 2.2. 运算符

优先级递减。

```
id++, id--			变量赋值后递增，递减
++id, --id			变量赋值前递增，递减
-, +				负，正
!, ~				逻辑否，按位取反
**					幂运算
*, /, %				乘，除，取余
+, -				加，减
<<, >>				按位左移，按位右移
<=, >=, <, >		比较
==, !=				等于，不等于
&					按位与
^					按位异或
|					按位或
&&					逻辑与
||					逻辑或
expr ? expr : expr	条件运算符（三元运算符）
=, *=, /=, %=,
+=, -=, <<=, >>=,
&=, ^=, |=          赋值
```

## 3. 用例

### 3.1. 简单算术

```shell
#!/usr/bin/env bash

# let "a = 11" "a = a + 5"
((a = 11, a = a + 5))
echo "11 + 5 = $a"

# let "a <<= 3"
((a <<= 3))                                     # 等同于 ((a = a << 3))
echo "\"\$a\" (=16) left-shifted 3 places = $a" # 128

# let "a /= 4"
((a /= 4))          # 等同于 ((a = a / 4))
echo "128 / 4 = $a" # 32

# let "a -= 5"
((a -= 5))         # 等同于 ((a = a - 5))
echo "32 - 5 = $a" # 27

# let "a *= 10"
((a *= 10))         # 等同于 ((a = a * 10))
echo "27 * 10 = $a" # 270

# let "a %= 8"
((a %= 8))               # 等同于 ((a = a % 8))
echo "270 modulo 8 = $a" # 6

exit 0
```

## 4. 注意

### 4.1. 使用 `(( expr ))` 而不是 `let expr`

有问题的代码：

```shell
let a++
```

正确的代码

```shell
(( a++ ))
```

`(( .. ))` 整数扩展符以与 `let` 相同的方式求表达式值，但是它不受 glob 展开的影响，因此不需要额外的引号或者转义。

- https://www.shellcheck.net/wiki/SC2219
- https://wiki.bash-hackers.org/commands/builtin/let
