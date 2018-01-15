---
layout: post
title: Docker-安装
category: Meta
---
## 安装环境
操作系统:centos

最低要求版本:7

## 通过yum安装
### 什么是yum
Linux分为
- RedHat系列包括如CentOS,软件安装包格式为rpm
yum用于管理rpm包
- Debian系列包括如Ubuntu,软件安装包格式为deb
apt-get用于管理deb包

### 为什么推荐用yum安装
如果通过rpm或deb软件包安装需要下载对应操作系统版本的软件包，还要去下载缺少的依赖包。而通过yum或apt-get可以把下载和依赖安装都交给系统自动处理。

### yum仓库配置
安装yum所需工具包
```
yum install -y yum-utils \
device-mapper-persistent-data \
lvm2
```
添加docker仓库
```
yum-config-manager \
--add-repo \
https://download.docker.com/linux/centos/docker-ce.repo
```

### 安装
```
yum install docker-ce
```
```
systemctl start docker
```
```
docker run hello-world #如果运行成功可以看到hello world的提示信息
```
