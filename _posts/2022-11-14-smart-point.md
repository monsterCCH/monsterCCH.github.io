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

## 为什么要使用智能指针
指针是访问堆内存的一种方式，但由于 heap memory 完全由开发者自己管理，就会存在申请资源忘记释放的情况，毕竟人为因素是不可控的。比如以下code

```c++
class demo {
    private:
        string str;
};
demo * func() {
    demo *p = new demo();
    return p;
}
```
func 返回的指针如果没有被释放就会造成内存泄漏，但即使开发者考虑到释放，在c++中也有可能内存泄漏。比如：

```c++
void fun() {
    demo * p = new demo[];
    // do something may throw exception but don't be catch
    delete[] p;
}
```
当在资源释放前抛出异常时也会导致资源没有被释放。

## 用法
在<memory>中主要有三种智能指针，auto_ptr已经废弃
* unique_ptr 表示互斥的所有权
* shared_ptr 表示共享所有权
* weak_ptr 用于打破共享所有权的回路

### unique_ptr
unique_ptr 提供严格的所有权, 有以下特性
* 拥有一个对象，保存一个指向该对象的指针
* 不能拷贝，没有拷贝构造和拷贝赋值， 但可以转移所有权
* 自身被销毁时需要同时释放所拥有的对象
