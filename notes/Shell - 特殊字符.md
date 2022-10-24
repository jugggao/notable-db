---
title: Shell - 特殊字符
tags: [Shell]
created: 2022-10-09T02:12:45.487Z
modified: 2022-10-09T02:21:05.367Z
---

# Shell - 特殊字符

## `#` - 注释符

注释符。如果一行脚本开头是 `#`（除了 `#!`），那么代表这一行是注释，不会被执行。

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

- 被引用会被转义的 `#` 不会被认为是注释

  ```shell
  echo "The # here does not begin a comment."
  echo 'The # here does not begin a comment.'
  echo The \# here does not begin a comment.
  echo The # here begins a comment.
  ```

- 某些变量替换
