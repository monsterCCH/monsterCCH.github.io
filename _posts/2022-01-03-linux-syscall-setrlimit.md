---
layout: post
title: Linux 系统调用 setrlimit&getrlimit 详解
categories: syscall, linux
description: Linux 系统调用 setrlimit&getrlimit 详解
keywords: setrlimit,getrlimit 
---

## 功能描述
获取或设定资源使用限制。每种资源都有相关的软硬限制，软限制是内核强加给相应资源的限制值，硬限制是软限制的最大值。非授权调用进程只可以将其软限制指定为0~硬限制范围中的某个值，同时能不可逆转地降低其硬限制。授权进程可以任意改变其软硬限制。

## 用法
```c
#include <sys/resource.h>

int getrlimit(int resource, struct rlimit *rlim);
int setrlimit(int resource, const struct rlimit *rlim);
```

### resource 参数
```c
RLIM_INFINITY //表示不对资源限制
RLIMIT_AS //进程的最大虚内存空间，字节为单位。
RLIMIT_CORE //内核转存文件的最大长度。
RLIMIT_CPU //最大允许的CPU使用时间，秒为单位。当进程达到软限制，内核将给其发送SIGXCPU信号，这一信号的默认行为是终止进程的执行。然而，可以捕捉信号，处理句柄可将控制返回给主程序。如果进程继续耗费CPU时间，核心会以每秒一次的频率给其发送SIGXCPU信号，直到达到硬限制，那时将给进程发送 SIGKILL信号终止其执行。
RLIMIT_DATA //进程数据段的最大值。
RLIMIT_FSIZE //进程可建立的文件的最大长度。如果进程试图超出这一限制时，核心会给其发送SIGXFSZ信号，默认情况下将终止进程的执行。
RLIMIT_LOCKS //进程可建立的锁和租赁的最大值。
RLIMIT_MEMLOCK //进程可锁定在内存中的最大数据量，字节为单位。
RLIMIT_MSGQUEUE //进程可为POSIX消息队列分配的最大字节数。
RLIMIT_NICE //进程可通过setpriority() 或 nice()调用设置的最大完美值。
RLIMIT_NOFILE //指定比进程可打开的最大文件描述词大一的值，超出此值，将会产生EMFILE错误。
RLIMIT_NPROC //用户可拥有的最大进程数。
RLIMIT_RTPRIO //进程可通过sched_setscheduler 和 sched_setparam设置的最大实时优先级。
RLIMIT_SIGPENDING //用户可拥有的最大挂起信号数。
RLIMIT_STACK //最大的进程堆栈，以字节为单位
```

### rlimit 结构体原型
```c
    struct rlimit {
　　rlim_t rlim_cur;
　　rlim_t rlim_max;
    };
```

### 返回值说明
```text
成功执行时，返回0。失败返回-1，errno被设为以下的某个值
EFAULT：rlim指针指向的空间不可访问
EINVAL：参数无效
EPERM：增加资源限制值时，权能不允许
```

## 扩展
ulimit和setrlimit轻松修改task进程资源上限值

在linux系统中，Resouce limit指在一个进程的执行过程中，它所能得到的资源的限制，比如进程的core file的最大值，虚拟内存的最大值等。

Resouce limit的大小可以直接影响进程的执行状况。其有两个最重要的概念：soft limit 和 hard limit。
```c
struct rlimit {
　　rlim_t rlim_cur;　　//soft limit
　　rlim_t rlim_max;　　//hard limit
};
```
soft limit是指内核所能支持的资源上限。比如对于RLIMIT_NOFILE(一个进程能打开的最大文件数，内核默认是1024)，soft limit最大也只能达到1024。对于RLIMIT_CORE(core文件的大小，内核不做限制)，soft limit最大能是unlimited。
hard limit在资源中只是作为soft limit的上限。当你设置hard limit后，你以后设置的soft limit只能小于hard limit。要说明的是，hard limit只针对非特权进程，也就是进程的有效用户ID(effective user ID)不是0的进程。具有特权级别的进程(具有属性CAP_SYS_RESOURCE)，soft limit则只有内核上限。

我们可以来看一下下面两条命令的输出
```shell
sishen@sishen:~$ ulimit -c -n -s
core file size (blocks, -c) 0
open files (-n) 1024
stack size (kbytes, -s) 8192

sishen@sishen:~$ ulimit -c -n -s -H
core file size (blocks, -c) unlimited
open files (-n) 1024
stack size (kbytes, -s) unlimited
```
-H表示显示的是hard limit。从结果上可以看出soft limit和hard limit的区别。unlimited表示no limit, 即内核的最大值。


对于resouce limit的读取修改，有两种方法。

* 使用shell内建命令ulimit
* 使用getrlimit和setrlimit API

ulimit是改变shell的resouce limit，并达到改变shell启动的进程的resouce limit效果(子进程继承)。

`usage：ulimit [-SHacdefilmnpqrstuvx [limit]]`

当不指定limit的时候，该命令显示当前值。这里要注意的是，当你要修改limit的时候，如果不指定-S或者-H，默认是同时设置soft limit和hard limit。也就是之后设置时只能减不能增。所以，建议使用ulimit设置limit参数是加上-S。

getrlimit和setrlimit的使用也很简单，manpage里有很清楚的描述。

需要注意的是你在setrlimit，需要检查是否成功来判断新值有没有超过hard limit。如下例Linux系统中在应用程序运行过程中经常会遇到程序突然崩溃，提示：Segmentation fault，这是因为应用程序收到了SIGSEGV信号。这个信号提示当进程发生了无效的存储访问，当接收到这个信号时，缺省动作是：终止w/core。终止w/core的含义是：在进程当前目录生成core文件，并将进程的内存映象复制到core文件中，core文件的默认名称就是“core”（这是 Unix类系统的一个由来已久的功能）。
事实上，并不是只有SIGSEGV信号产生coredump，还有下面一些信号也产生coredump：SIGABRT（异常终止）、SIGBUS（硬件故障）、SIGEMT（硬件故障）、SIGFPE（算术异常）、SIGILL（非法硬件指令）、SIGIOT（硬件故障），SIGQUIT，SIGSYS（无效系统调用），SIGTRAP（硬件故障）等。
对于resouce limit的读取修改，有两种方法。

* 使用shell内建命令ulimit
* 使用getrlimit和setrlimit APIsetrlimit：
```c
if (getrlimit(RLIMIT_CORE, &rlim)==0) {
　　rlim_new.rlim_cur = rlim_new.rlim_max = RLIM_INFINITY;
　　if (setrlimit(RLIMIT_CORE, &rlim_new)!=0) {
　　　　rlim_new.rlim_cur = rlim_new.rlim_max = rlim.rlim_max;
　　　　(void) setrlimit(RLIMIT_CORE, &rlim_new);
　　}
}
```
 
转载：[Linux系统调用--getrlimit()与setrlimit()函数详解](https://www.cnblogs.com/hustquan/archive/2012/04/01/2428128.html)
