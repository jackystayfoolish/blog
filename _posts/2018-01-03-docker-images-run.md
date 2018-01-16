---
layout: post
title: Docker-制作和运行镜像
category: Linux
---
* 目录
{:toc}

本文目的是展示docker镜像的制作和运行，以mysql为例。

## mysql客户化镜像制作
### 目标
制作mysql镜像,该镜像以mysql5.6镜像为基础，并实现:
- 添加root用户密码
- 修改配置文件使得默认字符集为utf-8

### 步骤

**建立目录**
docker-test/image/joget-mysql-v1

**镜像配置文件Dockerfile**
```
FROM mysql:5.6

# 添加root用户密码
ENV MYSQL_ROOT_PASSWORD root
# 修改配置文件使得默认字符集为utf-8
COPY mysql.cnf /etc/mysql/conf.d
COPY mysqld.cnf /etc/mysql/mysql.conf.d
```
mysql.cnf内容为:
```
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8
```
mysqld.cnf内容为:
```
[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
#log-error      = /var/log/mysql/error.log
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

collation-server = utf8_unicode_ci
init-connect='SET NAMES utf8'
character-set-server = utf8
```
**构建镜像**
cd到目录joget-mysql-v1，执行:
```
docker build -t joget-mysql:v1 .
```
docker build命令:默认会读取目录下名称为Dockfile的文件，如果想使用其它名称的文件可以加入 -f 选项
-t选项: joget-mysql是镜像名称，冒号后边的v1是tag名称
不要忘记最后的点'.' ，这个是指上下文，这里表示当前目录

**镜像管理**
列出所有镜像:`docker image ls`
删除镜像:`docker image rm joget-mysql:v1`
删除所有无用镜像:`docker image prune`

## mysql镜像运行:容器
### 目标
- 将容器的数据保存到宿主机，保证容器在关闭后数据库的数据不丢失
- 将容器的数据库端口和宿主机端口做映射

### 步骤
**建立目录**
docker-test/image/joget-mysql-v1下建立data文件夹，用于保存数据库的数据

**启动容器**
启动容器
```
docker run -d \
      --name joget-mysql \
      -p 3307:3306 \
      -v "$(pwd)"/data:/var/lib/mysql \
      joget-mysql:v1
```
-d: 以后台方式运行
--name: 容器的名称，可用该name来操作容器
-p: 用于宿主和容器的端口映射，冒号前是宿主的端口,冒号后是容器暴露出来的端口
-v: 用于宿主和容器的目录映射，这里将docker-test/image/joget-mysql-v1/data目录作为容器/var/lib/mysql目录的映射
joget-mysql:v1表示该容器基于什么镜像运行

### 检查容器
可以通过日志命令查看容器运行日志：`docker logs joget-mysql`

也可以进入容器进行检查，进入的命令为:`docker exec -it joget-mysql bash`

进入容器后，测试数据库连接是否正常:`mysql -uroot -p`

### 容器管理
关闭容器:`docker container stop joget-mysql`
关闭后可以使用start再启动:`docker container start joget-mysql`
如果关闭容器后，以后不想再启动了，可以删除该容器:`docker rm joget-mysql`
也可以使用命令prune删除所有不在启动状态的容器:`docker container prune`
