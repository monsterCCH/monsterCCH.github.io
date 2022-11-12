---
layout: post
title: linux进程在32位和64位下的内存布局
categories: mem, linux
description: linux进程在32位和64位下的内存布局
keywords: mem, linux
---

## 概念
linux 为每个进程维护了一套单独的虚拟地址空间，以此来更好的管理物理内存，并使不同的进程不会彼此干扰。

## 32位linux进程内存空间经典布局

![](/images/posts/linux-mem-layout/linux32_mem_layout.png)
