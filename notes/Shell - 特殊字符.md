---
title: Shell - 特殊字符
tags: [Shell]
created: 2022-10-09T02:12:45.487Z
modified: 2022-10-09T02:21:05.367Z
---

# Shell - 特殊字符

## 特殊变量

### `$0 $1 $2 $3 ...` - 命令行参数

- `$0` 代表脚本名称。

- `$1` 代表第一个参数，`$2` 代表第二个，`$3` 代表第三个，以此类推。在 `$9` 之后的参数必须被包括在大括号中，如 `${10}`、`${11}`。

### `$`

## `#` - 注释符

如果一行脚本开头是 `#`（除了 `#!`），那么代表这一行是注释，不会被执行。

```shell
# 这是一行注释
```

- 注释也可能会在一行命令结束之后出现。

  ```shell
  echo "A comment will follow" # 这可以写注释，注意 # 之前有空格
  ```

- 注释也可以在一行开头的空白符（whitespace）之后。

  ```shell
      # 这个注释前面有一个制表符（tab）
  ```

- 注释甚至可以嵌入到管道命令（pipe）之中。

  ```shell
  mapfile inital < <(
      sed -e '/#/d' $startfile | tr -d '\n' |
          # 删除所有带 '#' 注释符号的行
          sed -e 's/\./\. /g' -e 's/_/_ /g'
      # 删除换行符并在元素之间插入空格
  )
  ```

- 命令不能写在同一行注释之后，因为没有任何方法可以结束注释（仅支持单行注释），为了让新命令能够正常执行需要另起一行写。

- 被引用会被转义的 `#` 不会被认为是注释。

  ```shell
  echo "The # here does not begin a comment."
  echo 'The # here does not begin a comment.'
  echo The \# here does not begin a comment.
  echo The # here begins a comment.
  ```

- 某些参数替换式和常量表达式中的 `#` 不会被认为是注释。

  ```shell
  echo ${PATH#*:}
  echo $(( 2#101011 ))
  ```

- 某些模式匹配操作同样使用了 `#`。例如 `${#var}`、`${var#Pattern}`、`${var##Pattern}` 等。

## `;` - 命令分隔符

允许在同一行放置两条或更多的命令。

```shell
echo hello; echo there

if [ -x "$filename" ]; then
    echo "File $filename exists."; cp $filename $filename.bak
else
    echo "File $filename not found."; touch $filename
fi; echo "File test complete."
```

- 有时候 `;` 需要被转义才能正常工作。

  ```shell
  find ~/ -name 'core*' -exec rm {} \;
  ```

## `;;` `;&` `;;&` - `case` 条件语句终止符

- `;;` - 第一个模式匹配之后不会尝试后续匹配。

  ```shell
  #!/usr/bin/env bash

  foo() {
      case $1 in
          3)
              echo "Level Three"
              ;;
          2)
              echo "Level Two"
              ;;
          1)
              echo "Level one"
              ;;
          *)
              echo "Level *"
              ;;
      esac
  }

  echo 1:
  foo 1
  echo 2:
  foo 2
  echo 3:
  foo 3

  exit 0
  ```

  执行结果如下：

  ```
  $ sh case_terminator.sh
  1:
  Level one
  2:
  Level Two
  3:
  Level Three
  ```

- `;&` - 第一个模式匹配之后会继续执行之后的子句中的命令（Bash 4+ 版本后支持）

  ```shell
  #!/usr/bin/env bash

  foo() {
      case $1 in
          3)
              echo "Level Three"
              ;&
          2)
              echo "Level Two"
              ;&
          1)
              echo "Level one"
              ;&
          *)
              echo "Level *"
              ;&
      esac
  }

  echo 1:
  foo 1
  echo 2:
  foo 2
  echo 3:
  foo 3

  exit 0
  ```

  执行结果如下：

  ```shell
  $ sh case_terminator.sh
  1:
  Level one
  Level *
  2:
  Level Two
  Level one
  Level *
  3:
  Level Three
  Level Two
  Level one
  Level *
  ```

- `;;&` - 第一个模式匹配成功后会继续测试之后的子句，执行全部匹配成功子句的命令（Bash 4+ 版本后支持）

  ```shell
  #!/usr/bin/env bash

  foo() {
      case $1 in
          *3*)
              echo "Level Three"
              ;;&
          *2*)
              echo "Level Two"
              ;;&
          *1*)
              echo "Level one"
              ;;&
          *)
              echo "Level *"
              ;;&
      esac
  }

  echo 12:
  foo 12
  echo 13:
  foo 13
  echo 23:
  foo 23
  echo 123:
  foo 123

  exit 0
  ```

  执行结果如下：

  ```shell
  $ sh case_terminator.sh
  12:
  Level Two
  Level one
  Level *
  13:
  Level Three
  Level one
  Level *
  23:
  Level Three
  Level Two
  Level *
  123:
  Level Three
  Level Two
  Level one
  Level *
  ```

## `.` - 句点命令、隐藏文件头、当前目录、句点匹配符

- 当 `.` 作为命令使用时等价于 `source` 命令，这是一个 Bash 的内建命令。

  ```shell
  . /etc/environment
  ```

- 当 `.` 为文件名的开头，则说明此文件时隐藏文件。

- 当 `.` 出现在目录中时，`.` 表示当前工作目录，`..` 表示上级目录。

  ```shell
  cd ..
  cp junk/* .
  ```

- 当 `.` 出现在正则表达式中，`.` 表示匹配任意单个字符。

## `"` - 部分引用

在字符串中保留大部分特殊字符。

## `'` - 全引用

在字符串中保留所有特殊字符。

## `,` - 逗号运算符

- `,` 将一系列算术表达式串联在一起。算术表达式依次被执行，但只返回最后一个表达式的值。

  ```shell
  t2=$((a = 9, 15 / 3))
  # a 被赋值为 9，t2 被赋值为 15 / 3
  ```

- `,` 也可以用来连接字符串。

  ```shell
  for file in /usr/{,local/}bin/*calc; do
  # 在 /usr/bin 与 /usr/local/bin/ 目录中找到所有以 'calc' 结尾的可执行文件
      if [ -x "$file" ]; then
          echo "$file"
      fi
  done
  ```

- `,` 在参数替换中进行首字符小写转换，`,,` 则表示全部小写。

  ```shell
  a=ATest
  echo ${a,}  # aTest
  echo ${a,,} # atest
  ```

## `\` - 转义符

转义某字符的标志。

`\X` 转义了字符 X。双引号 `"X"` 内的 X 与单引号内的 `'X'` 具有相同效果。转义符也可以用来转义 `"` 与 `'`，使他们表达其字面含义。

## `/` - 文件路径分隔符

- 起分割路径的作用。例如（/home/sysadmin/projects/Makefile）

- 在算术运算中充当除法运算符。


## ``