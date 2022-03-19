---
layout: post
title: spinlock vs mutex
categories: linux, 锁
description: spinlock 和 mutex 的区别及说明
keywords: spinlock, mutex
---

# 前言

当共享资源被多个线程访问时，为了保证资源的正确访问，通常使用锁机制来保证数据的正确访问，在进程上下文访问
时，可以使用 mutex 锁机制，但当数据被中断上下文时，导致睡眠的 lock 就不能被使用了，此时可以使用 spinlock.

# spinlock 说明

* 自旋锁是一种死等的锁机制，