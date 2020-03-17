---
layout: post
title: "CentOS 8安装Docker"
date: 2020-02-17 10:10:10 +0800
categories: 实践之路
tags: Docker
---


## CentOS 8安装Docker

本文参考[Docker 官方 CentOS 安装文档](https://docs.docker.com/install/linux/docker-ce/centos/)<!-- more -->

### 安装依赖

	yum install -y yum-utils  device-mapper-persistent-data  lvm2
	
> yum options -h（帮助），-y（当安装过程提示选择全部为"yes"），-q（不显示安装的过程）	

### 更改yum源为国内源

	yum-config-manager --add-repo https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo

### 更新 yum 软件源缓存

	yum makecache

### 安装 docker-ce

	yum install docker-ce -y

如果报错： Problem: package docker-ce-3:19.03.7-3.el7.x86_64 requires containerd.io >= 1.2.2-3, but none of theproviders can be installed

问题的根源在于containerd.io的版本过低，这里通过安装较高版本的containerd.io即可解决问题。
访问官网：https://containerd.io/downloads/，根据提示，直接安装二进制包：
	
	wget https://github.com/containerd/containerd/releases/download/v1.3.2/containerd-1.3.2.linux-amd64.tar.gz tar xvf containerd-1.3.2.linux-amd64.tar.gz

这时候又有一个问题就是官网下载的网速奇慢，所以直接使用DNF工具从docker-ce的仓库（https://download.docker.com/linux/centos/7/x86_64/stable/Packages/）中下载：
	
	dnf install https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm
	
	yum install docker-ce -y
	
	docker --version （查看安装是否成功）

	
###开启Docker服务

	systemctl enable docker （开机自启）
	
	systemctl start docker  （启动docker服务）
	
	systemctl restart docker 重启
	
### Docker加速

https://blog.csdn.net/varyall/article/details/104266706

该脚本可以将 --registry-mirror 加入到你的 Docker 配置文件 /etc/docker/daemon.json 中。适用于 Ubuntu14.04、Debian、CentOS6 、CentOS7、Fedora、Arch Linux、openSUSE Leap 42.1，其他版本可能有细微不同。更多详情请访问文档。

curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io


`docker pull node:12.16.1-alpine`

`docker run -it --name node-test node:12.16.1-alpine`

我们在doker容器中直接运行node服务，并且打印字符串。这里我们通过run指令，用node镜像创建的一个叫node-test的容器， -i(interactive,表示开启交互), -t(terminal, 表示终端)，-it是两个参数的简写，表示通过命令行的方式运行。还可以添加-d(deamon)表示在后台运行。退出控制台后，我们通过docker ps -a查看当前的所有容器.

接下来我们再次启动这个容器，并进入其中运行bash命令行:

`docker exec -it node-test sh`

我们一般可能会在容器启动后进入容器，常用的是docker attach 镜像id，但是启动镜像的时候如果没有带 参数 -it的话，attach进去后可能是日志界面，并不能执行命令。所以我们会用 docker exec -it [镜像id] /bin/bash/

平常的容器一般都可以执行/bin/bash，但是alpine没有，改成 docker exec -it [镜像id] sh 就好了。


## 安装docker-compose

下载docker-compose二进制文件安装，因为github网速慢，所以改到了daocloud

    curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.4/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
 
添加可执行权限 

 `chmod +x /usr/local/bin/docker-compose`
 
测试安装结果 

`docker-compose --version`


