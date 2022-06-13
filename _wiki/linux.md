---
layout: wiki
title: Linux/Unix
categories: Linux
description: 类 Unix 系统下的一些常用命令和用法。
keywords: Linux
---
类 Unix 系统下的一些常用命令和用法。

## 实用命令

### screen

screen为多重视窗管理程序，用于在一个终端中创建多个会话窗口

常用命令

```shell
# 创建新会话
screen -S [name]
C + a + c

# 查看会话
C + a + w
0$ bash  1-$ bash  2*$ bash
# * 表示当前位于的窗口， - 表示上次处于的窗口
screen -ls

# 会话的分离与恢复
分离
screen -d [id]
OR
C + a + d
恢复
screen -r [id]

# 清除死掉的会话
screen -wipe
```

### dd

转换复制文件

实用命令

```shell
# 测试磁盘写入速度
dd if=/dev/zero bs=1024 count=1000000 of=/root/1Gb.file
# 测试磁盘读取速度
dd if=/root/1Gb.file bs=64k | dd of=/dev/null


```

### time

粗略统计程序执行时的耗时，通常输出三个值 real time、user time 和 sys time

* real time

  * 概念：程序从开始到结束所经历的时间，也就是用户所感受到的时间。包括当前程序CPU的用时和所有延迟程序执行的因素的耗时总和（比如其他程序耗时，等待I/O完成耗时等）
  * 来源：real time是由gettimeofday()中结束时间与开始时间相减得来。
* user time

  * 概念：程序执行过程中在用户空间（user space）中所花费的所有时间，即程序用户模式下的CPU耗时。仅指当前进程。其他进程的时间和当前进程I/O阻塞的时间均不计在内。
  * 来源：user time是由wait()或times()系统调用得来。
* sys time

  * 概念：程序执行过程中内核空间（kernel space）中所花费的时间，即程序在内核调用中的CPU耗时。仅指当前进程。程序的库代码调用仍然是在用户空间下。
  * 来源：sys time是由wait()或times()系统调用得来。

### 设置 root 密码

```sh
sudo passwd root
```

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

### nohub

nohup (no hang up)，用于在系统后台不挂断地运行命令，退出终端不会影响程序的运行。

```sh
# 在后台执行 root 目录下的 runoob.sh 脚本，并重定向输入到 runoob.log 文件
nohup /root/runoob.sh > runoob.log 2>&1 &

2>&1 解释：

将标准错误 2 重定向到标准输出 &1 ，标准输出 &1 再被重定向输入到 runoob.log 文件中。

0 – stdin (standard input，标准输入)
1 – stdout (standard output，标准输出)
2 – stderr (standard error，标准错误输出)
```

### watch

命令格式:
watch[参数][命令]

命令功能：
将命令的输出结果输出到标准输出设备，多用于周期性执行命令/定时执行命令

命令参数：

-n或--interval  watch缺省每2秒运行一下程序，可以用-n或-interval来指定间隔的时间。

-d或--differences  用-d或--differences 选项watch 会高亮显示变化的区域。 而-d=cumulative选项会把变动过的地方(不管最近的那次有没有变动)都高亮显示出来。

-t 或-no-title  会关闭watch命令在顶部的时间间隔,命令，当前时间的输出。

样例：

```sh
#每 10s 输出一次系统负载
watch -n 10 -d 'cat /proc/loadavg'
```

### pstree 以树状图展示进程间关系

语法

`pstree [选项]`

选项

```shell
-a：显示每个程序的完整指令，包含路径，参数或是常驻服务的标示；
-c：不使用精简标示法；
-G：使用VT100终端机的列绘图字符；
-h：列出树状图时，特别标明现在执行的程序；
-H <程序识别码>：此参数的效果和指定"-h"参数类似，但特别标明指定的程序；
-l：采用长列格式显示树状图；
-n：用程序识别码排序。预设是以程序名称来排序；
-p：显示程序识别码；
-u：显示用户名称；
-U：使用UTF-8列绘图字符；
-V：显示版本信息。
```

样例

```shell
pstree [pid] -a -p -l
```

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

### linux 查看进程/线程

```shell
ps -T -p <pid>
top -H -p <pid>
```

### linux 环境变量设置

```shell
#在PATH中找到可执行文件程序的路径。
export PATH =$PATH:$HOME/bin

#gcc找到头文件的路径
C_INCLUDE_PATH=/usr/include/libxml2:/MyLib
export C_INCLUDE_PATH

#g++找到头文件的路径
CPLUS_INCLUDE_PATH=$CPLUS_INCLUDE_PATH:/usr/include/libxml2:/MyLib
export CPLUS_INCLUDE_PATH

#找到动态链接库的路径
LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/MyLib
export LD_LIBRARY_PATH

#找到静态库的路径
LIBRARY_PATH=$LIBRARY_PATH:/MyLib
export LIBRARY_PATH
```

### update-alternatives 软件版本切换

语法：

```shell
root@ubuntu:~# update-alternatives --help
Usage: update-alternatives [<option> ...] <command>

Commands:
  --install <link> <name> <path> <priority>
    [--slave <link> <name> <path>] ...
                           add a group of alternatives to the system.
  --remove <name> <path>   remove <path> from the <name> group alternative.
  --remove-all <name>      remove <name> group from the alternatives system.
  --auto <name>            switch the master link <name> to automatic mode.
  --display <name>         display information about the <name> group.
  --query <name>           machine parseable version of --display <name>.
  --list <name>            display all targets of the <name> group.
  --get-selections         list master alternative names and their status.
  --set-selections         read alternative status from standard input.
  --config <name>          show alternatives for the <name> group and ask the
                           user to select which one to use.
  --set <name> <path>      set <path> as alternative for <name>.
  --all                    call --config on all alternatives.

<link> is the symlink pointing to /etc/alternatives/<name>.
  (e.g. /usr/bin/pager)
<name> is the master name for this link group.
  (e.g. pager)
<path> is the location of one of the alternative target files.
  (e.g. /usr/bin/less)
<priority> is an integer; options with higher numbers have higher priority in
  automatic mode.

Options:
  --altdir <directory>     change the alternatives directory.
  --admindir <directory>   change the administrative directory.
  --log <file>             change the log file.
  --force                  allow replacing files with alternative links.
  --skip-auto              skip prompt for alternatives correctly configured
                           in automatic mode (relevant for --config only)
  --quiet                  quiet operation, minimal output.
  --verbose                verbose operation, more output.
  --debug                  debug output, way more output.
  --help                   show this help message.
  --version                show the version.
```

### 清空大文件内容

* 重定向清空

```shell
> big.log
OR
: > big.log
OR
true > big.log
OR
echo > big.log
```

* 使用带/dev/null的cat/ cp/ dd 清空

```shell
cat /dev/null > big.log
cp /dev/null big.log
dd if=/dev/null of=big.log
```

* 使用truncate命令清空
  `truncate -s 0 big.log`
