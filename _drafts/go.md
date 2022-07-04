---
layout: post
title: go 使用记录 
categories: go
description: go 使用记录
keywords: go
---

## linux 环境安装

```shell
wget https://go.dev/dl/go1.18.3.linux-amd64.tar.gz
tar -zxvf go1.18.3.linux-amd64.tar.gz
# 设置环境变量
vim /etc/profile

GCC_PATH=/opt/dev_src/gcc-11.2.0_ins/bin
export GOROOT=/opt/dev_src/go
export GOPATH=/opt/dev_src/go_work
export PATH=$GCC_PATH:$PATH:$GOPATH/bin:$GOROOT/bin

source /etc/profile

# 验证版本
go version

'go version go1.18.3 linux/amd64'
```

## 安装第三方包

go install 报错 

`Get "https://proxy.golang.org/github.com/zu1k/nali.git/@v/list": dial tcp 172.217.160.113:443: i/o timeout`

解决方案：

* Bash (Linux or macOS)
```shell
# 配置 GOPROXY 环境变量
export GOPROXY=https://proxy.golang.com.cn,direct
# 还可以设置不走 proxy 的私有仓库或组，多个用逗号相隔（可选）
export GOPRIVATE=git.mycompany.com,github.com/my/private
```

* PowerShell (Windows)
```shell
# 配置 GOPROXY 环境变量
$env:GOPROXY = "https://proxy.golang.com.cn,direct"
# 还可以设置不走 proxy 的私有仓库或组，多个用逗号相隔（可选）
$env:GOPRIVATE = "git.mycompany.com,github.com/my/private"
```

安装第三方包示例

`go install github.com/zu1k/nali@latest`