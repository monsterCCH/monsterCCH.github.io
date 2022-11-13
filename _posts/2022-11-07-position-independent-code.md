---
layout: post
title: 深入理解 Linux 位置无关代码 IPC
categories: ipc, linux
description: 深入理解 Linux 位置无关代码 IPC
keywords: ipc, linux
---

## 概念
可以加载而无需重定位的代码称为位置无关代码（Position-Independent Code, IPC）,指代码无论被加载到哪个地址上都可以正常执行。gcc 选项中添加-fPIC会产生相关代码。PIC 用于共享库，允许库代码位于内存中的任何位置。

在计算机中，位置无关的代码( PIC ) 或位置无关的可执行文件( PIE ) 是放置在主存储器某处的机器代码体，无论其绝对地址如何，都能正常执行。PIC 通常用于共享库，因此可以将相同的库代码加载到每个程序地址空间中的某个位置，该位置不会与内存的任何其他用途（例如，其他共享库）重叠。PIC 也用于缺乏MMU的旧计算机系统，因此操作系统即使在MMU-less 系统 的单个地址空间内，应用程序也可以彼此远离。

与位置无关的代码可以在任何内存地址处执行而无需修改。这与绝对代码不同，绝对代码必须加载到特定位置才能正常运行，而加载时可定位(LTL) 代码在加载时可定位(LTL) 代码中，链接器或程序加载器会在执行之前修改程序，使其只能从特定内存中运行地点。生成与位置无关的代码通常是编译器的默认行为，但它们可能会对某些语言功能的使用施加限制，例如不允许使用绝对地址（与位置无关的代码必须使用相对寻址））。直接引用特定内存地址的指令有时执行得更快，而用等效的相对寻址指令替换它们可能会导致执行速度稍慢，尽管现代处理器几乎可以忽略不计的差异。

## 编译指定生成ipc
gcc 添加 -fPIC

cmake 添加 set_target_properties(<project-name> PROPERTIES POSITION_INDEPENDENT_CODE 1)

* fPIC与-fpic都是在编译时加入的选项，用于生成位置无关的代码(Position-Independent-Code)。这两个选项都是可以使代码在加载到内存时使用相对地址，所有对固定地址的访问都通过全局偏移表(GOT)来实现。-fPIC和-fpic最大的区别在于是否对GOT的大小有限制。-fPIC对GOT表大小无限制，所以如果在不确定的情况下，使用-fPIC是更好的选择。

* fPIE与-fpie是等价的。这个选项与-fPIC/-fpic大致相同，不同点在于：-fPIC用于生成动态库，-fPIE用与生成可执行文件。再说得直白一点：-fPIE用来生成位置无关的可执行代码。


## 参考资料
[Position Independent Code (PIC) in shared libraries](https://blog.csdn.net/astrotycoon/article/details/8456453)

[深入理解 Linux 位置无关代码 PIC](https://blog.csdn.net/feelabclihu/article/details/108289461)
