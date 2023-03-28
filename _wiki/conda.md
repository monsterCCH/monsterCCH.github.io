---
layout: wiki
title: conda 使用指南
categories: env
description: conda 使用指南
keywords: conda
topmost: true
---

## conda环境激活、退出、创建、删除

### 激活

```sh
conda activate <env_name>
```

### 退出

```sh
conda deactivate
```

### 创建

```sh
conda create -n <env_name> python=<version>
```

### 删除

```sh
conda remove -n <env_name> --all
```

## conda环境导入、导出 

### 导入

```sh
conda env create -f <env_file>
```

### 导出

```sh
conda env export > <env_file>
```

## conda环境列表

```sh   
conda env list
```

## conda环境配置

### 配置镜像

```sh   
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
``` 

### 配置代理

```sh
conda config --set proxy_servers.http http://<proxy_ip>:<proxy_port>
conda config --set proxy_servers.https https://<proxy_ip>:<proxy_port>
```

## conda包管理

### 安装

```sh
conda install <package_name>
```
    
### 卸载

```sh
conda uninstall <package_name>
```
    
### 更新

```sh
conda update <package_name>
```

### 查看

```sh
conda list
```

## conda包导入、导出

### 导入

```sh
conda install --file <package_file>
```

### 导出

```sh
conda list --explicit > <package_file>
```

## conda包搜索

```sh
conda search <package_name>
```

## conda包清理
    
```sh
conda clean -a
```
