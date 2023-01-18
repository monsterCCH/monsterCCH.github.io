---
layout: post
title: 动态输出 linux 内核调试信息
categories: kernel, linux
description: 动态输出 linux 内核调试信息
keywords: debugfs,dynamic_debug
---

## 介绍
printk()是很多嵌入式开发者喜欢用的调试手段之一，但是，使用printk()每次都要重新编译内核，很不方便。使用动态输出在不需要重新编译内核的情况下，方便的打印出内核的debug信息，并且可以动态选择打开某个内核子系统的输出，可以有选择性地打开某些模块的输出。

要开启动态输出，内核需要添加CONFIG_DYNAMIC_DEBUG。开启宏之后，pr_debug()，dev_dbg() ，print_hex_dump_debug()，print_hex_dump_bytes()`所有信息都可以被动态打印出来。

ps: 查看内核配置
```shell
zcat /proc/config.gz
cat /usr/src/linux/linux-3.10.0-1160.el7/.config
```


## 参考
[Linux内核基础篇——动态输出调试](https://zhuanlan.zhihu.com/p/592768166)

[使用动态输出打印内核的DEBUG信息](https://mp.weixin.qq.com/s/JL-_TWX5i86c42T8TDYT5A)
