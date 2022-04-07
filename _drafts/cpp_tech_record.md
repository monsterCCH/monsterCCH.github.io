---
layout: post
title: linux&cpp 技术记录
categories: cpp, linux
description: linux&cpp 技术记录
keywords: cpp, linux, build
---

##  Linux lib 库相互依赖，交叉引用的解决方法

```c++
如果 lib a 依赖 b, b 又依赖 a，则链接顺序应该为 -la -lb -la
```