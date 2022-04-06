---
layout: post
title: gcc-11.2 编译
categories: gcc, build
description: 记录 gcc-11.2 的编译过程
keywords: gcc, build
---

## 编译环境

```shell
系统：ubuntu 20.4 x86_64
gcc 版本：gcc (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0
make 版本：GNU Make 4.2.1
```

## 下载源码包

[gcc ftp站点](http://ftp.gnu.org/gnu/gcc/)

```shell
wget http://ftp.gnu.org/gnu/gcc/gcc-11.2.0/gcc-11.2.0.tar.gz
```

## 编译准备

```shell
tar -zxvf gcc-11.2.0.tar.gz
cd gcc-11.2.0
./contrib/download_prerequisites
```

## 编译配置

```shell
mkdir build
cd build/
../configure --prefix=/opt/dev_src/gcc-11.2.0_ins -enable-checking=release -enable-languages=c,c++ -disable-multilib
#--prefix-指定安装路径
#–enable-languages表示你要让你的gcc支持那些语言，
#–disable-multilib不生成编译为其他平台可执行代码的交叉编译器。
#–disable-checking生成的编译器在编译过程中不做额外检查，
#也可以使用*–enable-checking=xxx*来增加一些检查
```

## 编译安装

```shell
#多线程编译
make -j16

#安装
make install 
```

## 更新动态库

```shell
#查看当前的动态库
strings /lib/x86_64-linux-gnu/libstdc++.so.6 | grep CXXABI
rm -f /lib/x86_64-linux-gnu/libstdc++.so.6
cp /opt/dev_src/gcc-11.2.0_ins/lib64/libstdc++.so.6.0.29 /lib/x86_64-linux-gnu
ln -s /lib/x86_64-linux-gnu/libstdc++.so.6.0.29 /lib/x86_64-linux-gnu/libstdc++.so.6
#查看更新后的动态库
strings /lib/x86_64-linux-gnu/libstdc++.so.6 | grep CXXABI
# 安装后的动态库会位于/usr/local/lib64目录下，
#其他版本在该目录下寻找对应的动态库libstdc++.so.6.X.XX
```

## 验证 gcc 版本

```shell
gcc ---version
```

## 编译问题解决

error:
`gcc: error: gengtype-lex.c: No such file or directory`

solution:
`apt-get install flex`


[编译问题解决](https://gibsonic.org/tools/2019/08/08/gcc_building.html)
