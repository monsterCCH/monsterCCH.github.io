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

### awk

文本分析工具

语法

```sh
awk [选项参数] 'script' var=value file(s)
或
awk [选项参数] -f scriptfile var=value file(s)
```

选项参数说明

* -F fs or --field-separator fs
  指定输入文件折分隔符，fs是一个字符串或者是一个正则表达式，如-F:。
* -v var=value or --asign var=value
  赋值一个用户定义变量。
* -f scripfile or --file scriptfile
  从脚本文件中读取awk命令。
* -mf nnn and -mr nnn
  对nnn值设置内在限制，-mf选项限制分配给nnn的最大块数目；-mr选项限制记录的最大数目。这两个功能是Bell实验室版awk的扩展功能，在标准awk中不适用。
* -W compact or --compat, -W traditional or --traditional
  在兼容模式下运行awk。所以gawk的行为和标准的awk完全一样，所有的awk扩展都被忽略。
* -W copyleft or --copyleft, -W copyright or --copyright
  打印简短的版权信息。
* -W help or --help, -W usage or --usage
  打印全部awk选项和每个选项的简短说明。
* -W lint or --lint
  打印不能向传统unix平台移植的结构的警告。
* -W lint-old or --lint-old
  打印关于不能向传统unix平台移植的结构的警告。
* -W posix
  打开兼容模式。但有以下限制，不识别：/x、函数关键字、func、换码序列以及当fs是一个空格时，将新行作为一个域分隔符；操作符**和**=不能代替^和^=；fflush无效。
* -W re-interval or --re-inerval
  允许间隔正则表达式的使用，参考(grep中的Posix字符类)，如括号表达式[[:alpha:]]。
* -W source program-text or --source program-text
  使用program-text作为源代码，可与-f命令混用。
* -W version or --version
  打印bug报告信息的版本。
  
  示范用例
  
  ```sh
  kill 所有进程名包含 program 的进程
  ps -ef | grep program | grep -v grep | awk -F' ' '{print $2}' |xargs kill -9
  ```

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

---

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

#### 使用 find 命令删除

```sh
find ./ -name '[^k][^e][^e][^p]*' -exec rm -rf {} \; 此命令效率高，因为 -exec 会启动
多个进程处理，而 xargs 会启动一个 rm 进程来处理 
find ./ -name '[^k][^e][^e][^p]*' | xargs rm -rf
```

