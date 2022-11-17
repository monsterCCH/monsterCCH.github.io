---
layout: post
title: c++ 智能指针
categories: c++, smart point
description: c++ 智能指针
keywords: c++, smart point
---

## 概念
和Java、C#等语言不一样，C++没有内置的内存回收器。至于为什么C++没有内存回收器，C++之父Bjarne Stroustrup说：“我不喜欢乱扔垃圾，不产生任何内存垃圾就不需要内存垃圾回收器了”。

智能指针是一种使用封装的普通指针的范例，我们不需要显式删除被指向的对象。智能指针遵循RAII或资源获取即初始化模式，其中堆分配对象的所有权属于堆栈分配对象，因此当调用其析构函数时，可以完成动态内存的释放。C++ 保证总是调用静态分配对象的析构函数，而不考虑异常。智能指针在<memory>头文件的std命名空间中定义。最受欢迎的unique_ptr和shared_ptr。 unique_ptr 只允许底层指针有一个所有者，而 shared_ptr 是一个引用计数的智能指针。
