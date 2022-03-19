---
layout: post
title: ubuntu 使用记录 
categories: ubuntu, linux
description: 记录使用 ubuntu 进行开发的问题 
keywords: ubuntu
---

# 工具使用介绍

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

# 磁盘管理

![linux_disk_managerdrawio.png](./assets/linux_disk_manager.drawio.png)

