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

  * 指令中的操作数可以使用占位符引用C语言变量,操作数占位符最多10个,名称如下:%0,%1,…,%9。指令中使用占位符表示的操作数,总被视为long型(4个字节),但对其施加的操作根据指令可以是字或者字节,当把操作数当作字或者字节使用时,默认为低字或者低字节。对字节操作可以显式的指明是低字节还是次字节。方法是在%和序号之间插入一个字母,"b"代表低字节,"h"代表高字节,例如:%h1。
  
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
  * 输出部分描述输出操作数,不同的操作数描述符之间用逗号格开,每个操作数描述符由限定字符串和C 语言变量组成。每个输出操作数的限定字符串必须包含"="表示他是一个输出操作数。

eg：

```c
从arm协处理器p15中读出C1值

static unsigned long read_p15_c1 (void)
{
unsigned long value;
__asm__ __volatile__(
"mrc p15, 0, %0, c1, c0, 0 @ read control reg\n"
: "=r" (value) @编译器选择一个R*寄存器
:
: "memory");
#ifdef MMU_DEBUG
printf ("p15/c1 is = %08lx\n", value);
#endif
return value;
}
```

* Input
  * 用来指定当前内联汇编语句的输入，Output和Input中，格式为形如“constraint”(variable)的列表（逗号分隔)
  * 输入部分描述输入操作数,不同的操作数描述符之间使用逗号格开,每个操作数描述符由限定字符串和C语言表达式或者C语言变量组成。

eg：
```c
向arm协处理器p15中写入C1值

static void write_p15_c1 (unsigned long value) 
        { 
        #ifdef MMU_DEBUG 
                printf ("write %08lx to p15/c1\n", value); 
        #endif 
                __asm__ __volatile__( 
                                "mcr p15, 0, %0, c1, c0, 0 @ write it back\n" 
                                : 
                                : "r" (value) @编译器选择一个R*寄存器 
                                : "memory"); 
                read_p15_c1 (); 
        } 
```
* Clobber/Modify
  * 有时候，你想通知GCC当前内联汇编语句可能会对某些寄存器或内存进行修改，希望GCC在编译时能够将这一点考虑进去。那么你就可以在Clobber/Modify域声明这些寄存器或内存。这种情况一般发生在一个寄存器出现在"Instruction List"，但却不是由Input/Output操作表达式所指定的，也不是在一些Input/Output操作表达式使用"r"约束时由GCC 为其选择的，同时此寄存器被"Instruction List"中的指令修改，而这个寄存器只是供当前内联汇编临时使用的情况。

eg：

`__asm__ ("mov R0, #0x34" : : : "R0");`

寄存器R0出现在"Instruction List中"，并且被mov指令修改，但却未被任何Input/Output操作表达式指定，所以你需要在Clobber/Modify域指定"R0"，以让GCC知道这一点。

因为你在Input/Output操作表达式所指定的寄存器，或当你为一些Input/Output操作表达式使用"r"约束，让GCC为你选择一个寄存器时，GCC对这些寄存器是非常清楚的——它知道这些寄存器是被修改的，你根本不需要在Clobber/Modify域再声明它们。但除此之外， GCC对剩下的寄存器中哪些会被当前的内联汇编修改一无所知。所以如果你真的在当前内联汇编指令中修改了它们，那么就好在Clobber/Modify 中声明它们，让GCC针对这些寄存器做相应的处理。否则有可能会造成寄存器的不一致，从而造成程序执行错误。

如果一个内联汇编语句的Clobber/Modify域存在"memory"，那么GCC会保证在此内联汇编之前，如果某个内存的内容被装入了寄存器，那么在这个内联汇编之后，如果需要使用这个内存处的内容，就会直接到这个内存处重新读取，而不是使用被存放在寄存器中的拷贝。因为这个 时候寄存器中的拷贝已经很可能和内存处的内容不一致了。

这只是使用"memory"时，GCC会保证做到的一点，但这并不是全部。因为使用"memory"是向GCC声明内存发生了变化，而内存发生变化带来的影响并不止这一点。

eg：
```c
int main(int __argc, char* __argv[])
{
int* __p = (int*)__argc;
(*__p) = 9999;
__asm__("":::"memory");
if((*__p) == 9999)
return 5;
return (*__p);
}
```



本例中，如果没有那条内联汇编语句，那个if语句的判断条件就完全是一句废话。GCC在优化时会意识到这一点，而直接只生成return 5的汇编代码，而不会再生成if语句的相关代码，而不会生成return (*__p)的相关代码。但你加上了这条内联汇编语句，它除了声明内存变化之外，什么都没有做。但GCC此时就不能简单的认为它不需要判断都知道 (*__p)一定与9999相等，它只有老老实实生成这条if语句的汇编代码，一起相关的两个return语句相关代码。

另外在linux内核中内存屏障也是基于它实现的include/asm/system.h中

`define barrier() _asm__volatile_("": : :"memory")`

主要是保证程序的执行遵循顺序一致性。呵呵，有的时候你写代码的顺序，不一定是终执行的顺序，这个是处理器有关的。

## 常用代码示例
字符拷贝
```c
static inline char* strcpy (char* dest, const char* src)
{
int d0, d1, d2;
__asm__ __volatile__(  "1:/tlodsb\n\t"
                       "stosb\n\t"
                       "testb %%al,%%al\n\t"
                       "jne 1b"     
                     : "=&S" (d0), "=&D" (d1), "=&a" (d2)
                     : "0" (src),"1" (dest)
                     : "memory");
return dest;
}
```

获取高精度时钟
```c
static inline unsigned long long rdtsc(void) {
    unsigned int lo, hi;
    unsigned long val;
    __asm__ __volatile__ ("rdtsc":"=a" (lo), "=d" (hi));
    val = hi;
    return ((val << 32) + lo);
}
```
