---
layout: wiki
title: MySQL
categories: [MySQL, Database]
description: MySQL Wiki
keywords: MySQL
---

## 使用

### 启动与停止

macOS

```
mysql.server start
mysql.server stop
mysqladmin -u root -p shutdown
mysql -u root -p
```

Windows（使用管理员权限的 CMD）

```
net start mysql
net stop mysql
```

### 连接

```
mysql -u 用户名 -p密码 -h 服务器IP地址 -P 服务器端MySQL端口号 -D 数据库名
```

### 授权

授权远程登录

```
grant 权限1,权限2,…权限n on 数据库名称.表名称 to 用户名@用户地址 identified by '连接口令';
```

### 修改密码

**方法1**： 用SET PASSWORD命令

```
mysql -u root
mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass');
```

**方法2**：用 mysqladmin

```
mysqladmin -u root password "newpass"
```

如果root已经设置过密码，采用如下方法

```
mysqladmin -u root password oldpass "newpass"
```

**方法3**： 用UPDATE直接编辑user表

```
mysql -u root
mysql> use mysql;
mysql> UPDATE user SET Password = PASSWORD('newpass') WHERE user = 'root';
mysql> FLUSH PRIVILEGES;
```

在丢失root密码的时候，可以这样

```
mysqld_safe --skip-grant-tables&
mysql -u root mysql
mysql> UPDATE user SET password=PASSWORD("new password") WHERE user='root';
mysql> FLUSH PRIVILEGES;
```

### 命令行执行脚本

```
mysql -u root -p111111 -Dtest < test.sql
```

### 其它问题

MySQL 5.7 在 Windows 下安装后无法直接运行，需要步骤：

1. mysqld install
2. mysqld --initialize-insecure 自动生成无密码的 root 用户或 mysqld --initialize 自动生成带随机密码的 root 用户
3. net start mysql
4. Mysql -u root

## 常用指令

### 查看

```sql
-- 查看表的创建语句
desc tb_name;
show create table tb_name;
-- 查看表的索引
show index from table_name;
-- 显示数据库列表
show databases; 
```

### 修改密码 mysql 8.0

```sql
show databases;
use mysql;
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';
```

### 修改用户远程登录权限

```sql
update user set host='%' where user = 'root';
select host from user where user = 'root';
flush privileges;
```

### 修改

```sql
-- 修改列属性
alter table tb_name modify column_name int auto_increment;
```

（如果是修改主键，不能带 primary key，不然会报 ERROR 1068 (42000): Multiple primary key defined）

### dump

```sql
-- dump 库
mysqldump -u root -p db_name > 1.txt
-- dump 表
mysqldump -u root -p db_name tb_name > 1.txt
-- dump 表不带数据
mysqldump -u root -p --no-date db_name tb_name > 1.txt
```

### 数据库状态

```sql
show engine INNODB status;
```

脏页相关

```sql
-- flush 脏页时是否刷新邻居
show VARIABLES  like 'innodb_flush_neighbors';
-- 设置该值
set global innodb_flush_neighbors = 0;

-- 查看脏页比例
select VARIABLE_VALUE into @a from PERFORMANCE_SCHEMA.global_status where VARIABLE_NAME = 'Innodb_buffer_pool_pages_dirty';
select VARIABLE_VALUE into @b from PERFORMANCE_SCHEMA.global_status where VARIABLE_NAME = 'Innodb_buffer_pool_pages_total';
select @a/@b;
```

### 磁盘空间相关

查看数据库内磁盘占用空间：

```sql
SELECT
table_name,
    TABLE_SCHEMA,
    ( DATA_LENGTH + INDEX_LENGTH + DATA_FREE ) / 1024 / 1024 MB,
    TABLE_ROWS
    FROM
    information_schema. TABLES
    WHERE
    TABLE_SCHEMA NOT IN ('information_schema','mysql')
    ORDER BY
    MB DESC
    LIMIT 0,
    50
```

查看表中数据、索引、已分配给表但没有使用空间：

