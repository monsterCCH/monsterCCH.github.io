---
layout: post
title: 从 hook 的角度来分析内存泄漏
categories: hook, memory
description: 从 hook 的角度来分析内存泄漏
keywords: dlysym, memory_leak
---

## 概述
内存由用户管理可能会导致很多问题，如访问空指针、访问野指针、内存越界访问、内存泄漏、重复释放。为什么会发生内存泄漏，本质上就是程序在堆上申请内存空间后，在使用完成后没有对应释放。在 C 语言中就是 malloc 后没有 free，C++ 中则是 new 后没有 delete, 但是 C++ 可以借助 RAII 机制（智能指针）自动释放申请的资源。那么如何检测程序中的内存泄漏呢？以下是几种常用手段。
* 代码走查，适用于代码查找范围小的情况
* 借助分析工具，如 valgrind 和 gcc 中的 sanitizer等
* hook malloc/free 函数，实现自定义的 malloc/free 函数以统计内存申请释放情况

本文主要从 hook 的角度来分析一下内存泄漏。

## 通过hook的方式检测，定位内存泄露
测试代码
```c
#include <stdlib.h>
int main()
{
    void *ptr1 = malloc(10 * sizeof(char));
    void *ptr2 = malloc(20 * sizeof(char));
    free(ptr1);
    
    void *ptr3 = malloc(30 * sizeof(char));
    free(ptr3);
    return 0;
}
```

## 通过dlsym库函数对malloc/free进行hook
dlsym函数的功能就是可以从共享库（动态库）中获取符号（全局变量与函数符号）地址，通常用于获取函数符号地址，这样可用于对共享库中函数的包装；下面是函数原型及需要包含的头文件。
```c
#include <dlfcn.h>
void *dlsym(void *handle, const char *symbol);
```
其中handle可以是dlopen函数返回的handle值，也可以是RTLD_DEFAULT或RTLD_NEXT

RTLD_DEFAULT表示按默认的顺序搜索共享库中符号symbol第一次出现的地址

RTLD_NEXT表示在当前库以后按默认的顺序搜索共享库中符号symbol第一次出现的地址

两者的区别在于RTLD_DEFAULT在当前文件存在同名符号时会获取当前文件符号地址，RTLD_NEXT则会获取下一个符号如glibc中的符号地址。

### RC1
```c
//1:使用void * dlsym(void* handle, char* symbool)函数和handle为RTLD_NEXT标记，对malloc/free进行hook
//2:RTLD_NEXT标记 需要在本地实现 symbool同名函数达到hook功能，即这里要实现malloc/free功能
//3:dlsym()返回的是symbool 参数对应的函数的地址，在同名函数中用该地址实现真正的调用

//RTLD_NEXT 是dlsym() 库中的伪句柄，定义_GNU_SOURCE宏才能识别
//可以通过 man dlsym
//测试发现 ：必须放在最顶部，不然编译报 RTLD_NEXT没有定义 
#define _GNU_SOURCE
#include <dlfcn.h>  //对应的头文件

//第一步功能，确定hook成功，先在我们的hook函数中增加一些打印信息验证
#include <stdio.h>
#include <stdlib.h>

//定义相关全局变量，获取返回的函数地址，进行实际调用
typedef void *(*malloc_t)(size_t size);
malloc_t malloc_f = NULL;

typedef void (*free_t)(void* p);
free_t free_f = NULL;

//要hook的同名函数
void * malloc(size_t size){
    printf("exec malloc \n");
    return malloc_f(size);
}

void free(void * p){
    printf("exec free \n");
    free_f(p);
}
//通过dlsym 对malloc和free使用前进行hook
static void init_malloc_free_hook(){
    //只需要执行一次
    if(malloc_f == NULL){
        malloc_f = dlsym(RTLD_NEXT, "malloc"); //除了RTLD_NEXT 还有一个参数RTLD_DEFAULT
    }
    
    if(free_f == NULL)
    {
        free_f =  dlsym(RTLD_NEXT, "free");
    }
    return ;
}


int main()
{
    init_malloc_free_hook(); //执行一次
    void * ptr1 = malloc(10);
    void * ptr2 = malloc(20);
    
    free(ptr1);
    
    void * ptr3 = malloc(30);
    free(ptr3);
    return 0;
}
```
