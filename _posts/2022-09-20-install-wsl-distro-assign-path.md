---
layout: post
title: win11 手动安装 WSL 镜像
categories: WSL, env, win11
description: 手动安装 WSL 镜像
keywords: WSL, env, win11
---

## purpose

wsl 系统镜像默认安装在 C 盘, 可能导致系统盘空间不足，现手动安装到非系统盘

## steps

### 开启 wsl

powershell 中运行

`Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`

### 创建安装目录

```shell
cd F:\
mkdir WSL
cd WSL
```

### 下载镜像文件

[微软官方教程](https://learn.microsoft.com/en-us/windows/wsl/install-manual)

可选 ubuntu2204 
```shell
Invoke-WebRequest -Uri https://aka.ms/wslubuntu2204 -OutFile Ubuntu.appx -UseBasicParsing
OR
curl.exe -L -o ubuntu-2204.appx https://aka.ms/wslubuntu2204
```

### 解压镜像文件

```shell
mv .\Ubuntu2204-220620.AppxBundle .\Ubuntu2204-220620.zip
Expand-Archive .\Ubuntu2204-220620.zip
```

### 安装镜像

```shell
cd .\Ubuntu2204-220620.zip 
.\Ubuntu_2204.0.10.0_x64.appx | Add-AppxPackage Ubuntu_2204.0.10.0_x64.appx
弹出的窗口选择应用安装程序，等待安装完成
```
