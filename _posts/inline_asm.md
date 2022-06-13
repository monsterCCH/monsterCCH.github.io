---
layout: post
title: __asm__ __volatile__ 内联汇编
categories: C, asm
description: 内联汇编介绍描述
keywords: C, asm
---

## 什么是内联汇编

内联汇编主要用来在 C/C++ 中嵌入汇编语言代码，它方便，快速，对系统编程有着举足轻重的作用。**可以操作C语言变量，比如可以输出值到C语言变量。这个特性使内联汇编成为汇编代码和调用其C程序之间的桥梁。**

## 语法格式

`__asm__　__volatile__("Instruction List" : Output : Input : Clobber/Modify);`

* \_\_asm_\_
  * GCC 关键字 asm 的宏定义，用来声明一个内联汇编表达式
* \_\_volatile_\_
  * GCC 关键字volatile 的宏定义，用来防止编译器根据优化选项`-O`进行优化，以产生非预期效果
* Instruction List
  * Instruction List 是汇编指令序列。它可以是空的，比如：__asm__ __volatile__(""); 或 __asm__ ("");都是完全合法的内联汇编表达式，只不过这两条语句没有什么意义。但并非所有Instruction List 为空的内联汇编表达式都是没有意义的，比如：__asm__ ("":::"memory");就非常有意义，它向GCC 声明：“内存作了改动”，GCC 在编译的时候，会将此因素考虑进去。 当在"Instruction List"中有多条指令的时候，可以在一对引号中列出全部指令，也可以将一条 或几条指令放在一对引号中，所有指令放在多对引号中。如果是前者，可以将每一条指令放在一行，如果要将多条指令放在一行，则必须用分号（；）或换行符（\n）将它们分开. 综上述：（1）每条指令都必须被双引号括起来 (2)两条指令必须用换行或分号分开。
  
  eg:
  ```c
  int disable_interrupts (void) 
  { 
          unsigned long old,temp; 
          __asm__ __volatile__("mrs %0, cpsr\n" 
                          "orr %1, %0, #0x80\n" 
                          "msr cpsr_c, %1" 
                          : "=r" (old), "=r" (temp) 
                          : 
                          : "memory"); 
          return (old & 0x80) == 0; 
  }
  ```

* Output
  * 用来指定当前内联汇编语句的输出
* Input
  * 用来指定当前内联汇编语句的输入，Output和Input中，格式为形如“constraint”(variable)的列表（逗号分隔)
* Clobber/Modify
  * 有时候，你想通知GCC当前内联汇编语句可能会对某些寄存器或内存进行修改，希望GCC在编译时能够将这一点考虑进去。那么你就可以在Clobber/Modify域声明这些寄存器或内存。这种情况一般发生在一个寄存器出现在"Instruction List"，但却不是由Input/Output操作表达式所指定的，也不是在一些Input/Output操作表达式使用"r"约束时由GCC 为其选择的，同时此寄存器被"Instruction List"中的指令修改，而这个寄存器只是供当前内联汇编临时使用的情况。

例如：

__asm__ ("mov R0, #0x34" : : : "R0");

寄存器R0出现在"Instruction List中"，并且被mov指令修改，但却未被任何Input/Output操作表达式指定，所以你需要在Clobber/Modify域指定"R0"，以让GCC知道这一点。

因为你在Input/Output操作表达式所指定的寄存器，或当你为一些Input/Output操作表达式使用"r"约束，让GCC为你选择一个寄存器时，GCC对这些寄存器是非常清楚的——它知道这些寄存器是被修改的，你根本不需要在Clobber/Modify域再声明它们。但除此之外， GCC对剩下的寄存器中哪些会被当前的内联汇编修改一无所知。所以如果你真的在当前内联汇编指令中修改了它们，那么就好在Clobber/Modify 中声明它们，让GCC针对这些寄存器做相应的处理。否则有可能会造成寄存器的不一致，从而造成程序执行错误。

如果一个内联汇编语句的Clobber/Modify域存在"memory"，那么GCC会保证在此内联汇编之前，如果某个内存的内容被装入了寄存器，那么在这个内联汇编之后，如果需要使用这个内存处的内容，就会直接到这个内存处重新读取，而不是使用被存放在寄存器中的拷贝。因为这个 时候寄存器中的拷贝已经很可能和内存处的内容不一致了。

这只是使用"memory"时，GCC会保证做到的一点，但这并不是全部。因为使用"memory"是向GCC声明内存发生了变化，而内存发生变化带来的影响并不止这一点 

