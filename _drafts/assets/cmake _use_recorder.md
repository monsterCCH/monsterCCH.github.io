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