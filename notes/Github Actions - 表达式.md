---
title: Github Actions - 表达式
tags: [CICD/Github Actions]
created: 2022-09-28T09:29:17.430Z
modified: 2022-09-29T03:14:53.440Z
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

```yaml
env:
  myNull: ${{ null }}
  myBoolean: ${{ false }}
  myIntegerNumber: ${{ 711 }}
  myFloatNumber: ${{ -9.2 }}
  myHexNumber: ${{ 0xff }}
  myExponentialNumber: ${{ -2.99e-2 }}
  myString: Mona the Octocat
  myStringInBraces: ${{ 'It''s open source!' }}
```

## 操作符

| 操作符 | 说明     |
| ------ | -------- |
| ( )    | 逻辑分组 |
| [ ]    | 索引     |
| .      | 取消引用 |
| !      | 非       |
| <      | 小于     |
| <=     | 小于等于 |
| >      | 大于     |
| >=     | 大于等于 |
| ==     | 等于     |
| !=     | 不等于   |
| &&     | 和       |
| \|\|   | 或       |

- 如果类型不匹配，会将类型强制换换为数字。
  | 类型 | 转换后的结果 |
  | ------- | -------------------------------------------------------- |
  | Null | 0 |
  | Boolean | `true` 为 1；`false` 为 0 |
  | String | 符合 Json 数字格式转换为数字，否则为 `NaN`；空字符串为 0 |
  | Array | `NaN` |
  | Object | `NaN` |
- 一个 `NaN` 与另一个 `Nan` 比较不为 `true`。
- 在比较字符串会忽略大小写。
- 对象和数组只有是同一个实例时才会被认为是相等的。

## 函数

Github 提供了一些可在表达中使用的内置函数，一些函数将值转换为字符串以执行比较：

| 类型    | 转换后的结果                   |
| ------- | ------------------------------ |
| Null    | ''                             |
| Boolean | 'true' 或 'false'              |
| Number  | 十进制格式，大数转换为指数形式 |
| Array   | 不会转换                       |
| Object  | 不会转换                       |

### contains

`contains( search, item )` - 判断 `search` 是否包含 `item`

- 如果 `search` 是数组，`item` 是数组中的元素则返回 `true`。
- 如果 `search` 是字符串，`item` 是 `search` 子字符串则返回 `true`。
- 不区分大小写。
- 会将可以转换的值转换为字符串进行比较。

```javascript
// 返回 true
contains('Hello world', 'llo')

// 如果 Github Issue 中包含 bug 标签，则返回 true
contains(github.event.issue.labels.*.name, 'bug')

// 如果 Github 事件包含 push 或 pull_request 则返回 true
// 等同于 github.event_name == "push" || github.event_name == "pull_request"
contains(fromJson('["push", "pull_request"]'), github.event_name)
```

### startsWith

`startsWith( searchString, searchValue )` - 判断 `searchString` 是否以 `searchValue` 开头

- 不区分大小写。
- 会将可以转换的值转换为字符串进行比较。

```javascript
// 返回 true
startsWith('Hello world', 'He') 
```

### endsWith

endsWith( searchString, searchValue ) - 判断 `searchString` 是否以 `searchValue` 结尾

- 不区分大小写。
- 会将可以转换的值转换为字符串进行比较。

```javascript
// 返回 true
endsWith('Hello world', 'ld')
```

### format

`format( string, replaceValue0, replaceValue1, ..., replaceValue` - 格式化字符串

- 使用花括号转义花括号

```javascript
// 返回 'Hello Mona the Octocat'
format('Hello {0} {1} {2}', 'Mona', 'the', 'Octocat')

// 返回 '{Hello Mona the Octocat!}'
format('{{Hello {0} {1} {2}!}}', 'Mona', 'the', 'Octocat')
```

### join

`join( array, optionalSeparator )` - 数组转字符串

- 如果提供 `optionalSeparator`，则会插入到连接的值之间，否则使用默认分隔符 `,`。
- 会将可以转换的值转换为字符串。

```javascript
// 可能返回 'bug, help wanted'
join(github.event.issue.labels.*.name, ', ')
```

### toJSON

`toJSON(value)` - 格式化为可读的 Json 格式

```javascript
// 可能返回 { "status": "Success" }
toJSON(job)
```

### fromJSON

`fromJSON(value)` - 