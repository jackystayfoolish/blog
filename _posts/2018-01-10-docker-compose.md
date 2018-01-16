---
layout: post
title: Docker-Compose
category: Linux
---

* 目录
{:toc}

## 服务Service
在docker中系统架构中，从低到高为container->servcie->stack

service是"生产环境的容器",可以认为它对容器进行了封装，可以对容器进行生产环境系统资源分配，也可以运行多个相同容器来做负载均衡等。

## 为什么使用docker compose
如果需要运行的容器增多了，比如有跑web应用的容器、有跑db的容器，如果分开单独管理每个容器的运行会很费力。可以把每个容器封装为一个服务，在docker compose里管理。

Docker compose 的好处:

- 将多个服务的管理在一个配置文件中集中管理
热加载服务
- 可以对每个服务进行CPU、内存等资源分配

## 运行joget-mysql和joget服务
### 目录结构
在docker-test目录下新建compose目录，目录结构为:
![i](http://kb.valuprosys.com/download/thumbnails/2818503/image2017-12-29_9-30-2.png?version=1&modificationDate=1514511002000&api=v2)
mysql-data放mysql的数据,wflow-data放joget wflow的数据
### compose配置文件：docker-compose.yml
```
version: '3'
services:
  #服务名称
  jogetdb:
    #镜像名称
    image: joget-mysql:v1
    #端口映射
    ports:
     - "3307:3306"
    #数据卷映射
    volumes:
     - ./mysql-data:/var/lib/mysql
    #加入网络
    networks:
     - jogetnet
  joget:
    image: joget:v1
    ports:
     - "8080:8080"
    volumes:
     - ./wflow-data:/opt/joget/wflow
    networks:
     - jogetnet
    #依赖服务
    depends_on:
     - "jogetdb"
networks:
  jogetnet:
```
这个文件中jogetdb是服务名称,它运行的镜像是joget-mysql，另一个服务是joget,运行的镜像是joget，选项说明：

选项|说明|对比docker run命令
---|---|---
image|镜像名称|![1](http://kb.valuprosys.com/download/thumbnails/2818503/image2017-12-29_9-42-4.png?version=1&modificationDate=1514511724000&api=v2)
ports|端口映射|![1](http://kb.valuprosys.com/download/thumbnails/2818503/image2017-12-29_9-43-4.png?version=1&modificationDate=1514511784000&api=v2)
volumes|数据卷映射，注意这里的mysql-data和wflow-data文件夹必须存在，否则服务是无法启动的|![1](http://kb.valuprosys.com/download/thumbnails/2818503/image2017-12-29_9-44-11.png?version=1&modificationDate=1514511851000&api=v2) 使用-v参数宿主机的对应文件夹不存在时会自动创建，这个和volumes选项是不同的
networks|加入网络，可以看到jgoetdb和joget这两个服务都加入了名为jogetnet的网络，两个服务之间可以相互ping通。 一般都推荐使用networks来管理容器间的通信。|![1](http://kb.valuprosys.com/download/thumbnails/2818503/image2017-12-29_9-45-43.png?version=1&modificationDate=1514511943000&api=v2) 注意networks和run命令的link不是一回事，这里放在一起对比是因为networks取代了link的功能。
depends_on|服务依赖，这里joget依赖jogetdb服务，注意这里jogetdb服务启动后，就会启动joget服务，至于jogetdb的mysql是否已真正启动是没有影响的。|

### 命令
命令|说明|执行效果
---|---|---
docker swarm init|启动swarm，swarm指的是一组运行了docker的机器集群，启动swarm后，其它机器可以加入该集群	|![1](http://kb.valuprosys.com/download/attachments/2818503/image2017-12-29_10-22-26.png?version=1&modificationDate=1514514146000&api=v2) 可以看到swarm启动后，可以通过docker swarm join --token命令动态加入该集群
docker stack deploy -c docker-compose.yml joget|部署stack,注意使用该命令前需要先执行docker swarm init，相互关联的服务jogetdb和joget组成了stack，-c选项指定compose文件，joget是stack的名称|![1](http://kb.valuprosys.com/download/attachments/2818503/image2017-12-29_10-25-31.png?version=1&modificationDate=1514514331000&api=v2) 可以看到先启动了jogetnet网络，再创建了两个服务
docker stack rm joget|关闭stack，同时会关闭相关的服务、网络、数据卷|![1](http://kb.valuprosys.com/download/attachments/2818503/image2017-12-29_10-18-22.png?version=1&modificationDate=1514513902000&api=v2)
docker swarm leave --force|关闭swarm|![1](http://kb.valuprosys.com/download/attachments/2818503/image2017-12-29_10-21-1.png?version=1&modificationDate=1514514062000&api=v2)
执行了docker stack deploy -c docker-compose.yml joget后就可以通过http://localhost:8080/jw访问joget了

## 生产环境
### 热加载服务
现在跑着jogetdb和joget服务，如果想再增加两个joget服务，此时只要修改compose配置文件后再deploy一次，docker会新增这两个服务，而原来运行着的jogetdb和joget服务不受影响。

修改docker-compose.yml,增加:
```
joget2:
  image: joget:v1
  ports:
   - "8081:8080"
  volumes:
   - ./wflow-data2:/opt/joget/wflow
  networks:
   - jogetnet
  depends_on:
   - "jogetdb"
joget3:
  image: joget:v1
  ports:
   - "8082:8080"
  volumes:
   - ./wflow-data3:/opt/joget/wflow
  networks:
   - jogetnet
  depends_on:
   - "jogetdb"
```
可以看到增加了joget2和joget3服务，对应的宿主端口为8081和8082，数据卷为wfow-data2和wflow-data3

这时运行docker stack deploy -c docker-compose.yml joget命令，可以访问http://locahost:8081/jw和http://localhost:8082/jw会进入数据库初始配置页面

### 资源分配
可以给服务分配cpu和内存资源，修改docker-compose.yml:
```
joget3:
  image: joget:v1
  ports:
   - "8082:8080"
  volumes:
   - ./wflow-data3:/opt/joget/wflow
  networks:
   - jogetnet
  depends_on:
   - "jogetdb"
  deploy:
    resources:
      limits:
        cpus: '0.50'
        memory: 1000M
      reservations:
        cpus: '0.25'
        memory: 500M
```
可以看到增加了deploy选项

limits是最大使用量,这里最大使用50%的cpu和1000M的内存

reservations是最小使用量，这里最小使用25%的cpu和500M的内存

注意资源分配过小可能导致服务启动不了，系统会提示增大内存，可以通过命令docker service ls来查看服务是否已正常启动
