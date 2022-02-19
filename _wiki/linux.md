---
layout: wiki
title: Linux/Unix
categories: Linux
description: 类 Unix 系统下的一些常用命令和用法。
keywords: Linux
---

类 Unix 系统下的一些常用命令和用法。

## 实用命令

### chown

更改文件所有者和组。

比如某文件夹当前所有者是 root，需要更改为 admin：

```sh
chown -R admin:admin test
```

### chmod

修改文件权限。

### fuser

查看文件被谁占用。

```sh
fuser -u .linux.md.swp
```

### id

查看当前用户、组 id。

### lsof

查看打开的文件列表。

> An  open  file  may  be  a  regular  file,  a directory, a block special file, a character special file, an executing text reference, a library, a stream or a network file (Internet socket, NFS file or UNIX domain socket.)  A specific file or all the files in a file system may be selected by path.

#### 查看网络相关的文件占用

```sh
lsof -i
```

#### 查看端口占用

```sh
lsof -i tcp:5037
```

#### 查看某个文件被谁占用

```sh
lsof .linux.md.swp
```

#### 查看某个用户占用的文件信息

```sh
lsof -u mazhuang
```

`-u` 后面可以跟 uid 或 login name。

#### 查看某个程序占用的文件信息

```sh
lsof -c Vim
```

注意程序名区分大小写。

## 使用场景

### 删除文件排除文件或目录

#### extglob模式

extglob 打开后 shell 可以识别五个模式匹配操作符，使文件匹配更方便

`shopt -s extglob 开启`

`shopt -u extglob 关闭`

开启后可以识别以下五个模式匹配操作符

```regex
?(pattern-list) - 所给模式匹配0次或1次；

 *(pattern-list) - 所给模式匹配0次以上包括0次；

 +(pattern-list) - 所给模式匹配1次以上包括1次；

 @(pattern-list) - 所给模式仅仅匹配1次；

 !(pattern-list) - 不匹配括号内的所给模式。
```

#### 反选删除文件

```sh
shopt -s extglob
rm -rf !(file1)
rm -rf !(file1|file2)
```

#### 结合 grep 和 xargs 删除

```sh
ls | grep -v file1 | xargs rm
```

#### 使用 fine 命令删除

```sh
find ./ -name '[^k][^e][^e][^p]*' -exec rm -rf {} \; 此命令效率高，因为 -exec 会启动
多个进程处理，而 xargs 会启动一个 rm 进程来处理 
find ./ -name '[^k][^e][^e][^p]*' | xargs rm -rf
```

