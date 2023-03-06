---
layout: post
title: Linux 内核开发环境搭建
categories: kernel, linux, env
description: Linux 内核开发环境搭建
keywords: qemu, busybox, bzImage
---

## 环境准备
```shell
apt-get update
apt-get install git fakeroot build-essential ncurses-dev xz-utils qemu flex libncurses5-dev fakeroot build-essential ncurses-dev xz-utils libssl-dev bc bison libglib2.0-dev libfdt-dev libpixman-1-dev zlib1g-dev libelf-dev dwarves
```

## 获取内核镜像
* 内核源码编译
* 下载现有内核镜像
* 使用系统自身镜像

### 内核源码编译
#### 获取内核源码

在 [Linux Kernel Archive](https://www.kernel.org/) 中下载对应内核版本

国内站点
[aliyun](https://mirrors.aliyun.com/linux-kernel/)

`wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.10.165.tar.xz`

or

`wget https://mirrors.aliyun.com/linux-kernel/v5.x/linux-5.10.165.tar.xz?spm=a2c6h.25603864.0.0.14de4823vTOQKS -O linux-5.10.165.tar.xz`

#### 解压配置内核选项
```shell
tar xvf linux-5.10.165.tar.xz
cd linux-5.10.165.tar.xz; make menuconfig
```
配置内核以下选项

* Kernel hacking —> Kernel debugging 默认开启
* Kernel hacking —> Compile-time checks and compiler options —> Compile the kernel with debug info 默认开启
* Kernel hacking —> Generic Kernel Debugging Instruments –> + + KGDB: kernel debugger 默认开启
* kernel hacking —> Compile the kernel with frame pointers 默认开启
* Processor type and features —> Randomize the address of the kernel image 关闭地址随机化，否则断点无法停止

可选调试配置项
```text
 Kernel hacking  --->      

[*]   Magic SysRq key 

[*]   Kernel debugging 

[*]   Debug slab memory allocations  

[*]   Spinlock and rw-lock debugging: basic checks 

[*]   Spinlock debugging: sleep-inside-spinlock checking 

           [*]   Compile the kernel with debug info   

Device Drivers  --->   

           Generic Driver Options  ---> 

           [*]   Driver Core verbose debug messages 

General setup  ---> 

           [*]   Configure standard kernel features (for small systems)  ---> 

           [*]   Load all symbols for debugging/ksymoops
```
* slab layer debugging（slab层调试选项）内核内存分配由 slab 转为 slub, 选项开启为 SLUB_DEBUG
* high-memory debugging（高端内存调试选项）
* I/O mapping debugging（I/O映射调试选项）
* spin-lock debugging（自旋锁调试选项）
* stack-overflow checking（栈溢出检查选项）
* sleep-inside-spinlock checking（自旋锁内睡眠选项）

#### 编译
```shell
# 编译内核主体
make bzImage -j16

# 编译内核模块
make modules -j16
# 去除 debug 调试信息
make INSTALL_MOD_STRIP=1 modules_install
# 编译成 deb 包
make INSTALL_MOD_STRIP=1 deb-pkg
```

编译报错解决
```shell
如果报错 canonical-certs.pem：
如下：

make[1]: *** No rule to make target 'debian/canonical-certs.pem', needed by 'certs/x509_certificate_list'.  Stop.
make: *** [Makefile:1868: certs] Error 2
在命令行中执行：

scripts/config --disable SYSTEM_TRUSTED_KEYS

如果报错 canonical-revoked-certs.pem：
如下：

make[1]: *** No rule to make target 'debian/canonical-revoked-certs.pem', needed by 'certs/x509_revocation_list'.  Stop.
make: *** [Makefile:1868: certs] Error 2
在命令行中执行：

scripts/config --disable SYSTEM_REVOCATION_KEYS
```

#### 安装内核

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
# 状态为deinstall即为已卸载，去除配置文件可以使用 apt purge 删除，之后update-grub 即可(根据情况选择grub/grub2)

# 查看启动顺序，即内核选用的优先级顺序
grep menuentry /boot/grub/grub.cfg

# 确定默认使用需要的那个版本的内核，修改前记得先备份
sed -i 's/5.4.0-109-generic/5.4.178/g' /boot/grub/grub.cfg
```

### 下载现有内核镜像
```shell
apt search linux-image-
#选择对应的内核镜像安装
```

### 使用本地镜像
```shell
一般位于 /boot 目录下
```

## 使用 busybox 构建文件系统
### 获取busybox源码
在[busybox.net](https://busybox.net/downloads/)下载自己想要的版本，这里选用busybox-1.33.0.tar.bz2这个版本
```shell
wget https://busybox.net/downloads/busybox-1.33.0.tar.bz2
tar -jxvf busybox-1.33.0.tar.bz2
make menuconfig
#勾选Settings —> Build static binary file (no shared lib)
make install
```
编译完成后会生成一个_install目录，接下来我们将会用它来构建我们的磁盘镜像

### 建立文件系统
#### 初始化文件系统
```shell
cd _install
mkdir -pv {bin,sbin,etc,proc,sys,home,lib64,lib/x86_64-linux-gnu,usr/{bin,sbin}}
touch etc/inittab
mkdir etc/init.d
touch etc/init.d/rcS
chmod +x ./etc/init.d/rcS
```

#### 配置初始化脚本
配置 etc/inttab,写入如下内容:
```text
::sysinit:/etc/init.d/rcS
::askfirst:/bin/ash
::ctrlaltdel:/sbin/reboot
::shutdown:/sbin/swapoff -a
::shutdown:/bin/umount -a -r
::restart:/sbin/init
```
配置etc/init.d/rcS,写入如下内容: 
```text
#!/bin/sh
mount -t proc none /proc
mount -t sys none /sys
/bin/mount -n -t sysfs none /sys
/bin/mount -t ramfs none /dev
/sbin/mdev -s
```

#### 配置用户组
```shell
echo "root:x:0:0:root:/root:/bin/sh" > etc/passwd
echo "ctf:x:1000:1000:ctf:/home/ctf:/bin/sh" >> etc/passwd
echo "root:x:0:" > etc/group
echo "ctf:x:1000:" >> etc/group
echo "none /dev/pts devpts gid=5,mode=620 0 0" > etc/fstab
```

#### 打包文件系统
```shell
# 打包文件系统
find . | cpio -o --format=newc > ../../rootfs.cpio
# 解压文件系统
cpio -idv < ./rootfs.cpio
```

## 配置启动脚本
```shell
#!/bin/sh
qemu-system-x86_64 \
    -m 128M \
    -kernel ./bzImage \
    -initrd  ./rootfs.cpio \
    -monitor /dev/null \
    -append "root=/dev/ram rdinit=/sbin/init console=ttyS0 oops=panic panic=1 loglevel=3 quiet nokaslr" \
    -cpu kvm64,+smep \
    -smp cores=2,threads=1 \
    -netdev user,id=t0, -device e1000,netdev=t0,id=nic0 \
    -nographic \
    -s
```
实际使用脚本
```shell
#!/bin/sh
#root=/dev/ram
#-monitor /dev/null \
qemu-system-x86_64 \
    -m 128M \
    -kernel ./bzImage \
    -initrd  ./rootfs.cpio \
    -append "rdinit=/sbin/init console=ttyS0 oops=panic panic=1 loglevel=3 quiet nokaslr" \
    -cpu kvm64,+smep \
    -smp cores=2,threads=1 \
    -netdev user,id=t0, -device e1000,netdev=t0,id=nic0 \
    -nographic \
    -s
```

```text
这里文件名使用boot.sh
部分参数说明如下：

-m：虚拟机内存大小
-kernel：内存镜像路径
-initrd：磁盘镜像路径
-append：附加参数选项
nokalsr：关闭内核地址随机化，方便我们进行调试
rdinit：指定初始启动进程，/sbin/init进程会默认以/etc/init.d/rcS作为启动脚本
loglevel=3 & quiet：不输出log
console=ttyS0：指定终端为/dev/ttyS0，这样一启动就能进入终端界面
-monitor：将监视器重定向到主机设备/dev/null，这里重定向至null主要是防止CTF中被人给偷了qemu拿flag
-cpu：设置CPU安全选项，在这里开启了smep保护
-s：相当于-gdb tcp::1234的简写（也可以直接这么写），后续我们可以通过gdb连接本地端口进行调试
-S: 启动就挂起，等待 gdb 连接
```

## 内核函数调试
启动gdb远程调试。vmlinux文件在编译后的内核源码根目录下
```shell
gdb vmlinux
(gdb) target localhost:1234
(gdb) b new_sync_read # 打断点
Breakpoint 1 at 0xffffffff813596b0: file fs/read_write.c, line 405.
(gdb) c
Continuing.

Thread 1 hit Breakpoint 1, new_sync_read (filp=filp@entry=0xffff888005942dc0, buf=buf@entry=0x7ffd58ac0ea9 "\211X", len=1, ppos=ppos@entry=0xffffc90000433ef0) at fs/read_write.c:405
405     {
(gdb)
```

## 添加共享磁盘
有时候需要在宿主机和qemu虚拟机之间共享文件，添加一个共享磁盘将有助于该项工作。

创建64MB磁盘镜像文件，并格式化为ext4，作为共享磁盘备用。
```shell
dd if=/dev/zero of=ext4.img bs=512 count=131072
mkfs.ext4 ext4.img
```

修改qemu启动命令，使用-hdb增加一个磁盘。
```shell
qemu-system-x86_64 \
    -m 128M \
    -kernel ./bzImage \
    -initrd  ./rootfs.cpio \
    -append "rdinit=/sbin/init console=ttyS0 oops=panic panic=1 loglevel=3 quiet nokaslr" \
    -cpu kvm64,+smep \
    -smp cores=2,threads=1 \
    -netdev user,id=t0, -device e1000,netdev=t0,id=nic0 \
    -hdb ./ext4.img \
    -nographic \
    -s
qemu-system-x86_64 -kernel ~/linux-4.14.191/arch/x86_64/boot/bzImage  -hda ~/busybox-1.32.0/rootfs.img  -append "root=/dev/sda console=ttyS0" -s  -smp 1 -nographic -hdb ~/shadisk/ext4.img
```

进入qemu系统后使用mount命令挂载sdb到mnt目录。
```shell
mount /dev/sda /mnt
```

在原系统中挂载ext4.img，实现qemu与原系统的文件共享。**非实时同步**
```shell
mount -t ext4 -o loop ext4.img ./share
```
