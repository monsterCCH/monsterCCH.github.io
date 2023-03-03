---
layout: post
title: container_of 函数
categories: kernel, func
description: container_of 函数介绍
keywords: kernel, func
---

## 功能
根据结构体成员获取结构体成员所在结构体的首地址

## 定义
```c
#define container_of(ptr, type, member) ({			\
	const typeof( ((type *)0)->member ) *__mptr = (ptr);	\
	(type *)( (char *)__mptr - offsetof(type,member) );})
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)

# 在 c++ 中使用 decltype 代替 typeof
tips: C++ 中没有 typeof 运算符。虽然大多数编译器已经提供这样的功能已经有一段时间了，但它一直是编译器特定的语言扩展。因此，通常比较两者的行为没有意义，因为 typeof 的行为（如果它存在的话）是非常依赖于平台的。

因为我们现在有一个获取变量/表达式类型的标准方法，所以真的没有理由依赖不可移植的扩展，所以我想说它已经过时了。

另一件要考虑的事情是，如果 typeof 的行为与给定编译器的 decltype 不兼容，则 typeof 扩展可能不会得到太多发展以包含未来的新语言功能（这意味着它可能根本不起作用例如 lambdas）。我不知道目前是否是这种情况，但这是一种明显的可能性
```

## 说明
`const typeof( ((type *)0)->member ) *__mptr = (ptr);`

typeof是GNU C对标准C的扩展，它的作用是根据变量获取变量的类型。因此，上述代码的作用是首先使用typeof获取结构体成员 member 的类型,然后定义一个对应类型的指针临时变量 __mptr,并将结构体变量中的成员的地址 ptr 赋给临时变量 __mptr。

offsetof 用来获取结构体成员相对于地址0的偏移量。

用对应成员的地址减去该成员在结构体中的相对偏移量则为该结构体的首地址。

## 代码验证

```c
#include <stdio.h>

#define container_of(ptr, type, member) ({                      \
        const typeof( ((type *)0)->member ) *__mptr = (ptr);    \
        (type *)( (char *)__mptr - offsetof(type,member) );})
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)

typedef struct test_co {
    int a;
    char b;
    float c;
    double d;
}test_co;

int main(int args, char *argv[])
{
    test_co co, *coPtr;
    co.c = 3.14;
    coPtr = container_of(&co.c, test_co, c);
    printf("test_co.d offset: %d\n", offsetof(test_co, c));
    printf("test_co.d address：%p\n", &co.c);
    printf("test_co container_of address:%p\n", coPtr);
    printf("test_co origin address: %p\n", &co);
}

```

result:
```shell
test_co.d offset: 8
test_co.d address：0x7fffffffe3d8
test_co container_of address: 0x7fffffffe3d0
test_co origin address: 0x7fffffffe3d0
```
