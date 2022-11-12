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
int f(int i)[[expects:i > 0]] // expects已在C++20中废弃,在C++23中用assume替代
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

### [[noreturn]]
#### 用法
```c++
[[noreturn]] void f();
```

#### 示例
```c++
#include <iostream>
#include <string>

[[noreturn]] void f()
{
	// Some code that does not return
	// back the control to the caller
	// In this case the function returns
	// back to the caller without a value
	// This is the reason why the
	// warning "noreturn' function does return' arises
}

void g()
{
	std::cout << "Code is intented to reach here";
}

int main()
{
	f();
	g();
}
```
#### 说明
表明函数永远不会返回，注意返回值为 void 函数也是会返回的，只是没有返回值。一般用于无线循环的情况。

### [[carries_dependency]]
#### 示例
```c++
std::atomic<int *> p;
std::atomic<int *> q;
void func1(int *val) { 
    std::cout << *val << std::endl;
}
void func2(int * [[carries_dependency]] val) {
    q.store(val, std::memory_order_release);
    std::cout << *q << std::endl;
}
void thread_job() {    
    int *ptr1 = (int *)p.load(std::memory_order_consume); //1
    std::cout << *ptr1 << std::endl; //2
    func1(ptr1); //3
    func2(ptr1); //4
    }
```
#### 说明
这个属性的作用是允许我们将dependency跨越函数进行传递，用于避免在弱一致性模型平台上产生不必要的内存栅栏导致代码效率降低。

一般来说，这个属性是搭配 std::memory_order_consume 来使用的，支持这个属性的编译器可以根据属性的指示生成更合适的代码帮助程序在线程之间传递数据。在典型的情况下，如果在 memory_order_consume 的情况下读取一个值，编译器为了保证合适的内存读取顺序，可能需要额外的内存栅栏协调程序行为顺序，但是如果加上了[[carries_dependency]]的属性，则编译器可以保证函数体也被扩展包含了同样的dependency，从而不再需要这个额外的内存栅栏。同样的事情对于函数的返回值也是一致的。

程序在1的位置因为ptr1明确的使用了memory_order_consume的内存策略，所以对于ptr1的访问一定会被编译器排到这一行之后。
因为1的原因，所以这一行在编译的时候势必会排列在1后面。

func1并没有带任何属性，而他访问了ptr1，那么编译器为了保证内存访问策略被尊重所以必须在func1调用之间构建一个内存栅栏。如果这个线程被大量的调用，这个额外的内存栅栏将导致性能损失。

在func2中，我们使用了[[carries_dependency]]属性，那么同样的访问ptr1，编译器就知道程序已经处理好了相关的内存访问限制。这个也正如我们再func2中对val访问所做的限制是一样的。那么在func2之前，编译器就无需再插入额外的内存栅栏，提高了效率。

