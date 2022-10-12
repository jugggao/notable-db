---
title: Shell 内建命令 - 变量处理 - let
title_custom: true
tags: [Shell/Command]
created: 2022-10-12T02:21:00.002Z
modified: 2022-10-12T02:29:07.074Z
---

# Shell 内建命令 - 变量处理 - `let`

## 作用

对变量执行算数运算。大多数情况下，它作为 `expr` 的简单版本。

## 用法

### 语法

```shell
let arg [arg ...]
```

### 运算符

优先级递减。

```
id++, id--		变量赋值后递增，递减
++id, --id		变量赋值前递增，递减
-, +			
```


