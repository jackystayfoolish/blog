---
layout: post
title: Docker-Volume
category: Linux
---

* 目录
{:toc}

## 数据卷
数据的持久化可以通过两种方式：
1.主机目录和容器目录做映射，在docker run命令中使用-v参数:
`-v "$(pwd)"/data:/var/lib/mysql`

2.docker自身管理的数据卷
首先创建一个数据卷:
`docker volume create sqlserver-volume`
再将数据卷和容器目录做映射，在docker run命令中使用-v参数:
`-v sqlserver-volume:/var/opt/mssql`

注意在较新版本的docker中,只需要执行上面的-v参数的命令,docker会隐式执行数据卷的创建

## 数据卷的操作
列出所有数据卷:
`docker volume ls`
数据卷占用空间查看:
`docker system df -v`
数据卷删除:（注意这个操作会使容器持久化的数据删除）
`docker volume rm <volume-name>`

## 举例
运行msql-server-linux镜像:
```
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=Valuprosys1224" \
   -e "MSSQL_COLLATION=Chinese_PRC_CI_AS" \
   -p 1401:1433 --name sqlserver \
   -v sqlserver-volume:/var/opt/mssql \
   -d microsoft/mssql-server-linux:2017-latest
```
- ACCEPT_EULA=Y:表示接受条款
- MSSQL_SA_PASSWORD:表示sa的密码，密码规则：至少8位，英文数字混合，大小写混合
- MSSQL_COLLATION:默认是使用Latin的排序规则，此处改为简体中文，否则中文会显示乱码
- -p:将容器的1433端口暴露给主机，主机监听端口为1401
- --name:容器名称
- -v:使用了volume container而不是主机目录，见下面的说明
