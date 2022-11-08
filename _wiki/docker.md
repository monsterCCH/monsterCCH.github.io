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