```sql
SELECT
table_name,
    TABLE_SCHEMA,
    DATA_LENGTH/ 1024 / 1024 _DATA, INDEX_LENGTH/ 1024 / 1024 _INDEX, DATA_FREE/ 1024 / 1024 _DATA_FREE,
    TABLE_ROWS
    FROM
    information_schema. TABLES
    WHERE
    TABLE_SCHEMA NOT IN ('information_schema','mysql')
    ORDER BY
    _DATA DESC
    LIMIT 0,
    50
```

查看数据库的所有表数据大小排序

```shell
SELECT
	TABLE_NAME,
	DATA_LENGTH + INDEX_LENGTH,
	TABLE_ROWS,
	concat(round((DATA_LENGTH + INDEX_LENGTH) / 1024 / 1024,2),'MB') AS DATA
FROM
	information_schema. TABLES
WHERE
	TABLE_SCHEMA = 'ABC' -- 数据库名
ORDER BY
	DATA + 0 DESC;
```

查看数据库的文件大小：

```sql
SELECT file_name, concat(TOTAL_EXTENTS,'M') as 'FIle_size' FROM INFORMATION_SCHEMA.FILES order by TOTAL_EXTENTS DESC
```

### 阻塞问题排查/解决

```sql
-- 查看有哪些表是打开的
show open tables -- where in_use > 0;
-- 查看进程
show [full] processlist;
-- 查看 Sending data 状态的进程
select * from information_schema.`PROCESSLIST` where db = 'db_name' and state = 'Sending data' order by time desc;
-- 批量生成 kill <pid>; 语句
select concat("kill ", id, ";") from information_schema.`PROCESSLIST` where db = 'db_name' and state = 'Sending data' order by time desc;
-- 批量生成 kill <pid>; 语句并将结果集写到文件
select concat("kill ", id, ";") from information_schema.`PROCESSLIST` where db = 'db_name' and state = 'Sending data' order by time desc into outfile '/tmp/a.txt';
-- 执行文件
source /tmp/a.txt

-- 杀掉指定进程
kill <pid>;
-- 查看正在锁的事务
select * from information_schema.INNODB_LOCKS;
-- 查看等待的事务
select * from information_schema.INNODB_LOCK_WAITS;
-- 查看线程相关数量
show global status like 'Thread%'
-- 查看缓存线程数配置
show VARIABLES like 'thread_cache_size'
```

### SQL 技巧

```sql
-- 在原值后面附加内容，以值的最右几位为条件
update table_name set col1 = concat(col1, 'xxx') where right(col1, 1) = '?';
-- limit 示例
SELECT * FROM Orders LIMIT 30;
SELECT * FROM Orders LIMIT 10 OFFSET 15; -- 第 16 到第 25 个结果
SELECT * FROM Orders LIMIT 15, 10; -- 等价于上面
```

#### 判断字段值 Value 为空

```sql
update t_wxuser a set a.updatetime=a.subscribe_time where a.updatetime IS NULL
```

### 查询结果去重

* 对一个字段去重

```sql
select distinct key from table;
```

### 模糊查询

`SELECT 字段 FROM 表 WHERE 某字段 Like 条件`

```sql
LIKE'Mc%' 将搜索以字母 Mc 开头的所有字符串（如 McBadden）。
LIKE'%inger' 将搜索以字母 inger 结尾的所有字符串（如 Ringer、Stringer）。
LIKE'%en%' 将搜索在任何位置包含字母 en 的所有字符串（如 Bennet、Green、McBadden）。
LIKE'_heryl' 将搜索以字母 heryl 结尾的所有六个字母的名称（如 Cheryl、Sheryl）。
LIKE'[CK]ars[eo]n' 将搜索下列字符串：Carsen、Karsen、Carson 和 Karson（如 Carson）。
LIKE'[M-Z]inger' 将搜索以字符串 inger 结尾、以从 M 到 Z 的任何单个字母开头的所有名称（如 Ringer）。
LIKE'M[^c]%' 将搜索以字母 M 开头，并且第二个字母不是 c 的所有名称（如MacFeather）
```

