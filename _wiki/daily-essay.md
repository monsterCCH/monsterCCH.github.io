---
layout: wiki
title: 日常记录随笔
categories: essay
description: 常记录随笔
keywords: essay
---

## 编译 32 位程序
### 安装 gcc32 位编译库
```shell
apt install gcc-multilib
```
### 编译时添加 -m32 标志
```shell
gcc -m32 test.c
```