#### 补充参考资料
[C++：C++11中memory_order_consume的目的](https://blog.csdn.net/netyeaxi/article/details/80718781)

### [[deprecated]]
#### 用法
```c++
[[deprecated("Reason for deprecation")]] 

// For Class/Struct/Union
struct [[deprecated]] S;

// For Functions
[[deprecated]] void f();

// For namespaces
namespace [[deprecated]] ns{}

// For variables (including static data members)
[[deprecated]] int x;
```
#### 示例
```c++
[[deprecated("Susceptible to buffer overflow")]] void gets(char* str)
{
  
    // Code for gets dummy
    // (Although original function has
    // char* as return type)
}
  
void gets_n(std::string& str)
{
    // Dummy code
    char st[100];
    std::cout << "Successfully Executed";
    std::cin.getline(st, 100);
    str = std::string(st);
    // Code for new gets
}
  
int main()
{
    char a[100];
    gets(a);
  
    // std::string str;
    // gets_n(str);
}
```
#### 说明
这个属性是在C++14的标准中被引入的。被这个属性加持的名称或者实体在编译期间会输出对应的警告，告诉使用者该名称或者实体将在未来被抛弃。如果指定了具体的"reason"，则这个具体的原因也会被包含在警告信息中。 

### [[nodiscard]]
#### 用法
```c++
// Functions
[[nodiscard]] void f();

// Class/Struct declaration 
struct [[nodiscard]] my_struct{};
```

#### 示例
```c++
// Return value must be utilized by the caller
[[nodiscard]] int f()
{
    return 0;
}
  
class[[nodiscard]] my_class{};
  
// Automatically becomes nodiscard marked
my_class fun()
{
    return my_class();
}
  
int main()
{
    int x{ 1 };
  
    // No error as value is utilised
    // x= f();
  
    // Error : Value is not utilised
    f();
  
    // Value not utilised error
    // fun() ;
    return x;
}
```

#### 说明
这两个属性和前面的[[deprecated]]类似，但是他们是在不同的C++标准中被引入的，[[nodiscard]]是在C++17标准中引入，而[[nodiscard("reason")]]是在C++20标准中引入。

这个属性的含义是明确的告诉编译器，用此属性修饰的函数，其返回值（必须是按值返回）不应该被丢弃，如果在实际调用中舍弃了返回变量，则编译器会发出警示信息。如果此属性修饰的是枚举或者类，则在对应函数返回该类型的时候也不应该丢弃结果。

### [[fallthrough]]
#### 示例
```c++
void process_alert(Alert alert)
{
	switch (alert) {
	case Alert::Red:
		evacuate();
	// Compiler emits a warning here
	// thinking it is done by mistake

	case Alert::Orange:
		trigger_alarm();

		// this attribute needs semicolon
		[[fallthrough]];
	// Warning suppressed by [[fallthrough]]

	case Alert::Yellow:
		record_alert();
		return;

	case Alert::Green:
		return;
	}
}
```
#### 说明
[[fallthrough]]放在一个case块的末尾，表明你是故意没写break语句。不然的话编译器可以给你一个warning，static analyzer和code reviewer也会警告你。

### [[maybe_unused]]
#### 用法
```c++
//Variables
[[maybe_used]] bool log_var = true;

//Functions
[[maybe_unused]] void log_without_warning();

//Function arguments 
void f([[maybe_unused]] int a, int b);
```

#### 示例
```c++
int main()
{
  
    // Set debug mode in compiler or 'R'
    [[maybe_unused]] char mg_brk = 'D';
  
    // Compiler does not emit any warnings
    // or error on this unused variable
}
```
#### 说明
通常情况下，对于声明了但是从未使用过的变量会给出警告信息。但是在声明的时候添加了这个属性，则编译器确认是程序故意为之的逻辑，则不再发出警告。需要注意的是，这个声明不会影响编译器的优化逻辑，在编译优化阶段，无用的变量该干掉还是会被干掉的。

### [[likely]] 和 [[unlikely]]
#### 示例
```c++
int f(int i)
{
	switch (i) {
	case 1:
		[[fallthrough]];
		[[likely]] case 2 : return 1;
        [[unlikely]] case 3 : return 2;
	}
	return 2;
}

```
#### 说明
这一对属性是在C++20的时候引入标准的，这两个语句只允许用来修饰标号或者语句（非声明语句），目的是告诉编译器，在通常情况下，哪一个分支的执行路径可能性最大，显然，他俩也是不能同时修饰同一条语句。

在真实的场景中，[[likely]]和[[unlikely]]属性能否如我们所愿发挥作用是需要具体问题具体分析的。不过正确的使用属性即便没有正向收益，也不会有负收益，并且我相信在大部分的场景下这是有好处的，并且在未来编译器更加优化之后，明确意图的代码总是能得到更多优化。

### [[no_unique_address]]
#### 示例
```c++
// empty class ( No state!)
struct Empty {
};

struct X {
	int i;
	Empty e;
};

struct Y {
	int i;
	[[no_unique_address]] Empty e;
};

int main()
{
	// the size of any object of
	// empty class type is at least 1
	static_assert(sizeof(Empty) >= 1);

	// at least one more byte is needed
	// to give e a unique address
	static_assert(sizeof(X) >= sizeof(int) + 1);

	// empty base optimization applied
	static_assert(sizeof(Y) == sizeof(int));
}
```
#### 说明
这个属性也是在C++20中引入的，旨在和编译器沟通非位域非静态数据成员不需要具有不同于其相同类型其他非静态成员不同的地址。带来的效果就是，如果该成员拥有空类型，则编译器可以将它优化为不占用空间的部分。

## 参考资料

[Attribute specifier sequence](https://en.cppreference.com/w/cpp/language/attributes)

[谈谈C++新标准带来的属性（Attribute）](https://www.eet-china.com/mp/a65145.html)

[attributes in C++](https://www.geeksforgeeks.org/attributes-in-c/)

[A Tour to C++ Attribute](https://zhuanlan.zhihu.com/p/64493524)
