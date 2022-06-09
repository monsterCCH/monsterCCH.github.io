---
layout: wiki
title: GDB
categories: [debug,gdb]
description: debug Wiki
keywords: gdb,debug
---

## 打印 char 类型数组

```
/*!
 * @array 数组变量
 * @length 数组长度
 */
p *array@length
```

## 打印完整字符串

```
显示默认打印长度
show print elements
设置不限制长度
set print element 0
```

## dump 程序内存

```shell
cat /proc/pid/maps

address           perms offset  dev   inode       pathname
00400000-00452000 r-xp 00000000 08:02 173521      /usr/bin/dbus-daemon
00651000-00652000 r--p 00051000 08:02 173521      /usr/bin/dbus-daemon
00652000-00655000 rw-p 00052000 08:02 173521      /usr/bin/dbus-daemon
00e03000-00e24000 rw-p 00000000 00:00 0           [heap]
00e24000-011f7000 rw-p 00000000 00:00 0           [heap]
...
35b1800000-35b1820000 r-xp 00000000 08:02 135522  /usr/lib64/ld-2.15.so
35b1a1f000-35b1a20000 r--p 0001f000 08:02 135522  /usr/lib64/ld-2.15.so
35b1a20000-35b1a21000 rw-p 00020000 08:02 135522  /usr/lib64/ld-2.15.so
35b1a21000-35b1a22000 rw-p 00000000 00:00 0
35b1c00000-35b1dac000 r-xp 00000000 08:02 135870  /usr/lib64/libc-2.15.so
35b1dac000-35b1fac000 ---p 001ac000 08:02 135870  /usr/lib64/libc-2.15.so
35b1fac000-35b1fb0000 r--p 001ac000 08:02 135870  /usr/lib64/libc-2.15.so
35b1fb0000-35b1fb2000 rw-p 001b0000 08:02 135870  /usr/lib64/libc-2.15.so
...
f2c6ff8c000-7f2c7078c000 rw-p 00000000 00:00 0    [stack:986]
...
7fffb2c0d000-7fffb2c2e000 rw-p 00000000 00:00 0   [stack]
7fffb2d48000-7fffb2d49000 r-xp 00000000 00:00 0   [vdso]


gdb attach pid

dump memory /tmp/sshd.dump 0x800da000 0x801c2000
```



## 反汇编调试

```
#设置反汇编代码格式
set disassembly-flavor intel

#查看反汇编代码格式
show disassembly-flavor

#反汇编命令，将内存中的机器码程序以指令助记符的形式显示出来
disas/disass/disassemble
eg：disas main 显示main函数对应

#查看某个行的相关信息
info line 函数名/*内存地址

ps: 如果参数为函数名，显示该函数在源文件中的行号及在内存中的起始、结束地址
eg:Line 49 of "/tmp/tmp.jZQLmWixjt/cmake-build-debug/_deps/googletest-src/googletest/src/gtest_main.cc" starts at address 0x5555555f34f5 <main(int, char**)> and ends at 0x5555555f3508 <main(int, char**)+19>.

ps: 如果参数为*内存地址，则显示该内存地址的指令对应的语句所在的函数在源文件中的行号及在内存中的起始地址和结束地址
eg: ine 53 of "/tmp/tmp.jZQLmWixjt/gtest/module/cplusplus_basics/tests/cpp_basics_test.cpp" starts at address 0x555555577f0c <cpp_basics_test_static_array_Test::TestBody()> and ends at 0x555555577f1c <cpp_basics_test_static_array_Test::TestBody()+16>.

#单步调试
ni 等价于 n，针对于汇编指令
si 等价于 s，针对于汇编指令

#设置地址断点
b *[address]

#设置程序中断后显示的数据和格式
display 

eg: display /i $pc 以十六进行显示当前汇编指令

#取消先前的 display 设置
undisplay

#查看内存单元
x /[size][format][unit] [address]

ps：
       size 表示要显示的内存单元的个数

       format 表示显示方式, 可取如下值：

       x 按十六进制格式显示变量。
       d 按十进制格式显示变量。
       u 按十进制格式显示无符号整型。
       o 按八进制格式显示变量。
       t 按二进制格式显示变量。
       a 按十六进制格式显示变量。
       i 指令地址格式。
       c 按字符格式显示变量。
       s 按字符串格式显示变量。
       f 按浮点数格式显示变量。

       unit 表示一个地址单元的长度，其中：

       b表示单字节，
       h表示双字节，
       w表示四字节，
       g表示八字节
```


