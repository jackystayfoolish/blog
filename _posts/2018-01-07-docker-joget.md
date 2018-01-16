---
layout: post
title: Docker-Joget
category: Linux
---
* 目录
{:toc}

本文是[joget](http://www.joget.org)的镜像制作教程,Joget是一款开源的工作流软件。

## joget镜像制作
### 目标
以tomcat8镜像为基础镜像，实现:
- 设置joget运行时参数
- 将joget-enterprise-linux-6.0-RC2 的jw.war,jwdesigner.war拷贝到tomcat
- 将tomcat的配置文件替换
- 将joget依赖的类库包拷贝到指定位置

### 步骤
**建立目录**
下载joget的安装压缩包:
http://www.joget.org/downloads/enterprise/joget-enterprise-linux-6.0-RC2.tar.gz

进入目录docker-test/image/joget-v1，拷贝joget-enterprise-linux-6.0-RC2.tar.gz到该目录

**镜像配置文件Dockerfile**
```
FROM tomcat:8

# 环境变量
ENV JOGET_VERSION joget-enterprise-linux-6.0-RC2
ENV JOGET_VIRTUALHOST false
ENV INSTALLER_PATH ./
ENV ASPECTJ_VERSION aspectjweaver-1.8.5
ENV WFLOW_HOME /opt/joget/wflow/

# 设置运行时参数
ENV JAVA_OPTS -Xmx1024m -XX:MaxPermSize=256m -Dwflow.home=${WFLOW_HOME} -Dwflow.virtualhost=${JOGET_VIRTUALHOST} -javaagent:/opt/joget/lib/${ASPECTJ_VERSION}.jar

# 拷贝安装文件和脚本文件
COPY ${INSTALLER_PATH}${JOGET_VERSION}.tar.gz /opt/joget/
COPY run.sh /opt/joget/


# 将jw.war,jwdesigner.war拷贝到tomcat,将joget依赖的类库包拷贝到指定位置
RUN \
cd /opt/joget/ \
; tar xvfz ${JOGET_VERSION}.tar.gz \
; cd ${JOGET_VERSION}/ \
; find . -name 'jw*.war' -exec mv -t /usr/local/tomcat/webapps/ {} + \
; rm -rf apache-tomcat-* \
; cp -r *.* /opt/joget/ \
; cp -r * /opt/joget/ \
; mkdir -p /opt/joget/lib \
; cp /opt/joget/wflow/${ASPECTJ_VERSION}.jar /opt/joget/lib \
; rm -rf /opt/joget/${JOGET_VERSION}/ \
; rm /opt/joget/${JOGET_VERSION}.tar.gz

# 将tomcat的配置文件替换
COPY server.xml /usr/local/tomcat/conf/server.xml

# 启动tomcat
ENTRYPOINT ["/opt/joget/run.sh"]
CMD [""]
```
run.sh脚本内容:
```
cd /usr/local/tomcat
catalina.sh run
```
server.xml是从joget-enterprise-linux-6.0-RC2包里抽取出来的
注意Dockfile中使用了解压缩，在解压处理完后使用rm命令清除无用的文件，减小镜像文件体积

**构建镜像**
```
docker build -t joget:v1 .
```


## joget镜像运行:容器
### 目标
- 将容器的wflow数据保存到宿主机，保证容器在关闭后wflow数据不丢失
- 将容器的数据库端口和宿主机端口做映射

### 步骤
**建立目录**
docker-test/image/joget-mysql-v1下建立data文件夹，用于保存数据库的数据

**启动容器**
```
docker run -d -p 8080:8080 \
      --name joget \
      -v "$(pwd)"/data:/opt/joget/wflow \
      --link joget-mysql:jogetdb \
      joget:v1
```
-d: 以后台方式运行
--name: 容器的名称，可用该name来操作容器，文章后面会有相关操作说明
-p: 用于宿主和容器的端口映射，冒号前是宿主的端口,冒号后是容器暴露出来的端口
-v: 用于宿主和容器的目录映射，这里将docker-test/image/joget-v1/data目录作为容器/opt/joget/wflow目录的映射
--link:用于容器间的链接，这里把joget-mysql容器映射为jogetdb，这样在joget容器中可以通过命令ping jogetdb访问到joget-mysql容器
joget:v1表示该容器基于什么镜像运行

**检测容器是否正常运行**
在宿主访问http://localhost:8080/jw,在数据库初始化页面进行配置:
![image](http://kb.valuprosys.com/download/attachments/2818491/image2017-12-28_15-17-57.png?version=1&modificationDate=1514510231000&api=v2)

这里的host的来源是这里:
![image](http://kb.valuprosys.com/download/attachments/2818491/image2017-12-28_15-26-1.png?version=1&modificationDate=1514510231000&api=v2)
这里的root密码来源是这里:
![image](http://kb.valuprosys.com/download/attachments/2818491/image2017-12-28_15-27-3.png?version=1&modificationDate=1514510231000&api=v2)
保存后可以看到docker-test/image/joget-v1/data和docker-test/image/joget-mysql-v1/data目录里的内容都发生了变化，数据表都是以utf8的格式建立的。
