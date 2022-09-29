---
title: Github Actions - 表达式
tags: [CICD/Github Actions]
created: 2022-09-28T09:29:17.430Z
modified: 2022-09-28T09:49:20.651Z
---

# Github Actions - 表达式

Github Actions 表达式常用于条件判断与设置环境变量中：

```yaml
steps:
  - uses: actions/hello-world-javascript-action@v1.1
    if: ${{ <expression> }

env:
  MY_ENV_VAR: ${{ <expression> }}
```

## 字面量

| 数据类型 | 字面量                                                                                                                                 |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| boolean  | true false                                                                                                                             |
| null     | null                                                                                                                                   |
| number   | JSON 支持的任意数字格式                                                                                                                |
| string   | 不必将字符串放在 `${{ }}` 内；但是如果字符串放在 `${{ }}` 内，则必须使用单引号包裹字符串，使用单引号对单引号进行转义，使用双引号会报错 |

