---
layout: wiki
title: 环境配置相关
categories: env
description: 开发相关的环境依赖工具
keywords: pkg_config
---

## pkg_config
### 简介
软件开发过程中会使用到很多第三方依赖库，如果手动指定依赖库的头文件和库位置会相当麻烦。pkg_config 提供一个标准可以快速查找相关库的头文件和依赖库。

### 常用命令
```shell
# 查找 redis 的头文件
❯ pkg-config hiredis --cflags
-D_FILE_OFFSET_BITS=64 -I/usr/include/hiredis
# 查找 redis 的链接库
❯ pkg-config hiredis --libs
-lhiredis
# 同时
pkg-config hiredis --cflags --libs
```

## 自定义.pc
pkg_config 通过.pc 的配置文件来查找库相关文件，默认查找路径为 PKG_CONFIG_PATH 环境变量。可以自定义查找路径
`export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig`

### 模板
按照库名定义.pc文件如 hiredis.pc

```text
prefix=/opt/comm_lib/hiredis_cluster/src/hiredis_cluster/build/install
install_libdir=lib
exec_prefix=${prefix}
libdir=${exec_prefix}/${install_libdir}
includedir=${prefix}/include
pkgincludedir=${includedir}/hiredis

Name: hiredis # 库名 必填
Description: Minimalistic C client library for Redis. # 描述 必填
Version: 1.0.0 # 版本 必填
Libs: -L${libdir} -lhiredis # 链接标志 必填
Cflags: -I${pkgincludedir} -D_FILE_OFFSET_BITS=64 # 头文件指定 必填
```
