---
layout: post
title: linux perf 程序性能分析
categories: linux, performance
description: 介绍 linux 下 perf 的使用分析
keywords: perf
---

## what's perf?

perf 是 linux 下的性能分析工具。

Linux的性能计数器是一个新的基于内核的子系统，它为所有性能分析提供了一个框架。 它涵盖硬件级别(CPU/PMU，性能监控单元)特性和软件特性(软件计数器，跟踪点)。

## how to use?

### perf list

显示可以在 perf 命令中使用 -e 选项选择的各种符号事件类型。

```shell
List of pre-defined events (to be used in -e):

  branch-instructions OR branches                    [Hardware event]
  branch-misses                                      [Hardware event]
  cache-misses                                       [Hardware event]
  cache-references                                   [Hardware event]
  cpu-cycles OR cycles                               [Hardware event]
  instructions                                       [Hardware event]
  stalled-cycles-backend OR idle-cycles-backend      [Hardware event]
  stalled-cycles-frontend OR idle-cycles-frontend    [Hardware event]

  alignment-faults                                   [Software event]
  bpf-output                                         [Software event]
  context-switches OR cs                             [Software event]
  cpu-clock                                          [Software event]
  cpu-migrations OR migrations                       [Software event]
  dummy                                              [Software event]
  emulation-faults                                   [Software event]
  major-faults                                       [Software event]
  minor-faults                                       [Software event]
  page-faults OR faults                              [Software event]
  task-clock                                         [Software event]
...

```

### perf top

实时生成展示系统性能统计信息

#### 语法

```shell
perf top [-e <EVENT> | --event=EVENT] [<option>]
```

#### 可选参数

```shell
-e：指定性能事件

-a, --all_cpus：显示在所有CPU上的性能统计信息

-c: 事件采样周期 一般建议为 997

-C：显示在指定CPU上的性能统计信息, 多个 CPU 使用 , 获者 - 分割, eg: 0,1  or 0-2

-d: 输出刷新间隔时间, 单位 s 

-p：指定进程PID

-t：指定线程TID

-K：隐藏内核统计信息

-U：隐藏用户空间的统计信息

-s：指定待解析的符号信息

‘‐G’ or‘‐‐call‐graph’ <output_type,min_percent,call_order>

graph: 使用调用树，将每条调用路径进一步折叠。这种显示方式更加直观。

每条调用路径的采样率为绝对值。也就是该条路径占整个采样域的比率。

fractal

默认选项。类似与 graph，但是每条路径前的采样率为相对值。

flat

不折叠各条调用

选项 call_order 用以设定调用图谱的显示顺序，该选项有 2个取值，分别是

callee 与caller。

将该选项设为callee 时，perf按照被调用的顺序显示调用图谱，上层函数被下层函数所调用。

该选项被设为caller 时，按照调用顺序显示调用图谱，即上层函数调用了下层函数路径，也不显示每条调用路径的采样率
```

### perf stat

运行命令采集性能统计信息

#### 语法

```shell
perf stat [-e <EVENT> | --event=EVENT] [-a] <command>
perf stat [-e <EVENT> | --event=EVENT] [-a] — <command> [<options>]
perf stat [-e <EVENT> | --event=EVENT] [-a] record [-o file] — <command> [<options>]
perf stat report [-i file]
```

#### 输出说明

```shell
task‐clock 事件表示目标任务真正占用处理器的时间，单位是毫秒。也称任务执行时间

context-switches 是系统发生上下文切换的次数

CPU-migrations 是任务从一个处理器迁往另外一个处理器的次数

page-faults 是内核发生缺页的次数

cycles 是程序消耗的处理器周期数

instructions 是指命令执行期间产生的处理器指令数

branches 是指程序在执行期间遇到的分支指令数。

branch‐misses 是预测错误的分支指令数。

XXX seconds time elapsed系程序持续时间
```

#### 可选参数

```shell
-e：选择性能事件

-i：禁止子任务继承父任务的性能计数器。

-r：重复执行 n 次目标程序，并给出性能指标在n 次执行中的变化范围。

-n：仅输出目标程序的执行时间，而不开启任何性能计数器。

-a：指定全部cpu

-C：指定某个cpu

-A：将给出每个处理器上相应的信息。

-p：指定待分析的进程id

-t：指定待分析的线程id
```

### perf record

记录一段时间内的性能统计信息

#### 语法

```shell
perf record [-e <EVENT> | --event=EVENT] [-a] <command>
perf record [-e <EVENT> | --event=EVENT] [-a] — <command> [<options>]
```

#### 可选参数

```shell
 -e：选择性能事件

 -p：待分析进程的id

 -t：待分析线程的id

 -a：分析整个系统的性能

 -C：只采集指定CPU数据

 -c：事件的采样周期

 -o：指定输出文件，默认为perf.data

 -A：以append的方式写输出文件

 -f：以OverWrite的方式写输出文件

 -g：记录函数间的调用关系
```

### perf report

读取perf record生成的数据文件，并显示分析数据

#### 语法

```shell
perf report [-i <file> | --input=file]
```

#### 可选参数

```shell
-i：输入的数据文件

-v：显示每个符号的地址

-d <dos>：只显示指定dos的符号

-C：只显示指定comm的信息（Comm. 触发事件的进程名）

-S：只考虑指定符号

-U：只显示已解析的符号

-g[type,min,order]：显示调用关系，具体等同于perf top命令中的-g

-c：只显示指定cpu采样信息

-M：以指定汇编指令风格显示

–source：以汇编和source的形式进行显示

-p<regex>：用指定正则表达式过滤调用函数
```