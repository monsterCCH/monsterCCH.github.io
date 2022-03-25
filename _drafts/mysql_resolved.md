---
layout: post
title: mysql 问题记录
categories: mysql
description: 记录 mysql 使用中遇到的问题
keywords: mysql
---

## Table ‘.\xxx\xxx‘ is marked as crashed and should be repaired

解决方案：
```sql
# 进入命令行
mysql -u[用户名] -p[密码]

# 切换数据库
use [库名];

# 检查出现问题的表
check table [表名]

# 修复表
repair table [表名]

```
or
```sql
# 备份数据库
mysqldump -u root -p aaa > qqtexas.sql
Enter password:

# 添加 --auto-repair 参数自动修复 
mysqlcheck -u root -p aaa --auto-repair
Enter password:
```