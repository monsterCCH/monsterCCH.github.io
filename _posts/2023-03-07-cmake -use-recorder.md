---
layout: post
title: cmake 使用记录
categories: cmake, build
description: 记录使用 cmake 中遇到的问题
keywords: cmake, build
---

## target_link_libraries 链接库顺序
```cmake
target_link_libraries(test libA libB libC)
# 链接顺序 libA <== libB <== libC 优先使用 libC
```

## target_compile_options 语法解析
```cmake
target_compile_options(
        ${target_name}
        PRIVATE $<$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:AppleClang>,$<CXX_COMPILER_ID:GNU>>:
        -Wall
        -Wextra
        -Wconversion
        -pedantic
        -Werror
        -Wfatal-errors>
        $<$<CXX_COMPILER_ID:MSVC>:${MSVC_OPTIONS}>)
```
如果使用的是 Clang、AppleClang 或 GNU，就会添加 -Wall、-Wextra、-Wconversion、-pedantic、-Werror、-Wfatal-errors 等编译选项，分别表示启用所有警告、启用额外的警告、启用类型转换时的警告、启用语言标准的警告、将所有警告都视为错误、将所有致命错误都视为错误。而对于 MSVC，则会添加 MSVC_OPTIONS 中指定的编译选项。
