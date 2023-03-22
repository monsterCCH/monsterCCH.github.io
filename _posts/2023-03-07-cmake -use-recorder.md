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

## target_include_directories
```cmake
target_include_directories(p2p SYSTEM PRIVATE ${CRYPTOPP_INCLUDE_DIR})
```
在CMake中，SYSTEM是一个标志，用于指定某个目录是否应被视为系统目录。通常情况下，系统目录包含操作系统提供的头文件和库文件，而用户目录则包含用户自己编写的头文件和库文件。

当使用include_directories或target_include_directories函数添加头文件路径时，可以使用SYSTEM标志将该路径标记为系统路径。这主要是为了避免编译过程中产生某些警告或错误，因为一些编译器会对系统头文件做特殊处理，忽略目录中发现的某些警告。

## PUBLIC、PRIVATE、INTERFACE
在CMake中，PUBLIC、PRIVATE和INTERFACE是目标属性的关键字，用于指定目标对其他目标的可见性和使用方式。下面是这些关键字的解释和作用：

* PUBLIC属性：目标的PUBLIC属性表示该目标将它的属性（例如头文件路径和链接库）公开给其他目标。当一个目标使用另一个目标时，它也将使用该目标的PUBLIC属性。例如，如果您的库需要使用另一个库的头文件和链接库，您可以将该库的属性设置为PUBLIC，然后您的库将自动使用这些属性。

* PRIVATE属性：目标的PRIVATE属性表示该目标的属性仅对该目标的实现代码可见，其他目标无法使用。例如，如果您的库需要使用另一个库的实现代码，但不需要其他目标使用该库，您可以将该库的属性设置为PRIVATE。

* INTERFACE属性：目标的INTERFACE属性表示该目标的属性对其他目标是可见的，但不对该目标的实现代码可见。例如，如果您的库需要使用另一个库的头文件和链接库，并且您希望其他目标也能够使用这些属性，但您不希望这些属性对您的库的实现代码可见，您可以将该库的属性设置为INTERFACE。

使用这些属性，您可以更好地控制您的目标对其他目标的可见性和使用方式。例如，当您将一个库链接到您的应用程序时，您可以将该库的属性设置为PRIVATE，以确保其他目标无法使用该库的实现代码。或者，当您将一个库作为接口库提供时，您可以将该库的属性设置为INTERFACE，以便其他目标也能够使用该库的头文件和链接库

## INTERFACE 详解

在CMake中，INTERFACE是目标属性的一个关键字，用于指定目标的接口属性。一个带有INTERFACE属性的目标通常被称为接口库（interface library）。

接口库是一种特殊类型的库，它不包含任何实现代码，只包含一些头文件和链接库，以及其他目标可以使用的一些属性。它的主要作用是为其他目标提供一个标准化的接口，以确保这些目标使用相同的头文件和链接库，并且避免重复的工作。

在一个接口库中，可以使用INTERFACE_INCLUDE_DIRECTORIES和INTERFACE_LINK_LIBRARIES等属性指定头文件路径和链接库。例如：

```cmake
add_library(my_interface_library INTERFACE)
target_include_directories(my_interface_library INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/include)
target_link_libraries(my_interface_library INTERFACE some_other_library)
```


这里，我们创建了一个名为my_interface_library的接口库，并使用target_include_directories和target_link_libraries函数分别指定了该库的头文件路径和链接库。这些属性被设置为INTERFACE，表示它们只能被其他目标使用，而不能被该库的实现代码使用。

当其他目标使用接口库时，可以通过target_link_libraries函数将接口库链接到该目标中，例如：

```cmake
add_executable(my_program main.cpp)
target_link_libraries(my_program PRIVATE my_interface_library)
```

这里，我们创建了一个名为my_program的可执行文件，并使用target_link_libraries函数将my_interface_library接口库链接到该文件中。因为my_interface_library是一个接口库，所以它不会增加my_program的大小，只是确保该文件使用了my_interface_library指定的头文件和链接库。

总之，使用INTERFACE属性可以创建一个标准化的接口库，为其他目标提供一个标准的接口，以确保这些目标使用相同的头文件和链接库，并且避免重复的工作。这可以使您的项目更加清晰、易于维护和扩展。
