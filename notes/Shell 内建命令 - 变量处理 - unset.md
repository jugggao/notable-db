---
title: Shell 内建命令 - 变量处理 - unset
tags: [Shell/Command]
created: 2022-10-24T08:10:24.982Z
modified: 2022-10-24T08:41:12.729Z
---

# Shell 内建命令 - 变量处理 - `unset`

## 1. 用法

### 1.1. 语法

```shell
unset [-fv] [name ...]
```

### 1.2. 作用

- 删除一个到多个 Shell 变量（不包括只读变量）。
- 删除一个到多个 Shell 函数。
- 删除具有引用属性的变量名。

### 1.3. 选项

```
-f          仅删除函数
-v          仅删除变量（不包括只读变量）
-n          删除具有引用属性的变量名
```

## 2. 用例

### 2.1. `unset -v` - 删除变量

```shell
#!/usr/bin/env bash

variable=hello
echo "variable = $variable"

unset -v variable # 删除变量

echo "(unset) variable = $variable" # $variable 为 null

if [ -z "$variable" ]; then # 判断字符串是否为 0
    echo "\$variable has zero length."
fi

exit 0
```

### 2.2. `unset -f` - 删除函数

```shell
#!/usr/bin/env bash

show_result() {
    echo "Last Command Return: $?"
}

unset -f show_result
show_result

exit 0
```

输出结果如下：

```shell
$ sh unset_func.sh
unset_func.sh: line 9: show_result: command not found
```

可以看到函数已被，不可使用。

### 2.3. `unset` - 不指定选项时优先删除变量，如果失败则删除同名函数

```shell
#!/usr/bin/env bash

hello="Hello Bash"

hello() {
    echo "Hello"
}

unset hello

declare -p hello # 变量 hello 已经被删除
declare -F hello # 函数 hello 存在

exit 0
```

输出结果如下：

```shell
$ sh unset.sh
unset.sh: line 11: declare: hello: not found
hello
```

### 2.4. `unset -n` 删除引用属性的变量

```shell
#!/usr/bin/env bash

declare -i a=3
declare -n b=a # 定义引用变量
declare -n c=a # 定义引用变量
declare -p b   # 查看变量属性，显示 declare -n b="a"
declare -p c   # 查看变量属性，显示 declare -n c="a"

echo "\$b = $b"             # 3
echo "\$b references ${!b}" # a
echo "\$c = $b"             # 3
echo "\$c references ${!b}" # a

unset -n b      # 执行 -n 删除引用变量
declare -p b    # 引用变量 b 已经被删除
declare -p a    # 被引用变量 a 未被删除
echo "\$a = $a" # 3

unset c         # 不执行 -n 选项
declare -p c    # 引用变量 c 未被删除，显示 declare -n c="a"
declare -p a    # 引用变量 a 被删除
echo "\$c = $c" # 但是变量 a 被删除，c 变量为 null

exit 0
```

## 3. 参考

- `https://linux.die.net/abs-guide/internal.html#EX37`
- `https://wangchujiang.com/linux-command/c/unset.html`
