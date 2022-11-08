---
layout: post
title: cpp attribute 的理解与应用
categories: cpp, attribute
description: cpp attribute 的理解与应用
keywords: cpp, attribute
---

## 概念

attribute 是现代C++的关键特性之一，允许开发者向编译器指定附加信息来强制执行有条件的约束、优化某些代码片段或生成某些特定代码。简单来说，attribute 充当编译器的注释，向编译器提供了有关代码的附加信息以用于优化代码并对其强制执行某些条件。其随C++11引入，并随着C++版本不断发展。

## 历史

由于在C++11之前，不同的编译器对 attribute 的规范都有自己的一套标准，不统一。自C++11开始引入C++标准的 attribute，这些 attribute 在所有的编译器上都是通用，但是不同的编译器还是保留着自己的一套独有标准。

## 语法

```c++
// C++ 11
[[attribute-list]]

// C++ 17
[[using attribute-namespace:attribute-list]]

// C++ 20
[[contract-attribute-token contract-level identifier : expression]]

eg：
[[noreturn]]
[[gnu::unused]]
[[deprecated("because")]]
// 同一命名空间的多个 attribute (since C++17)
[[using CC: opt(1), debug]] // same as [[CC::opt(1), CC::debug]]
```

除了一些特定的属性(待补充)，大部分attribute都可以应用于变量、函数、类、结构体等

## 作用

### 对代码进行约束

这里的约束是指一个条件，一个特定函数的参数必须满足它的执行前提条件。在以前的 C++ 版本中，指定约束的代码就是这样写的

```c++
int f(int i)
{
	if (i > 0)
		return i;
	else
		return -1;
	// Code
}
```

添加 attribute 后

```c++
int f(int i)[[expects:i > 0]]
{
	// Code
}
```

增加了代码的可读性，同时不用在函数内部编写用于参数检查的代码，代码更加整洁。

### 为优化目的向编译器提供附加信息

编译器非常擅长优化，但与人类相比，它们在某些地方仍然落后，这会导致生成一些效率不高的通用代码。这主要是因为缺乏关于人类所面临的“问题”的额外信息。为了在一定程度上减少这个问题，C++ 标准引入了一些新属性，允许对编译器而不是代码语句本身进行更多指定。

```c++
int f(int i)
{
	switch (i) {
	case 1:
		[[fallthrough]];
		[[likely]] case 2 : return 1;
	}
	return -1;
}
```

编译器会对该语句进行特殊优化，从而提高代码的整体性能。此类属性的一些示例是[carries_dependency]、[likely]、[unlikely]

### 清除开发人员在其代码中出现有意为之而产生一些警告和错误

这种情况很少发生，但有时开发者会故意尝试编写一些代码，该代码会被编译器检测到并报告为错误或警告。一个这样的例子是一个未使用的变量，它由于特定原因而处于该状态，或者一个 switch 语句，其中在某些情况下没有放置 break 语句以引起失败条件。为了规避此类情况下的错误和警告，C++ 提供了诸如[maybe_unused]和[ fallthrough] 之类的属性，以防止编译器生成警告或错误。

```c++
int main()
{
	// Set debug mode in compiler or 'R'
	[[maybe_unused]] char mg_brk = 'D';
	// Compiler does not emit any warnings
	// or error on this unused variable
}
```

## C++ attribute 更新历史

```c++
// C++11 引入
[[noreturn]] // 表明函数不会返回
[[carries_dependency]]

// C++14 引入
[[deprecated]]
[[deprecated("reason")]]

// C++ 17 引入
[[fallthrough]]
[[nodiscard]]
[[maybe_unused]]

// C++ 20 引入
[[likely]]
[[unlikely]]
[[no_unique_address]]
[[nodiscard("reason")]]

// C++23 引入
[[assume]]
```

## 标准 attribute 和非标准 attribute 的区别

| 标准attribute      | 非标准attribute                                              |
|------------------|-----------------------------------------------------------|
| 由标准指定存在于所有编译器中   | 由编译器供应商提供并非存在所有编译器中                                       |
| 代码是完全可移植的没有错误或警告 | 代码移植性不好                                                   |
| 属性写在标准语法[[atr]]中 | 一些属性写在非标准语法中，而另一些属性写在编译器特定的关键字中，如declspec()或__attribute__ |
| 标准属性不存在于任何封闭的命名空间中        | 非标准属性以标准语法编写，其封闭命名空间[[namespace::attr]]                                             |

## C++ 标准 attribute 说明
