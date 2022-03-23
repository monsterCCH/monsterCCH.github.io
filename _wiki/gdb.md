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

