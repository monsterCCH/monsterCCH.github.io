---
layout: wiki
title: docker
categories: docker
description: docker 常用操作记录。
keywords: docker, 容器
---

### 启动所有已经存在的容器
```shell
docker start $(docker ps -a -q --filter "status=exited")  
```
### 进入容器
```shell
docker exec -it e8771c7a7151 /bin/bash
```

### 和容器传递文件
from container to host
```shell
docker cp 容器名：要拷贝的文件在容器里面的路径  要拷贝到宿主机的相应路径   
eg: docker cp -a fe5282e395eb:/demo_ws/ /home/sz/code/
```

from host to container
```shell
docker  cp  要拷贝的文件路径    容器名:要拷贝到容器里面对应的路径
eg：docker cp a.txt 47683ef9b01e:/demo_ws/a.txt
```
不管容器有没有启动，拷贝命令都会生效。

### docker中僵尸进程的处理
```shell
# 添加 /bin/bash 作为 init 进程回收僵尸进程
docker run -itd --name centos7 centos:latest /bin/bash
```
[在docker中出现的僵尸进程怎么处理](https://www.modb.pro/db/81444)
