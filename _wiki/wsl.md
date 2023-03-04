---
layout: wiki
title: wsl tutorial
categories: wsl
description: windows subsystem WSL 使用教程
keywords: wsl
---

## 管理工具
微软商店的 `WSL Manager` 图形化管理工具十分好用，可以配置 wsl 的各项参数，包括systemd、内存上限、cpu上限等，只是需要收费。

## 常用命令
```shell
wsl -l -v # 列出当前安装LINUX发行版的状态及版本
wsl --update #更新WSL
wsl --set-version <distribution name> <versionNumber> # 设置wsl版本
wsl -d <Distribution Name> -u <User Name> # 运行特定的 Linux 发行版
wsl --help # 帮助指令
wsl --shutdown # 关闭 WSL
wsl --unregister <DistributionName> # 注销或卸载LINUX发行版
```

## 启用 systemd
在 WSL 镜像中执行
```shell
echo -e "[boot]\nsystemd=true" | sudo tee -a /etc/wsl.conf
```
执行完后重启 wsl, 通过 `wsl --shutdown`命令关闭 WSL，来进行完整的重启。

**判断是否启用成功**
```shell
ps --no-headers -o comm 1
# 输出进程号为 1 的进程名
```
如果返回是`init`则未启用成功，为`systemd`则启动成功

**查看已启用的 systemd service**
```shell
systemctl list-units --type=service
systemctl list-unit-files --type=service --state=enabled
```

## 常见问题
### 和vpn冲突导致无法启动
产生原因和解决方法分析： 代理软件和wsl2的sock端口冲突，使用netsh winsock reset重置修复。

Proxifer开发人员解释如下： 如果Winsock LSP DLL被加载到其进程中，则wsl.exe将显示此错误。最简单的解决方案是对wsl.exe使用WSCSetApplicationCategory WinAPI调用来防止这种情况。在后台，该调用在HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WinSock2\Parameters\AppId_Catalog中为wsl.exe创建一个条目。 这将告诉Windows不要将LSP DLL加载到wsl.exe进程中

详见：

[WSL2启动时提示：参考的对象类型不支持尝试的操作](https://blog.csdn.net/fangye945a/article/details/123832623)

[工具下载](https://github.com/dyingsu/nolsp)

## 参考
[WSL2中的高级设置配置](https://learn.microsoft.com/zh-cn/windows/wsl/wsl-config)
