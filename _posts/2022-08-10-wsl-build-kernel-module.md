---
layout: post
title: WSL 编译内核模块
categories: kernel, build
description: WSL 编译内核模块
keywords: kernel, build
---

## WSL operation

```shell
sudo apt install build-essential flex bison libssl-dev libelf-dev git dwarves
git clone https://github.com/microsoft/WSL2-Linux-Kernel.git
cd WSL2-Linux-Kernel
cp Microsoft/config-wsl .config
make -j $(expr $(nproc) - 1)
```

## windows operation

```shell
cp \\wsl$\<DISTRO>\home\<USER>\WSL2-Linux-Kernel\arch\x86\boot\bzimage 到 %userprofile%, 比如 C:\Users\<Windows_user>

新增文件
%userprofile%\.wslconfig

写入
[wsl2]
kernel=C:\\Users\\WIN10_USER\\bzimage
```

## powershell

```shell
wsl --shutdown
重新打开 wsl
```
