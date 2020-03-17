---
layout: post
title: "CentOS 8安装Node"
date: 2020-02-23 12:10:10 +0800
categories: 实践之路
tags: Node
---

### wget下载源码

在需要安装nodejs的目录中执行

`wget https://nodejs.org/dist/v12.16.1/node-v12.16.1-linux-x64.tar.xz`

https://nodejs.org/dist 这个目录下可以找到各个版本的源码

### 解压

`xz -d node-v12.16.1-linux-x64.tar.xz`

`tar -xvf node-v12.16.1-linux-x64.tar`
    
### 配置软连接

要想node能够在全局能够使用，需要添加软连接，配置完即可使用

`ln -s /usr/local/node-v12.16.1-linux-x64/bin/node /usr/local/bin/node`
 
`ln -s /usr/local/node-v12.16.1-linux-x64/bin/npm /usr/local/bin/npm`

### 全局安装的包执行shell时找不到命令的解决方法

https://blog.csdn.net/m0_37263637/article/details/81942435


