---
layout: post
title: ubuntu 使用记录 
categories: ubuntu, linux
description: 记录使用 ubuntu 进行开发的问题 
keywords: ubuntu
---

# 系统工具使用介绍

## systemctl

Systemctl 是一个 systemd 工具，主要负责控制 systemd 系统和服务管理器。

Systemd 是一个系统管理守护进程、工具和库的集合，用于取代 System V 初始进程。Systemd 的功能是用于集中管理和配置类 UNIX 系统

[systemctl 命令完全指南](https://linux.cn/article-5926-1.html)

[systemctl命令介绍和使用](https://segmentfault.com/a/1190000023029058)

### 配置自定义服务

```sh
cd /usr/lib/systemd/system/ 目录中创建自定义的 service 文件如下

redis-server.service 

[Unit]
Description=Advanced key-value store
After=network.target
Documentation=http://redis.io/documentation, man:redis-server(1)

[Service]
Type=forking
ExecStart=/opt/dev_src/redis-6.2.6/src/redis-server /etc/redis/redis.conf
ExecStop=/opt/dev_src/redis-6.2.6/src/redis-cli shutdown
TimeoutStopSec=0
Restart=always
#User=redis
#Group=redis

[Install]
WantedBy=multi-user.target
#Alias=redis.service
```

#### 常用命令

```sh
systemctl list-units --type=service 列出所有的 service
systemctl list-units --type=service --state=active 列出所有为 active 状态的服务
systemctl list-units --type=service --state running,failed 列出所有正在运行或失败的服务
systemctl start/stop/restart/reload service 对一个服务进行启动、停止、重启、重新加载操作
sytemctl daemon-reload 重载所有修改过的配置文件
```

## snap

一个全新的软件包管理方式，类似与容器拥有一个程序的所有文件和库，各个应用程序之间完全独立。**解决了应用程序间的依赖，使程序更容易管理，但会导致占用磁盘空间变大。**

### 安装方法

```sh
apt install snap
apt install snapcraft 提供将程序打包成 snap
```

### 常用命令

```sh
snap list 列出已经安装的 snap 包
snap find ... 搜索要安装的 snap 包
snap install ... 安装对应的 snap 包
snap refresh ... 更新一个 snap 包，不指定名称则更新所有
snap revert ... 还原一个 snap 包
snap remove ... 删除对应 snap 包
snap change 查看安装状态
```

## dpkg

Debian，以及基于 Debian 的系统，如 Ubuntu 等，所使用的包格式为 deb。以下为操作 deb 包的常用 Dpkg 指令表

```sh
dpkg -i package.deb	安装包
dpkg -r package	删除包
dpkg -P package	删除包（包括配置文件）
dpkg -L package	列出与该包关联的文件
dpkg -l package	显示该包的版本
dpkg --unpack package.deb	解开 deb 包的内容
dpkg -S keyword	搜索所属的包内容
dpkg -l	列出当前已安装的包
dpkg -c package.deb	列出 deb 包的内容
dpkg --configure package	配置包
```
实例
```shell
查看包安装路径
dpkg -l | grep [pakeage_name]
dplkg -L [pakeage_name]
```

## APT

APT 全称为“Advanced Package Tool”即高级软件包工具,和命令 apt 不是一个东西；这是现今最成熟的软件包管理系统，它可以自动检测软件依赖问题，下载和安装所有文件；

> apt = apt-get、apt-cache 和 apt-config 中最常用命令选项的集合。

### apt 和 apt-get 的区别

通过 **apt** 命令，用户可以在同一地方集中得到所有必要的工具，**apt** 的主要目的是提供一种以「让终端用户满意」的方式来处理 Linux 软件包的有效方式。

**apt** 具有更精减但足够的命令选项，而且参数选项的组织方式更为有效。除此之外，它默认启用的几个特性对最终用户也非常有帮助。例如，可以在使用 **apt** 命令安装或删除程序时看到进度条。

**apt** 还会在更新存储库数据库时提示用户可升级的软件包个数。

虽然 **apt** 与 **apt-get** 有一些类似的命令选项，但它并不能完全向下兼容 **apt-get** 命令。也就是说，可以用 **apt** 替换部分 **apt-get** 系列命令，但不是全部。

| apt 命令                           | 取代的命令                         | 命令的功能                     |
| ------------------------------------ | ------------------------------------ | -------------------------------- |
| apt install                        | apt-get install                    | 安装软件包                     |
| apt remove                         | apt-get remove                     | 移除软件包                     |
| apt purge                          | apt-get purge                      | 移除软件包及配置文件           |
| apt update                         | apt-get update                     | 刷新存储库索引                 |
| apt upgrade                        | apt-get upgrade                    | 升级所有可升级的软件包         |
| apt autoremove                     | apt-get autoremove                 | 自动删除不需要的包             |
| apt download                       | apt—get download                  | 下载对应的软件包               |
| apt full-upgrade                   | apt-get dist-upgrade               | 在升级软件包时自动处理依赖关系 |
| apt install --reinstall            | apt-get install --reinstall        | 重新安装软件                   |
| apt --install-suggests install     | apt-get --install-suggests install | 同时安装建议的安装包           |
| apt-get --install-suggests install | apt-get --install-suggests install | 不安装建议的安装包             |
| apt search                         | apt-cache search                   | 搜索应用程序                   |
| apt show                           | apt-cache show                     | 显示装细节                     |

| 新的apt命令      | 命令的功能                           |
| ------------------ | -------------------------------------- |
| apt list         | 列出包含条件的包（已安装，可升级等） |
| apt edit-sources | 编辑源列表                           |

## nala

nala 是 libapt-pkg 的前端，通过删除一些冗余消息、改进包格式以及使用颜色来说明在安装、删除或升级过程中包发生的情况来简化 apt 的使用.

主要优点：

* 并行下载
* 自动选择最快的镜像
* 软件包的管理历史

### 安装方法

```sh
git clone https://gitlab.com/volian/nala
cd nala
python3 ./setup.py build
sudo python3 ./setup.py install
```

或

```sh
echo "deb [arch=amd64,arm64,armhf] http://deb.volian.org/volian/ scar main" | sudo tee /etc/apt/sources.list.d/volian-archive-scar-unstable.list
wget -qO - https://deb.volian.org/volian/scar.key | sudo tee /etc/apt/trusted.gpg.d/volian-archive-scar-unstable.gpg > /dev/null
sudo apt update && sudo apt install nala
```

### 用法

```sh
usage: nala [--options] <command>

commands:

install            install packages
remove             remove packages
purge              purge packages
update             update package list and upgrade the system
upgrade            alias for update
fetch              fetches fast mirrors to speed up downloads
show               show package details
history            show transaction history
clean              clears out the local repository of retrieved package files

optional arguments:
-h, --help           show this help message and exit
-y, --assume-yes     assume 'yes' to all prompts and run non-interactively
-d, --download-only  package files are only retrieved, not unpacked or installed
-v, --verbose        logs extra information for debugging
--no-update          skips updating the package list
--no-autoremove      stops nala from autoremoving packages
--remove-essential   allows the removal of essential packages
--raw-dpkg           skips all formatting and you get raw dpkg output
--update             updates the package list
--debug              logs extra information for debugging
--version            show program's version number and exit
--license            reads the licenses of software compiled in and then reads the GPLv3
```

# 软件工具安装

## 编译开发工具安装

**一键安装**

`apt install build-essential cmake gdb libboost-all-dev libsystemd-dev libssl-dev pkg-config tcl tcl-tls net-tools`

## Linux 系统监控工具选型

[比 top 更好用的七个 linux 重要系统状态监控工具](https://itsfoss.com/linux-system-monitoring-tools/)

**推荐 htop、glances、bashtop(bpytop)**

### bashtop

一款针对 Linux/OSX/FreeBSD 的基于终端的资源监控使用程序, 开发者写了三个版本，bahstop(基于 shell 编写)，bpytop(基于 python 编写)， btop(基于 C++ 编写)

安装方法：

* snap

```sh
snap install bashtop
```

* APT软件包管理器

```sh
sudo add-apt-repository ppa:bashtop-monitor/bashtop
'add-apt-repository: command not found
apt install software-properties-common'
sudo apt update
sudo apt install bashtop
```

## 其它工具

### shfmt

Shell 格式化程序、解析器和解释器。该项目本身托管在 GitHub 上，并有一个清晰的 README 和清晰的存储库。该工具是用 Go 开发的，支持 POSIX、Bash 和 mksh shell。这使得 shfmt 成为一个真正通用的程序，而不是仅限于 Bash。

```sh
# ubuntu 安装
snap install shfmt

# 工具使用
shfmt test.sh
```

# 网络配置

## 查看网络配置

```sh
ip a
ip addr
```

## 设置网络地址

ubuntu 20.04 使用 **netplan** 作为默认网络配置

**查看 netplan 配置文件**

`ls /etc/netplan`

**备份默认配置文件**

`cp /etc/netplan/00-installer-config.yaml /etc/netplan/00-installer-config.yaml.bak`

**编辑配置文件**

`nano /etc/netplan/00-installer-config.yaml`

### 静态地址

```yaml
network:
  ethernets:
    ens33:
      dhcp4: no
      addresses:
      - 192.168.0.111/24
      gateway4: 192.168.0.1
      nameservers:
        addresses: [114.114.114.114, 8.8.8.8]
  version: 2
```

### 动态地址

```yaml
network:
  ethernets:
    ens33:
      dhcp4: yes
      addresses: []
  version: 2
```

*修改时不能使用制表符*

**应用配置**

```sh
netplan try
netplan apply
```

## 主机名配置

### 查看主机名

```sh
hostnamectrl
hostname
```

### 修改主机名

`hostnamectl set-hostname ubuntu.server`
或

```sh
vim /etc/hostname
vim /etc/hosts
```

### firewall 防火墙设置

firewall-cmd 是 firewalld的字符界面管理工具，firewalld是centos7的一大特性，最大的好处有两个：
支持动态更新，不用重启服务；第二个就是加入了防火墙的“zone”概念。

firewalld跟iptables比起来至少有两大好处：

1. firewalld可以动态修改单条规则，而不需要像iptables那样，在修改了规则后必须得全部刷新才可以生效。
2. firewalld在使用上要比iptables人性化很多，即使不明白“五张表五条链”而且对TCP/IP协议也不理解也可以实现大部分功能。

firewalld自身并不具备防火墙的功能，而是和iptables一样需要通过内核的netfilter来实现，也就是说firewalld和 iptables一样，他们的作用都是用于维护规则，而真正使用规则干活的是内核的netfilter，只不过firewalld和iptables的结 构以及使用方法不一样罢了。

#### 命令格式

`firewall-cmd [选项 ... ]`

#### 选项

通用选项
```shell
-h, --help    # 显示帮助信息；
-V, --version # 显示版本信息. （这个选项不能与其他选项组合）；
-q, --quiet   # 不打印状态消息；
```

状态选项
```shell
--state                # 显示firewalld的状态；
--reload               # 不中断服务的重新加载；
--complete-reload      # 中断所有连接的重新加载；
--runtime-to-permanent # 将当前防火墙的规则永久保存；
--check-config         # 检查配置正确性；
```
日志选项
```shell
--get-log-denied         # 获取记录被拒绝的日志；
--set-log-denied=<value> # 设置记录被拒绝的日志，只能为 'all','unicast','broadcast','multicast','off' 其中的一个；
```
#### 实例

```shell
# 安装firewalld
yum install firewalld firewall-config

systemctl start  firewalld # 启动
systemctl stop firewalld  # 停止
systemctl enable firewalld # 启用自动启动
systemctl disable firewalld # 禁用自动启动
systemctl status firewalld # 或者 firewall-cmd --state 查看状态

# 关闭服务的方法
# 你也可以关闭目前还不熟悉的FirewallD防火墙，而使用iptables，命令如下：

systemctl stop firewalld
systemctl disable firewalld
yum install iptables-services
systemctl start iptables
systemctl enable iptables
```
配置firewalld
```shell
firewall-cmd --version  # 查看版本
firewall-cmd --help     # 查看帮助

# 查看设置：
firewall-cmd --state  # 显示状态
firewall-cmd --get-active-zones  # 查看区域信息
firewall-cmd --get-zone-of-interface=eth0  # 查看指定接口所属区域
firewall-cmd --panic-on  # 拒绝所有包
firewall-cmd --panic-off  # 取消拒绝状态
firewall-cmd --query-panic  # 查看是否拒绝

firewall-cmd --reload # 更新防火墙规则
firewall-cmd --complete-reload
# 两者的区别就是第一个无需断开连接，就是firewalld特性之一动态添加规则，第二个需要断开连接，类似重启服务


# 将接口添加到区域，默认接口都在public
firewall-cmd --zone=public --add-interface=eth0
# 永久生效再加上 --permanent 然后reload防火墙
 
# 设置默认接口区域，立即生效无需重启
firewall-cmd --set-default-zone=public

# 查看所有打开的端口：
firewall-cmd --zone=dmz --list-ports

# 加入一个端口到区域：
firewall-cmd --zone=dmz --add-port=8080/tcp
# 若要永久生效方法同上
 
# 打开一个服务，类似于将端口可视化，服务需要在配置文件中添加，/etc/firewalld 目录下有services文件夹，这个不详细说了，详情参考文档
firewall-cmd --zone=work --add-service=smtp
 
# 移除服务
firewall-cmd --zone=work --remove-service=smtp

# 显示支持的区域列表
firewall-cmd --get-zones

# 设置为家庭区域
firewall-cmd --set-default-zone=home

# 查看当前区域
firewall-cmd --get-active-zones

# 设置当前区域的接口
firewall-cmd --get-zone-of-interface=enp03s

# 显示所有公共区域（public）
firewall-cmd --zone=public --list-all

# 临时修改网络接口（enp0s3）为内部区域（internal）
firewall-cmd --zone=internal --change-interface=enp03s

# 永久修改网络接口enp03s为内部区域（internal）
firewall-cmd --permanent --zone=internal --change-interface=enp03s

```
服务管理
```shell
# 显示服务列表  
Amanda, FTP, Samba和TFTP等最重要的服务已经被FirewallD提供相应的服务，可以使用如下命令查看：

firewall-cmd --get-services

# 允许SSH服务通过
firewall-cmd --new-service=ssh

# 禁止SSH服务通过
firewall-cmd --delete-service=ssh

# 打开TCP的8080端口
firewall-cmd --enable ports=8080/tcp

# 临时允许Samba服务通过600秒
firewall-cmd --enable service=samba --timeout=600

# 显示当前服务
firewall-cmd --list-services

# 添加HTTP服务到内部区域（internal）
firewall-cmd --permanent --zone=internal --add-service=http
firewall-cmd --reload     # 在不改变状态的条件下重新加载防火墙

```
端口管理
```shell
# 打开443/TCP端口
firewall-cmd --add-port=443/tcp

# 永久打开3690/TCP端口
firewall-cmd --permanent --add-port=3690/tcp

# 永久打开端口好像需要reload一下，临时打开好像不用，如果用了reload临时打开的端口就失效了
# 其它服务也可能是这样的，这个没有测试
firewall-cmd --reload

# 查看防火墙，添加的端口也可以看到
firewall-cmd --list-all

```
直接模式
```shell
# FirewallD包括一种直接模式，使用它可以完成一些工作，例如打开TCP协议的9999端口

firewall-cmd --direct -add-rule ipv4 filter INPUT 0 -p tcp --dport 9000 -j ACCEPT
firewall-cmd --reload
```
#### 自定义服务管理

选项
```shell
（末尾带有 [P only] 的话表示该选项除了与（--permanent）之外，不能与其他选项一同使用！）
--new-service=<服务名> 新建一个自定义服务 [P only]
--new-service-from-file=<文件名> [--name=<服务名>]
                      从文件中读取配置用以新建一个自定义服务 [P only]
--delete-service=<服务名>
                      删除一个已存在的服务 [P only]
--load-service-defaults=<服务名>
                      Load icmptype default settings [P only]
--info-service=<服务名>
                      显示该服务的相关信息
--path-service=<服务名>
                      显示该服务的文件的相关路径 [P only]
--service=<服务名> --set-description=<描述>
                      给该服务设置描述信息 [P only]
--service=<服务名> --get-description
                      显示该服务的描述信息 [P only]
--service=<服务名> --set-short=<描述>
                      给该服务设置一个简短的描述 [P only]
--service=<服务名> --get-short
                      显示该服务的简短描述 [P only]
                      
--service=<服务名> --add-port=<端口号>[-<端口号>]/<protocol>
                      给该服务添加一个新的端口(端口段) [P only]
                      
--service=<服务名> --remove-port=<端口号>[-<端口号>]/<protocol>
                      从该服务上移除一个端口(端口段) [P only]
                      
--service=<服务名> --query-port=<端口号>[-<端口号>]/<protocol>
                      查询该服务是否添加了某个端口(端口段) [P only]
                      
--service=<服务名> --get-ports
                      显示该服务添加的所有端口 [P only]
                      
--service=<服务名> --add-protocol=<protocol>
                      为该服务添加一个协议 [P only]
                      
--service=<服务名> --remove-protocol=<protocol>
                      从该服务上移除一个协议 [P only]
                      
--service=<服务名> --query-protocol=<protocol>
                      查询该服务是否添加了某个协议 [P only]
                      
--service=<服务名> --get-protocols
                      显示该服务添加的所有协议 [P only]
                      
--service=<服务名> --add-source-port=<端口号>[-<端口号>]/<protocol>
                      添加新的源端口(端口段)到该服务 [P only]
                      
--service=<服务名> --remove-source-port=<端口号>[-<端口号>]/<protocol>
                      从该服务中删除源端口(端口段) [P only]
                      
--service=<服务名> --query-source-port=<端口号>[-<端口号>]/<protocol>
                      查询该服务是否添加了某个源端口(端口段) [P only]
                      
--service=<服务名> --get-source-ports
                      显示该服务所有源端口 [P only]
                      
--service=<服务名> --add-module=<module>
                      为该服务添加一个模块 [P only]
--service=<服务名> --remove-module=<module>
                      为该服务移除一个模块 [P only]
--service=<服务名> --query-module=<module>
                      查询该服务是否添加了某个模块 [P only]
--service=<服务名> --get-modules
                      显示该服务添加的所有模块 [P only]
--service=<服务名> --set-destination=<ipv>:<address>[/<mask>]
                      Set destination for ipv to address in service [P only]
--service=<服务名> --remove-destination=<ipv>
                      Disable destination for ipv i service [P only]
--service=<服务名> --query-destination=<ipv>:<address>[/<mask>]
                      Return whether destination ipv is set for service [P only]
--service=<服务名> --get-destinations
                      List destinations in service [P only]

```
#### 控制端口 / 服务
可以通过两种方式控制端口的开放，一种是指定端口号另一种是指定服务名。虽然开放 http 服务就是开放了 80 端口，但是还是不能通过端口号来关闭，也就是说通过指定服务名开放的就要通过指定服务名关闭；通过指定端口号开放的就要通过指定端口号关闭。还有一个要注意的就是指定端口的时候一定要指定是什么协议，tcp 还是 udp。知道这个之后以后就不用每次先关防火墙了，可以让防火墙真正的生效
```shell
firewall-cmd --add-service=mysql        # 开放mysql端口
firewall-cmd --remove-service=http      # 阻止http端口
firewall-cmd --list-services            # 查看开放的服务
firewall-cmd --add-port=3306/tcp        # 开放通过tcp访问3306
firewall-cmd --remove-port=80tcp        # 阻止通过tcp访问3306
firewall-cmd --add-port=233/udp         # 开放通过udp访问233
firewall-cmd --list-ports               # 查看开放的端口
```
伪装IP
```shell
firewall-cmd --query-masquerade # 检查是否允许伪装IP
firewall-cmd --add-masquerade   # 允许防火墙伪装IP
firewall-cmd --remove-masquerade# 禁止防火墙伪装IP
```

#### 端口转发
端口转发可以将指定地址访问指定的端口时，将流量转发至指定地址的指定端口。转发的目的如果不指定 ip 的话就默认为本机，如果指定了 ip 却没指定端口，则默认使用来源端口。 如果配置好端口转发之后不能用，可以检查下面两个问题：
1. 比如我将 80 端口转发至 8080 端口，首先检查本地的 80 端口和目标的 8080 端口是否开放监听了
2. 其次检查是否允许伪装 IP，没允许的话要开启伪装 IP
```shell
firewall-cmd --add-forward-port=port=80:proto=tcp:toport=8080   # 将80端口的流量转发至8080
firewall-cmd --add-forward-port=port=80:proto=tcp:toaddr=192.168.0.1 # 将80端口的流量转发至192.168.0.1
firewall-cmd --add-forward-port=port=80:proto=tcp:toaddr=192.168.0.1:toport=8080 # 将80端口的流量转发至192.168.0.1的8080端口
```
1. 当我们想把某个端口隐藏起来的时候，就可以在防火墙上阻止那个端口访问，然后再开一个不规则的端口，之后配置防火墙的端口转发，将流量转发过去。
2. 端口转发还可以做流量分发，一个防火墙拖着好多台运行着不同服务的机器，然后用防火墙将不同端口的流量转发至不同机器。

# LVM 磁盘管理

## 术语说明

* 逻辑卷组管理 (Logic Volume Manager)
* 物理卷 (Physical Volume, PV): 物理磁盘分区，如果使用 LVM 管理，需要使用 fdisk 将其 ID 改为 LVM 可以识别的值，即 8e.
* 卷组 (Volume Group,VG): PV 的集合
* 逻辑卷 (Logic Volume,LV): VG 中分出的逻辑磁盘

架构如下：

![linux_disk_managerdrawio.png](./assets/linux_disk_manager.drawio.png)

## LVM 管理的优点

* 可以使用大容量磁盘，将多块磁盘聚合到一个卷组上，可以分配大容量逻辑卷，以便挂载。
* 动态调整磁盘大小，使磁盘空间得到合理利用。

## 使用教程

### 创建物理卷 (PV)

创建物理卷可以使用磁盘直接创建也可以使用磁盘分区创建

相关命令

```sh
# pvcreate 创建物理卷
pvcreate /dev/sda

# pvdisplay、pvscan、pvs 查看物理卷
pvdisplay
pvscan
pvs
```

#### 磁盘创建物理卷

样例：

```sh
# 使用fdisk -l确认磁盘，可以看到/dev/sdb未做分区处理
[root@masterdb ~]# fdisk -l /dev/sdb

Disk /dev/sdb: 2147 MB, 2147483648 bytes, 4194304 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

# 使用pvcreate将sdb磁盘创建为物理卷
[root@masterdb ~]# pvcreate /dev/sdb
  Physical volume "/dev/sdb" successfully created.

# 使用pvdisplay确认物理卷信息
[root@masterdb ~]# pvdisplay 
  --- Physical volume ---
  PV Name               /dev/sda3
  VG Name               centos
  PV Size               <68.73 GiB / not usable 4.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              17593
  Free PE               0
  Allocated PE          17593
  PV UUID               FRxq7G-1XWu-dPeW-wEwO-322y-M9XR-0ExebA
   
  "/dev/sdb" is a new physical volume of "2.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb
  VG Name               
  PV Size               2.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               nsL75f-o3fD-apyz-SSY0-miUi-4RYf-zVLIT6
   # 也可以使用pvs确认物理卷信息,不过能够看到的信息比pvdisplay少
[root@masterdb ~]# pvs 
  PV         VG     Fmt  Attr PSize  PFree
  /dev/sda3  centos lvm2 a--  68.72g    0 
  /dev/sdb          lvm2 ---   2.00g 2.00g
```

#### 磁盘分区创建物理卷

磁盘分区后，需要将磁盘的 ID 改为 8e

样例：

```sh
[root@masterdb ~]# fdisk /dev/sdc 
 Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
 Be careful before using the write command.


 Command (m for help): t     #t可以修改分区代码
 Partition number (1,2, default 2): 1            #选择1分区进行修改
 Hex code (type L to list all codes): L          #如果不知道类型，可以用”L”列出可以选择的修改代码

  0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris        
  1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
  2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
  3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden C:  c6  DRDOS/sec (FAT-
  4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx         
  5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data    
  6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
  7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility   
  8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt         
  9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access     
  a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O        
  b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor      
  c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi eb  BeOS fs        
  e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         ee  GPT            
  f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ef  EFI (FAT-12/16/
 10  OPUS            55  EZ-Drive        a7  NeXTSTEP        f0  Linux/PA-RISC b
 11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f1  SpeedStor      
 12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f4  SpeedStor      
 14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f2  DOS secondary  
 16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      fb  VMware VMFS    
 17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fc  VMware VMKCORE 
 18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fd  Linux raid auto
 1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fe  LANstep        
 1c  Hidden W95 FAT3 75  PC/IX           be  Solaris boot    ff  BBT            
 1e  Hidden W95 FAT1 80  Old Minix      
 Hex code (type L to list all codes): 8e         #选择8e
 Changed type of partition 'Linux' to 'Linux LVM'

Command (m for help): w               # 保存
 The partition table has been altered!

Calling ioctl() to re-read partition table.
 Syncing disks.
 [root@masterdb ~]#
```

### 创建扩容卷组

相关命令

```sh
# vgcreate 创建卷组
vgcreate [VG_NAME] [device1] [device2] ...

# vgdisplay、vgsan、vgs 查看卷组
vgdisplay
vgsan
vgs
```

样例：

```sh
# 使用vgcreate创建卷组VG_TEST,包含物理卷：/dev/sdb和/dev/sdc1
[root@masterdb ~]# vgcreate VG_TEST /dev/sdb /dev/sdc1
  Volume group "VG_TEST" successfully created
# 查看方法一：使用vgdisplay查看卷组信息[root@masterdb ~]# vgdisplay
  --- Volume group ---
  VG Name               VG_TEST
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               2.99 GiB
  PE Size               4.00 MiB
  Total PE              766
  Alloc PE / Size       0 / 0   
  Free  PE / Size       766 / 2.99 GiB
  VG UUID               DmY2Nz-ietc-2Y8Y-7A1b-1cpT-qEeV-XrgURn
...  
   
# 查看方法二：使用vgscan查看卷组信息
[root@masterdb ~]# vgscan
  Reading volume groups from cache.
  Found volume group "VG_TEST" using metadata type lvm2
  Found volume group "centos" using metadata type lvm2

# 查看方法三：使用vgs查看卷组信息
[root@masterdb ~]# vgs 
  VG      #PV #LV #SN Attr   VSize  VFree
  VG_TEST   2   0   0 wz--n-  2.99g 2.99g
  centos    1   4   0 wz--n- 68.72g    0
```

#### 扩容卷组

相关命令：

```sh
vgextend [VG_NAME] [device1] [device2] ...
```

### 创建、扩容、逻辑卷

相关命令：

```sh
# lvcreate 创建逻辑卷
lvcreate -L [SIZE] -n [LV_NAME] [VG_NAME]

# lvdisplay、lvscan、lvs 查看逻辑卷
lvdisplay
lvscan
lvs
```

#### 创建逻辑卷

```sh
# 使用lvcreate创建逻辑卷lv_test
[root@masterdb ~]# lvcreate -L 1g -n lv_test VG_TEST
  Logical volume "lv_test" created.

# 查看方法一：使用lvdisplay查看逻辑卷[root@masterdb ~]# lvdisplay 
  --- Logical volume ---
  LV Path                /dev/VG_TEST/lv_test
  LV Name                lv_test
  VG Name                VG_TEST
  LV UUID                RqWMOG-wCJJ-deu4-dIgv-c5hI-Bsqa-FHgh4E
  LV Write Access        read/write
  LV Creation host, time masterdb, 2020-05-13 22:42:45 +0800
  LV Status              available
  # open                 0
  LV Size                1.00 GiB
  Current LE             256
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:4
...
   
# 查看方法二：使用lvscan查看逻辑卷  
[root@masterdb ~]# lvscan 
  ACTIVE            '/dev/VG_TEST/lv_test' [1.00 GiB] inherit
  ACTIVE            '/dev/centos/mysql' [<45.00 GiB] inherit
  ACTIVE            '/dev/centos/swap' [<3.73 GiB] inherit
  ACTIVE            '/dev/centos/home' [10.00 GiB] inherit
  ACTIVE            '/dev/centos/root' [10.00 GiB] inherit
  
# 查看方法三：使用lvs查看逻辑卷  
[root@masterdb ~]# lvs 
  LV      VG      Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv_test VG_TEST -wi-a-----   1.00g                                                    
  home    centos  -wi-ao----  10.00g                                                    
  mysql   centos  -wi-ao---- <45.00g                                                    
  root    centos  -wi-ao----  10.00g                                                    
  swap    centos  -wi-ao----  <3.73g
```

创建完 lv 后， 格式化挂载即可

```sh
# 创建文件系统[root@masterdb ~]# mkfs.ext3 /dev/VG_TEST/lv_test 
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
 OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
 Stride=0 blocks, Stripe width=0 blocks
 65536 inodes, 262144 blocks
 13107 blocks (5.00%) reserved for the super user
 First data block=0
 Maximum filesystem blocks=268435456
 8 block groups
 32768 blocks per group, 32768 fragments per group
 8192 inodes per group
 Superblock backups stored on blocks: 
     32768, 98304, 163840, 229376

Allocating group tables: done                            
 Writing inode tables: done                            
 Creating journal (8192 blocks): done
 Writing superblocks and filesystem accounting information: done

# 创建挂载点[root@masterdb ~]# mkdir /test 
# 挂载文件系统
[root@masterdb ~]# mount /dev/VG_TEST/lv_test /test
# 确认结果
[root@masterdb ~]# df –h 
 Filesystem                   Size  Used Avail Use% Mounted on
 /dev/mapper/centos-root       10G  4.1G  5.9G  42% /
 ...
 /dev/mapper/VG_TEST-lv_test  976M  1.3M  924M   1% /test
```

#### 扩容逻辑卷

相关命令：

```sh
# lvextend 扩容 lv，+SIZE 代表增加的空间
lvextend -L +[SIZE] [lv_device]

# resize2fs 调整文件系统的大小
resize2fs [lv_device]
```

样例：

```sh
# 对lv进行扩容，增加500M空间
[root@masterdb ~]# lvextend -L +500M /dev/VG_TEST/lv_test 
  Size of logical volume VG_TEST/lv_test changed from 1.00 GiB (256 extents) to <1.49 GiB (381 extents).
  Logical volume VG_TEST/lv_test successfully resized.

# 使用resize2fs调整文件系统的大小[root@masterdb ~]# resize2fs /dev/VG_TEST/lv_test 
resize2fs 1.42.9 (28-Dec-2013)
Filesystem at /dev/VG_TEST/lv_test is mounted on /test; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 1
The filesystem on /dev/VG_TEST/lv_test is now 390144 blocks long.

xfs文件系统扩容
xfs_growfs /dev/vg001/lv001 刷新信息，完成扩展。
df -hT 查看挂载信息以及容量，确认扩展成功
```

#### 缩容逻辑卷

相关命令:

```sh
# lvreduce 减小分区大小到 SIZE
lvreduce -L [SIZE] [lv_device]

eg:
ext4 文件系统
umount /dev/vg001/lv001 卸载设备
e2fsck -f /dev/vg001/lv001 检查文件系统完整性
resize2fs /dev/vg001/lv001 35G 刷新信息到需要缩小的空间
lvreduce -L 35G /dev/vg001/lv001 缩小逻辑卷
mount -a 重新挂载
```

xfs 文件系统大小调整：

```sh
# 备份数据 
xfsdump -f [/tmp/opt.dump] [/opt]

# 卸载分区
umount /test

# 减小分区大小
lvreduce -L 30G /dev/mapper/opt

# 使用 xfs 文件系统格式化分区
mkfs.xfs -f /opt

# 重新安装分区
mount /dev/mapper/opt /opt

# 恢复数据
xfsrestore -f /tmp/opt.dump /opt
```

#### 设置自动挂载

```sh
# lsblk 查看全局块设备布局

root@ubuntu:~# lsblk
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0  67.4M  1 loop /snap/powershell/200
sda                         8:0    0   120G  0 disk
├─sda1                      8:1    0     1M  0 part
├─sda2                      8:2    0   1.5G  0 part /boot
└─sda3                      8:3    0 118.5G  0 part
  ├─ubuntu--vg-ubuntu--lv 253:0    0  59.3G  0 lvm  /
  └─ubuntu--vg-opt--lv    253:1    0    59G  0 lvm  /opt
sr0                        11:0    1   1.2G  0 rom

# blkid 或者设备相关信息

root@ubuntu:~# blkid
/dev/sr0: UUID="2022-02-23-09-27-00-00" LABEL="Ubuntu-Server 20.04.4 LTS amd64" TYPE="iso9660" PTUUID="492bdcc4" PTTYPE="dos"
/dev/sda2: UUID="9445ef1c-6249-425f-89a6-38cf25acca9e" TYPE="ext4" PARTUUID="deaf7f11-f433-406e-8eb0-80074769829c"
/dev/sda3: UUID="qp2Wau-DcL5-slT3-KH9R-KMlG-71ez-PhH0xi" TYPE="LVM2_member" PARTUUID="616f30e5-8f5e-48d3-9541-cb9feef9c198"
/dev/mapper/ubuntu--vg-ubuntu--lv: UUID="14f22172-d6f2-41e9-a17c-46c738a6acfa" TYPE="ext4"
/dev/mapper/ubuntu--vg-opt--lv: UUID="8dc14374-37fc-4e1e-83c4-5d2020cb020e" TYPE="ext4"
/dev/loop0: TYPE="squashfs"
/dev/sda1: PARTUUID="ee8b5636-2b50-4c1d-ae33-0887bb5705a2"
```

编辑 /etc/fstab 文件系统信息

```sh
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-q2JDRi66CdfDhvRF2N6xp14H7IfdxalwQfsag6us1M8KvxzvkUy3BjblSeHtqkyn / ext4 defaults 0 1
#/opt was on /dev/ubuntu-vg/opt-lv using as dev
/dev/mapper/ubuntu--vg-opt--lv /opt ext4 defaults  0 1
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/9445ef1c-6249-425f-89a6-38cf25acca9e /boot ext4 defaults 0 1
/swap.img       none    swap    sw      0       0
```

文件字段描述

| key         | description                                              |
|-------------|----------------------------------------------------------|
| file system | 将要挂载的特定块设备或远程文件系统                                        |
| mount point | 挂载点                                                      |
| type        | 挂载文件系统                                                   |
| options     | 文件系统相关联的挂载选项                                             |
| dump        | 针对ext2/3/4文件系统，是否要备份，防止因异常断电导致的数据丢失 此字段默认填0，不需要dump |
| pass        | 指定系统启动时通过fsck检查文件系统的顺序，根文件系统检查顺序为1，其他为2。默认为0表示不执行检查      |

