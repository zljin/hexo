---
title: Docker
date: 2021-03-07 12:00:45
tags: 技术
categories: 工具
---
> docker是一款容器应用引擎,一种轻量级的操作系统虚拟化解决方案
> docker可以看成游轮(服务器)上的集装箱(容器),容器是由一个应用程序和其依赖的应用环境(操作系统)打成一个包
> docker可以快速搭建中间件环境,快速部署应用


## 安装Docker
```
centos
yum install -y docker # centos安装
sudo apt install docker.io  # ubuntu安装
## 国内镜像加速配置
vi /etc/docker/daemon.json
{
  "registry-mirrors" : ["https://docker.mirrors.ustc.edu.cn"]
}

docker version
```
## 常用命令
### 容器的开启关闭
```
service docker start //启动docker后台服务
systemctl start docker
systemctl restart docker
systemctl status docker
systemctl stop docker
systemctl enable docker //开机自动启动
docker info//概要信息
docker --help
```

> 镜像可理解为Java的类，容器可来理解Java类new出来的无数对象

### 镜像的操作
```
docker images --查看镜像
//Resposity:镜像名称	Tag:镜像标签	IMAGE ID :镜像ID	CREATED : 镜像的创建日期
//这些镜像都是存储在Docker宿主机的/var/lib/docker目录下

docker search 镜像名称  --搜索镜像
//Stars:用户评价		AutoMated:自动构建,表示该镜像由DockerHub自动构建流程创建的
docker pull ** --拉取镜像
docker rmi 镜像ID --按镜像id删除镜像,必须先删掉对应的容器
```

### 容器操作

```
# 查看容器
docker ps //查看正在运行的容器 
docker ps -a //查看所有的容器 
docker ps -l //查看最后一次运行的容器 
docker ps -f status=exited //查看停止的容器 
docker logs containId //查看容器日志

# 创建容器
## 交互式创建：exit之后容器会stop
docker run -it --name=myUuntu1 ubuntu:15.10 /bin/bash
## 守护式创建：
docker run -di --name=myUuntu2 ubuntu:15.10
### 守护式创建后：进入容器
docker exec -it 容器ID /bin/bash 
### 守护式创建后：退出容器
exit

# 启动、停止、删除容器
docker stop 容器名称(或容器ID)
docker start 容器名称(或容器ID)
docker rm 容器名称(或容器ID)

# 查看容器相关数据
## 容器所有信息
docker inspect 容器名称(或容器ID)
## 容器ip信息
docker inspect --format='{{.NetworkSettings.IPAddress}}' 容器名称(或容器ID)

# 文件拷贝
## 宿主机文件导入到容器
docker cp 需要拷贝的文件或目录 容器名称:容器目录
## 导出到宿主机文件
docker cp 容器名称:容器目录/文件 本地文件名字
e.g:
docker cp tensquare_elasticsearch:/usr/share/elasticsearch/config/elasticsearch.yml  /usr/share/elasticsearch.yml

# 目录挂载
对宿主机目录:容器目录 进行映射,这样可通过修改宿主机目录来修改容器目录

用法: -v (是目录映射) 宿主机目录:容器目录  
docker run -di -v /usr/local/myhtml:/usr/local/myhtml --name=myUuntu3 ubuntu:15.10

--privileged=true来解决挂载的目录无权限问题
```

## Docker安装软件
```
# tomcat
1. 拉取镜像
docker pull tomcat:7-jre7
2. 创建容器
-p :表示地址映射调用 宿主9001端口操作容器端口8080
-v :目录映射 通过将文件上传的宿主文件并且映射到容器对应的文件中
-i :以交互模式运行容器
-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用
-d :后台运行容器，并返回容器ID

docker run -di --name=Mytomcat -p 9001:8080  
-v /usr/local/webapps:/usr/local/tomcat/webapps tomcat:7-jre7

# redis
docker pull Redis
docker run -di --name=myRedis -p 6379:6379 redis
# nginx
docker pull nginx
docker run -di --name=mynginx -p 80:80 nginx
```

## DockerFile
> 由一系列命令和参数构成的脚本，这些命令应用于基础镜像并最终创建一个新的镜像
> 基础镜像：如Ubuntu Or Cenos 就是操作系统级别的镜像，如果要创建一个镜像那么这个镜像本身是要代操作系统的

意义：
* 对于开发人员可为开发人员提供一个完全一致的开发环境
* 对于测试人员，可以直接拿开发时所构建的dockfile文件构成的新镜像开始工作
* 对于运维人员，再部署的时候，可以实现应用的无缝移植

### 常用命令

|  命令 | 作用  |
|  ----  | ----  |
| FROM image_ name:tag  |定义了使用哪个基础镜像启动构建流程 |
| MAINTAINER user_ name  | 声明镜像的创建者 |
| ENV key value | 设置环境变量(可以写多条) |
| RUN command |是Dockerfile的核心部分(可以写多条)|
|ADD source_dir/file dest_ dir/file |将宿主机的文件复制到容器内，如果是一个压缩文件， 将会在复制后自动解压|
|copy source_dir/file dest_ dir/file|与上面相同，只是不解压|
|WORKDIR path_ _dir| 设置工作目录|

### 脚本创建镜像实际操作

> 以DockerFile来构建一个jdk镜像为例

```
//1.首先构建一个目录:目的是在这个目录下构建一个dockfile文件,并将需要的压缩包放入到这个目录下
mkdir -p /usr/local/dockerjdk8

//2.将压缩包放入
cp softwares/jdk-8u201-linux-x64.tar.gz /usr/local/dockerjdk8/

//3.编写脚本,名字强行规定不能更改
vi Dockerfile
```

> 下面是脚本的编写
```sh
FROM centos:7 --为基础镜像,此镜像必须存在否则会自行下载
MAINTAINER leonard --指定镜像的创建者信息
WORKDIR /usr  
RUN mkdir /usr/local/java
ADD jdk-8u201-linux-x64.tar.gz /usr/local/java

ENV JAVA_HOME /usr/loca1/java/jdk1.8.0_201  --配置环境变量
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH $JAVA_HOME/bin/dt.jar:$JAVA_HOME/1ib/tools.jar:$JRE_HOME/1ib:$CLASSPATH
ENV PATH $JAVA_HOME/bin:$PATH
```

> 输入命令开始构建
```
docker build -t='jdk1.8' .  //在当前目录下寻找docfile脚本文件
docker images   //可清晰的看到镜像的生成 
```

### docker私有仓库
> 公有仓库是那种通用性的仓库,私有仓库是公司开发的企业级应用,为了在一个局域网共享这个镜像

```
1.docker pull registry --拉取私有仓库镜像
2.docker run -di --name=registry -p 5000:5000 registry   --启动私有仓库
3.curl http://192.168.2.6:5000/v2/_calalog  --->  {"repostories:[]"} -- 私仓创建成功
```

> 下面将镜像上传至私有仓库

```
1. 修改daemon.json
vi /etc/docker/daemon.json
添加以下内容退出,目的是:让docker容器信任私有仓库的地址
{"insecure-registries":["192.168.2.6:5000"]}
5.systemctl restart docker
(1)标记此镜像为私有仓库的镜像
docker tag jdk1.8 192.168.2.6:5000/jdk1.8
(2)上传标记的镜像
docker push 192.168.2.6:5000/jdk1.8
```
