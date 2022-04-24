---
layout: post
title: linux 内核编译安装
categories: kernel、linux
description: linux 内核编译安装
keywords: kernel、linux、build
---

## 编译环境

CPU: AMD Ryzen 7 5800X 8-Core Processor

OS: Linux ubuntu.server 5.4.0-109-generic #123-Ubuntu SMP  x86_64 GNU/Linux

MEM: 8Gb

## 源码包下载

```shell
apt search linux-source
#linux-source/focal-updates,focal-security 5.4.0.109.113 all
  #Linux kernel source with Ubuntu patches

#linux-source-5.4.0/focal-updates,focal-security,now 5.4.0-109.123 all [installed]
  #Linux kernel source for version 5.4.0 with Ubuntu patches
  
# 选择对应的内核源码安装
apt install linux-source-5.4.0
# 安装目录默认为 /usr/src

# 解压源码到自定义目录
tar xvf linux-source-5.4.0.tar.bz2 -C /opt/kernel
```

## 编译依赖安装

`apt install bison flex libssl-dev libelf-dev libncurses5-dev zstd dwarves`

## 编译

### 配置清理

```shell
make mrproper
```

### 应用当前内核配置文件

```shell
# 复制当前内核配置
cp /boot/config-`uname -r` ./.config

# 配置内核
make menuconfig
# ←和→选择“Load”， 选择刚刚复制的.config文件
# load	→	(.config)→	OK	→	SAVE	→	(.config)	→	OK	→	EXIT	→	EXIT
```

### 编译内核
```shell
# 编译内核主体
make bzImage -j16
# 消耗时长一分钟

# 编译内核模块
make modules -j16
# 去除 debug 调试信息
make INSTALL_MOD_STRIP=1 modules_install
# 编译成 deb 包
make INSTALL_MOD_STRIP=1 deb-pkg

# 消耗时长二十分钟
```

### 编译报错参考

```shell
make[1]: *** No rule to make target 'debian/canonical-certs.pem', needed by 'certs/x509_certificate_list'.  Stop.
make: *** [Makefile:1868: certs] Error 2

执行 scripts/config --disable SYSTEM_TRUSTED_KEYS

make[1]: *** No rule to make target 'debian/canonical-revoked-certs.pem', needed by 'certs/x509_revocation_list'.  Stop.
make: *** [Makefile:1868: certs] Error 2

执行 scripts/config --disable SYSTEM_REVOCATION_KEYS
```

### 安装内核

```shell
# 安装内核模块
make modules_install

# 安装内核
make install
# or
mkinitramfs /lib/modules/内核版本号 -o /boot/initrd.img-内核版本号-generic
cp /usr/src/linux-版本号/arch/x86/boot/bzImage    /boot/vmlinuz-版本号-generic
cp /usr/src/linux-版本号/System.map    /boot/System.map-版本号
update-grub2

# 验证测试
cat /boot/grub/grub.cfg
```

### 切换内核版本

```shell
# 查看当前使用的内核版本
uname -r

# 查看系统当前安装有哪些版本的内核
dpkg --get-selections | grep linux

# 查看启动顺序，即内核选用的优先级顺序
grep menuentry /boot/grub/grub.cfg

# 确定默认使用需要的那个版本的内核，修改前记得先备份
sed -i 's/5.4.0-109-generic/5.4.178/g' /boot/grub/grub.cfg
```