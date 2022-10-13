---
title: Shell 内建命令 - 变量处理 - eval
tags: [Shell/Command]
created: 2022-10-12T06:03:26.460Z
modified: 2022-10-12T08:38:15.363Z
---

# Shell 内建命令 - 变量处理 - `eval`

## 1. 用法

### 1.1. 语法

```shell
eval [arg ...]
```

### 1.2. 作用

`eval` 获取其参数，将它们用空格连接起来，其中包含的任何变量或表达式都会被展开，然后再当前执行环境中将生成的字符串作为 Bash 代码执行。对于从命令行或脚本中生成代码很有用。

同样， `eval` 命令适用于那些一次扫描无法实现其功能的变量。

`eval` 在 Bash 中的工作方式与大多数其他具有 `eval` 功能的语言基本相同。

## 2. 用例

### 2.1. 展开变量后再执行命令

`eval` 先展开参数中的变量或表达式，然后再执行此命令。

```shell
$ pipe="|"
# 直接执行 ls $pipe wc -l 会报错
# 因此需要通过 eval 将 ls $pipe wc -l 中的变量展开，然后再执行此命令
$ eval ls $pipe wc -l
29
```

案例：输出命令行参数

```shell
#!/usr/bin/env bash

# 调用脚本时跟一些命令行参数
# 例如：sh echo-params.sh first second third fourth fifth

params=$# # 命令行参数数量
param=1   # 从第一个命令行参数开始

while [ "$param" -le "$params" ]; do
    echo -n "Command line parameter "
    echo -n \$$param # 仅显示变量名，例如 $1, $2, $3 等
                     # \$ 转义第一个 $，所以仅显示字面量
    echo -n " = "
    eval echo \$$param # 显示变量的值
                       # 'eval' 会强制展开变量的值然后再执行
    ((param++))
done

exit $?
```

```shell
$ sh eval_echo_params.sh first second third fourth fifth
Command line parameter $1 = first
Command line parameter $2 = second
Command line parameter $3 = third
Command line parameter $4 = fourth
Command line parameter $5 = fifth
```

### 2.2. 展开表达式后再执行命令

```shell
$ ssh-agent -s
SSH_AUTH_SOCK=/tmp/ssh-lSoBMuHdwoP8/agent.2131386; export SSH_AUTH_SOCK;
SSH_AGENT_PID=2131387; export SSH_AGENT_PID;
echo Agent pid 2131387;

# 如果直接执行 ssh-agent -s，尽管运行成功了，但是输出结果中的环境变量并没有导出，上面的结果只是显示给用户看的
# 所以需要使用 eval 来执行 ssh-agent，使其输出的内容能够执行
$ eval $(ssh-agent -s)
Agent pid 2131483
```

```shell
#!/usr/bin/env bash

FILE="/tmp/test.mp4"
TMP="/tmp/tmp.mp4"

declare -i streams_stream_0_width
declare -i streams_stream_0_height

OUT_WIDTH=720
OUT_HEIGHT=480

# 获取视频的分辨率
eval "$(ffprobe -v error -of flat=s=_ -select_streams v:0 -show_entries stream=height,width $FILE)"
# 直接执行 ffprobe -v error -of flat=s=_ -select_streams v:0 -show_entries stream=height,width ${FILE} 会输出：
# streams_stream_0_width=1920
# streams_stream_0_height=1080
# 使用 'eval' 直接执行了输出的表达式
IN_WIDTH=$streams_stream_0_width
IN_HEIGHT=$streams_stream_0_height

# 获取实际分辨率和所需分辨率的差值
W_DIFF=$((OUT_WIDTH - IN_WIDTH))
H_DIFF=$((OUT_HEIGHT - IN_HEIGHT))

# 使用 ffmpeg 调整视频大小
CROP_SIDE="n"
if [ $W_DIFF -lt $H_DIFF ]; then
    SCALE="-2:$OUT_HEIGHT"
    CROP_SIDE="w"
else
    SCALE="$OUT_WIDTH:-2"
    CROP_SIDE="h"
fi
ffmpeg -i $FILE -vf scale=$SCALE $TMP

eval "$(ffprobe -v error -of flat=s=_ -select_streams v:0 -show_entries stream=height,width $TMP)"
IN_WIDTH=${streams_stream_0_width}
IN_HEIGHT=${streams_stream_0_height}

# 计算剪裁的尺寸
if [ "z${CROP_SIDE}" = "zh" ]; then
    DIFF=$((IN_HEIGHT - OUT_HEIGHT))
    CROP="in_w:in_h-$DIFF"
elif [ "z${CROP_SIDE}" = "zw" ]; then
    DIFF=$((IN_WIDTH - OUT_WIDTH))
    CROP="in_w-$DIFF:in_h"
fi

# 剪裁
ffmpeg -i $TMP -filter:v "crop=${CROP}" "$OUT"
```

## 将变量作为命令使用

```shell
$ process=nginx
$ show_process="eval ps ax | grep $process"
$ $show_process
2131281 pts/0    S+     0:00 grep nginx
2845689 ?        Ss     0:00 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
2845690 ?        S      0:00 nginx: worker process
2845691 ?        S      0:00 nginx: worker process
2845692 ?        S      0:00 nginx: worker process
2845693 ?        S      0:00 nginx: worker process
```



## 3. 参考

- https://wiki.bash-hackers.org/commands/builtin/eval
- https://www.cnblogs.com/klb561/p/10834592.html
- https://unix.stackexchange.com/questions/23111/what-is-the-eval-command-in-bash
- https://unix.stackexchange.com/questions/190431/convert-a-video-to-a-fixed-screen-size-by-cropping-and-resizing/192021#192021


