---
title: Github Actions - 表达式
tags: [CICD/Github Actions]
created: 2022-09-28T09:29:17.430Z
modified: 2022-09-29T07:56:10.176Z
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

## 1. 字面量

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

## 2. 操作符

| 操作符 | 说明     |
| ----- | ------- |
| ( )   | 逻辑分组 |
| [ ]   | 索引     |
| .     | 引用     |
| !     | 非      |
| <     | 小于     |
| <=    | 小于等于 |
| >     | 大于     |
| >=    | 大于等于 |
| ==    | 等于     |
| !=    | 不等于   |
| &&    | 和      |
| \|\|  | 或      |

- 如果类型不匹配，会将类型强制换换为数字。
  | 类型 | 转换后的结果 |
  | ------- | ------- |
  | Null | 0 |
  | Boolean | `true` 为 1；`false` 为 0 |
  | String | 符合 Json 数字格式转换为数字，否则为 `NaN`；空字符串为 0 |
  | Array | `NaN` |
  | Object | `NaN` |
- 一个 `NaN` 与另一个 `Nan` 比较不为 `true`。
- 在比较字符串会忽略大小写。
- 对象和数组只有是同一个实例时才会被认为是相等的。

## 3. 函数

Github 提供了一些可在表达中使用的内置函数，一些函数将值转换为字符串以执行比较：

| 类型    | 转换后的结果                   |
| ------- | ------------------------------ |
| Null    | ''                             |
| Boolean | 'true' 或 'false'              |
| Number  | 十进制格式，大数转换为指数形式 |
| Array   | 不会转换                       |
| Object  | 不会转换                       |

### 3.1. contains

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

### 3.2. startsWith

`startsWith( searchString, searchValue )` - 判断 `searchString` 是否以 `searchValue` 开头

- 不区分大小写。
- 会将可以转换的值转换为字符串进行比较。

```javascript
// 返回 true
startsWith('Hello world', 'He');
```

### 3.3. endsWith

endsWith( searchString, searchValue ) - 判断 `searchString` 是否以 `searchValue` 结尾

- 不区分大小写。
- 会将可以转换的值转换为字符串进行比较。

```javascript
// 返回 true
endsWith('Hello world', 'ld');
```

### 3.4. format

`format( string, replaceValue0, replaceValue1, ..., replaceValue` - 格式化字符串

- 使用花括号转义花括号

```javascript
// 返回 'Hello Mona the Octocat'
format('Hello {0} {1} {2}', 'Mona', 'the', 'Octocat');

// 返回 '{Hello Mona the Octocat!}'
format('{{Hello {0} {1} {2}!}}', 'Mona', 'the', 'Octocat');
```

### 3.5. join

`join( array, optionalSeparator )` - 数组转字符串

- 如果提供 `optionalSeparator`，则会插入到连接的值之间，否则使用默认分隔符 `,`。
- 会将可以转换的值转换为字符串。

```javascript
// 可能返回 'bug, help wanted'
join(github.event.issue.labels.*.name, ', ')
```

### 3.6. toJSON

`toJSON(value)` - 格式化为可读的 Json 格式

```javascript
// 可能返回 { "status": "Success" }
toJSON(job);
```

### 3.7. fromJSON

`fromJSON(value)` - 返回 Json 对象或者 Json 数据类型的值

返回 Json 对象：

```yaml
name: build
on: push
jobs:
  job1:
    runs-on: ubuntu-latest
    outputs:
      matrix: ${{ steps.set-matrix.outputs.matrix }}
    steps:
      - id: set-matrix
        run: echo "::set-output name=matrix::{\"include\":[{\"project\":\"foo\",\"config\":\"Debug\"},{\"project\":\"bar\",\"config\":\"Release\"}]}"
  job2:
    needs: job1
    runs-on: ubuntu-latest
    strategy:
      matrix: ${{ fromJSON(needs.job1.outputs.matrix) }}
    steps:
      - run: build
```

将环境变量转换为 Json 数据类型的值：

```yaml
# 将环境变量中的字符串转换为布尔值或数值
name: print
on: push
env:
  continue: true
  time: 3
jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
      - continue-on-error: ${{ fromJSON(env.continue) }}
        timeout-minutes: ${{ fromJSON(env.time) }}
        run: echo ...
```

### 3.8. hashFiles

`hashFiles(path)` - 获取文件哈希值

- 如果 `path` 不匹配任何文件，则返回空字符串

```javascript
// 单文件
hashFiles('**/package-lock.json');

// 多文件
hashFiles('**/package-lock.json', '**/Gemfile.lock');
```

## 4. 状态检测函数

如果没有任何状态检测函数，则使用 `success()` 检测状态。

### 4.1. success

当前面的步骤没有错误或取消时返回 `true`。

```yaml
steps:
  ...
  - name: The job has succeeded
    if: ${{ success() }}
```

### 4.2. always

始终执行此步骤，并返回 `true`，即使取消也如此。但是当出现严重错误阻止任务进行时，此任务或步骤将不执行（例如获取源失败）。

```yaml
if: ${{ always() }}
```

### 4.3. cancelled

当工作流取消时返回 `true`。

```yaml
if: ${{ cancelled }}
```

### 4.4. failure

当前面的步骤失败时返回 `true`。如果有依赖任务，则任何父任务失败时也返回 `true`。

```yaml
steps:
  ...
  - name: The job has failed
    if: ${{ failure() }}
```

```yaml
steps:
  ...
  - name: Failing step
    id: demo
    run: exit 1
  - name: The demo step has failed
    if: ${{ failure() && steps.demo.conclusion == 'failure' }}
```

## 5. 对象过滤

可以使用 `*` 来过滤集合中的元素。

使用 `*` 过滤 `fruits` 数组：

```json
[
  { "name": "apple", "quantity": 1 },
  { "name": "orange", "quantity": 2 },
  { "name": "pear", "quantity": 1 }
]
```

`fruits.*.name` 将返回 `[ "apple", "orange", "pear" ]`。

使用 `*` 来过滤 `vegetables` 对象：

```json
{
  "scallions": {
    "colors": ["green", "white", "red"],
    "ediblePortions": ["roots", "stalks"]
  },
  "beets": {
    "colors": ["purple", "red", "gold", "white", "pink"],
    "ediblePortions": ["roots", "stems", "leaves"]
  },
  "artichokes": {
    "colors": ["green", "purple", "red", "black"],
    "ediblePortions": ["hearts", "stems", "leaves"]
  }
}
```

`vegetables.*.ediblePortions` 将返回：

```
[
  ["roots", "stalks"],
  ["hearts", "stems", "leaves"],
  ["roots", "stems", "leaves"],
]
```

> 由于对象是无序的，因此过滤出来的数据不能保证其顺序。

