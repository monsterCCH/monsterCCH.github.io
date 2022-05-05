---
layout: post
title: 开发备忘录
categories: linux, os
description: 开发备忘录
keywords: linux, os
--

## linux下拷贝可执行程序依赖的所有动态库.so

```shell
#!/bin/bash

exe="$1"   #  可执行程序
des="$2"  #  存放位置

mkdir -p des

deplist=$(ldd $exe | awk  '{if (match($3,"/")){ printf("%s "),$3 } }')
cp $deplist $des
```