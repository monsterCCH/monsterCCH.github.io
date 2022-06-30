---
layout: post
title: linux kernel 笔记
categories: kernel、linux
description: linux kernel 笔记
keywords: kernel、linux
---

## windows clion clone 报错

使用 Windows 环境下的 clion clone linux 内核项目

`git clone https://github.com/torvalds/linux.git`

### 报错

```shell
invalid path 'drivers/gpu/drm/nouveau/nvkm/subdev/i2c/aux.c' unable to checkout working tree warning: Clone succeeded, but checkout failed. You can inspect what was checked out with 'git status' and retry with 'git restore --source=HEAD :/'
```

### 原因
aux是windows保留字段，不允许用改名字给文件取名

### 解决方案

```shell
git config core.protectNTFS false
git reset --hard HEAD
```