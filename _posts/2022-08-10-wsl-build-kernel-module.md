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

## WSL 编写内核模块
```c
#include <linux/init.h>
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/moduleparam.h>

MODULE_LICENSE("GPL");

static int __init mod_init(void)
{
    printk(KERN_ALERT "Hello world\n");
    return 0;
}

static void __exit mod_exit(void)
{
    printk(KERN_ALERT "Goodbye\n");
}

module_init(mod_init);
module_exit(mod_exit);
```

```makefile
obj-m := firstmod.o

CURRENT_PATH := $(shell pwd)
VERSION_NUM := $(shell uname -r)
LINUX_PATH := /usr/src/linux-headers-$(VERSION_NUM)

all :
    make -C $(LINUX_PATH) M=$(CURRENT_PATH) modules

clean :
    make -C $(LINUX_PATH) M=$(CURRENT_PATH) clean
```

使用uname -r命令查看系统版本,下载对应版本源码

```shell
wget https://github.com/microsoft/WSL2-Linux-Kernel/archive/5.15.57.1-microsoft-standard.tar.gz
```

```shell
sudo apt-get install libelf-dev build-essential pkg-config
sudo apt-get install bison build-essential flex libssl-dev libelf-dev bc
```

```shell
sudo tar xvzf 5.15.57.1-microsoft-standard.tar.gz
cd WSL2-Linux-Kernel-5.15.57.1-microsoft-standard
zcat /proc/config.gz > .config
make -j $(nproc)  #Compile the kernel and modules
sudo make -j $(nproc) modules_install
```

创建软链接

```shell
ln -s {PATH}/WSL2-Linux-Kernel-5.15.57.1-microsoft-standard /usr/src/linux-headers-5.15.57.1-microsoft-standard
```

## 参考资料

[WSL2下增删Linux内核模块](https://codeantenna.com/a/pjvcQ78Glo)
