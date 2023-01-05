---
layout: post
title: Linux backtrace调用栈回溯介绍
categories: technical, linux
description: Linux backtrace调用栈回溯介绍
keywords: backtrace,backtrace_symbols
---

## 介绍
gdb 调试时通过 bt 可以打印出程序的堆栈调用情况，为了在程序抛出异常或者收到异常信号时输出程序的堆栈调用情况到日志中，我们可以使用 linux 的 backtrace() 和 backtrace_symbols() 获取函数调用堆栈帧数据,即回溯函数调用列表。

## 函数说明
### 头文件
```c
#include <execinfo.h>
```
### 函数原型
```c
int backtrace (void **buffer, int size);
char **backtrace_symbols (void *const *buffer, int size);
void backtrace_symbols_fd (void *const *buffer, int size, int fd);
```
### 函数描述
backtrace()函数，获取函数调用堆栈帧数据，即回溯函数调用列表。数据将放在buffer中。参数size用来指定buffer中可以保存多少个void*元素（表示相应栈帧的地址，一个返回地址）。如果回溯的函数调用大于size，则size个函数调用地址被返回。为了取得全部的函数调用列表，应保证buffer和size足够大。

backtrace_symbols()函数，参数buffer是从backtrace()函数获取的数组指针，size是该数组中的元素个数（backtrace()函数的返回值）。该函数主要功能：将从backtrace()函数获取的地址转为描述这些地址的字符串数组。每个地址的字符串信息包含对应函数的名字、在函数内的十六进制偏移地址、以及实际的返回地址（十六进制）。需注意的是，当前，只有使用elf二进制格式的程序才能获取函数名称和偏移地址，此外，为支持函数名功能，可能需要添加相应的编译链接选项如-rdynamic；否则，只有十六进制的返回地址能被获取。backtrace_symbols()函数返回值是一个字符串指针，是通过malloc函数申请的空间，使用完后，调用者必需把它释放掉。注：如果不能为字符串获取足够的空间，该函数的返回值为NULL。

backtrace_symbols_fd()函数，与backtrace_symbols()函数具有相同的功能，不同的是它不会给调用者返回字符串数组，而是将结果写入文件描述符为fd的文件中，每个函数对应一行。它不会调用malloc函数，因此，它可以应用在函数调用可能失败的情况下

### 返回值
backtrace()函数返回通过buffer返回的地址个数，这个数目不会超过size。如果这个返回值小于size，那么所有的函数调用列表都被保存；如果等于size，那么函数调用列表可能被截断，此时，一些最开始的函数调用没有被返回。

成功时，backtrace_symbols()函数返回一个由malloc分配的数组；失败时，返回NULL。

### demo
```c
#include <cxxabi.h>
#include <execinfo.h>
#include <stdlib.h>

string stackTrace(bool demangle)
{
  string stack;
  const int max_frames = 200;
  void* frame[max_frames];
  int nptrs = ::backtrace(frame, max_frames);
  char** strings = ::backtrace_symbols(frame, nptrs);
  if (strings)
  {
    size_t len = 256;
    char* demangled = demangle ? static_cast<char*>(::malloc(len)) : nullptr;
    for (int i = 1; i < nptrs; ++i)  // skipping the 0-th, which is this function
    {
      if (demangle)
      {
        // https://panthema.net/2008/0901-stacktrace-demangled/
        // bin/exception_test(_ZN3Bar4testEv+0x79) [0x401909]
        char* left_par = nullptr;
        char* plus = nullptr;
        for (char* p = strings[i]; *p; ++p)
        {
          if (*p == '(')
            left_par = p;
          else if (*p == '+')
            plus = p;
        }

        if (left_par && plus)
        {
          *plus = '\0';
          int status = 0;
          // 获取函数原型
          char* ret = abi::__cxa_demangle(left_par+1, demangled, &len, &status);
          *plus = '+';
          if (status == 0)
          {
            demangled = ret;  // ret could be realloc()
            stack.append(strings[i], left_par+1);
            stack.append(demangled);
            stack.append(plus);
            stack.push_back('\n');
            continue;
          }
        }
      }
      // Fallback to mangled names
      stack.append(strings[i]);
      stack.push_back('\n');
    }
    free(demangled);
    free(strings);
  }
  return stack;
}
```

## 参考
[linux backtrace()详细使用说明，分析Segmentation fault](https://www.cnblogs.com/muahao/p/7610645.html)

[C++ 如何获取函数的原型](https://www.zhihu.com/question/278587865/answer/401159650)
