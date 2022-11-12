---
layout: post
title: linux进程在32位和64位下的内存布局
categories: mem, linux
description: linux进程在32位和64位下的内存布局
keywords: mem, linux
---

## 概念
linux 为每个进程维护了一套单独的虚拟地址空间，以此来更好的管理物理内存，并使不同的进程不会彼此干扰。

## 32位linux进程内存空间布局

![](/images/posts/linux-mem-layout/linux32_mem_layout.png)

如图所示，栈空间至顶向下扩展且栈空间是有界的，一般默认大小为8Mb，可通过`ulimit -s`查看linux设置大小。堆至底向上扩展， mmap 映射区域至顶向下扩展， mmap 映射区域和堆相对扩展，直至耗尽虚拟地址空间中的剩余区域，这种结构便于 C 运行时库使用 mmap 映射区域和堆进行内存分配。

栈和 mmap 映射区域并不是从一个固定地址开始，程序在每次启动时会随机修改offset的值，以使缓存区溢出攻击更加困难。当然也可以让程序的栈和 mmap 映射区域从一个固定位置开始，只需要设置全局变量 randomize_v a_space 值为 0 ，这个变量默认值为 1 。用户可以通过如下的任一方式来停用进程地址随机化
```shell
echo 0 >  /proc/sys/kernel/randomize_va_space
sudo sysctl -w kernel.randomize_va_space=0
```
该属性有3个取值：
* 0 ： 表示关闭内存地址随机化
* 1 ： 表示将mmap的基址，stack和vdso地址随机化
* 2 ： 表示在1的基础上， heap地址随机化

## 64位linux进程内存空间布局

![](/images/posts/linux-mem-layout/linux64_mem_layout.png)

64位系统的寻址空间比较大，沿用了32位的经典布局，但是加上了随机的mmap起始地址，以防止溢出攻击。

目前大部分的操作系统和应用程序并不需要16EB( 264 )如此巨大的地址空间, 实现64位长的地址只会增加系统的复杂度和地址转换的成本, 带不来任何好处. 所以目前的x86-64架构CPU都遵循AMD的Canonical form, 即只有虚拟地址的最低48位才会在地址转换时被使用, 且任何虚拟地址的48位至63位必须与47位一致(sign extension). 也就是说, 总的虚拟地址空间为256TB

256TB的虚拟内存空间划分如下:

* 0000000000000000 - 00007fffffffffff(128TB)为用户空间
* ffff800000000000 - ffffffffffffffff(128TB)为内核空间

内核空间中有很多空洞, 越过第一个空洞后, ffff880000000000 - ffffc7ffffffffff(64TB)才是直接映射物理内存的区域, 也就是说默认的PAGE_OFFSET为ffff880000000000

这么大的直接映射区域足够映射所有的物理内存, 所以目前x86-64架构下是不存在高端内存, 也就是ZONE_HIGHMEM这个区域的

#### 查看cpu寻址能力

`cat /proc/cpuinfo | grep "address sizes"`

```text
address sizes   : 45 bits physical, 48 bits virtual
表示45位物理地址空间寻址，48位虚拟地址空间寻址
```

#### 查看实际内存布局
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/mman.h>
//#include <sys/types.h>
#include <fcntl.h>
//#include <unistd.h>

int bss_var;
int data_var = 200;

void text_var(void)
{
    printf("test\n");
}

int main(int argc, char* argv[])
{
    int stack_var;
    int fd;
    int *heap_var;
    void *mmap_var;

    fd = open("1", O_CREAT|O_RDWR, 0777);
    mmap_var = mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_SHARED, fd, 0);
    munmap(mmap_var, 4096);
    heap_var = (int *)malloc(16);

    printf("stack_var: %p\n", &stack_var);
    printf("mmap_var: %p\n", mmap_var);
    printf("heap_var: %p\n", heap_var);
    printf("bss_var: %p\n", &bss_var);
    printf("data_var: %p\n", &data_var);
    printf("text_var: %p\n", &text_var);

    return 0;
}
```

gcc -o test test.c -static

运行结果
```shell
❯ ./test
stack_var: 0x7ffe2008fd90
mmap_var: 0x7fb91d2ae000
heap_var: 0x22fb780
bss_var: 0x4c72f0
data_var: 0x4c50f0
text_var: 0x401775
❯ ./test
stack_var: 0x7ffd89612760
mmap_var: 0x7f1733383000
heap_var: 0xfd8780
bss_var: 0x4c72f0
data_var: 0x4c50f0
text_var: 0x401775
❯ ./test
stack_var: 0x7ffca9420c80
mmap_var: 0x7fec6fa1e000
heap_var: 0x956780
bss_var: 0x4c72f0
data_var: 0x4c50f0
text_var: 0x401775
由于开启了地址随机化，每次运行stack、mmap、heap的地址都不同，关闭后则保持一致
```

#### 查看进程的内存空间布局

```shell
cat /proc/xxx/maps
cat /proc/xxx/smaps # 更详细信息
```

## 参考资料
[linux进程的内存布局](https://lishiwen4.github.io/linux/linux-process-memory-location)

[Linux虚拟地址空间布局](http://www.360doc.com/content/14/1020/21/19947352_418512226.shtml)

[Linux进程栈空间大小](https://www.tiehichi.site/2020/10/22/Linux%E8%BF%9B%E7%A8%8B%E6%A0%88%E7%A9%BA%E9%97%B4%E5%A4%A7%E5%B0%8F/)
