---
layout: post
title: linux system() 函数使用的发散思考
categories: linux
description: 记录system()使用引入的问题
keywords: linux, network
---

**问题起源**

重启一个网络服务程序，但是本来应该监听的端口却未能正常监听，程序报错 *Bind failed: Address already in use*
导致这个报错的原因我所知的有两种。其一、端口资源被其他程序使用。其二、socket套接字状态处于 TIMEWAIT 状态，在此状态
下，套接字也会绑定失败。导致此状态的原因和 网络协议的通讯流程有关，在此不再赘述，[预留文章整理说明]()。

我遇到的是第一种情况，但是系统中只有一个程序会去使用端口，端口为什么会被占用呢？通过 netstat 查看端口使用情况，发现
是一个 shell 命令行程序占用，而这个 shell 命令行正是由那个网络服务程序通过 system() 函数调用。事情到这真相已经呼之欲出了。

**

