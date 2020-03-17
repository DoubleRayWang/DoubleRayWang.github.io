---
layout: post
title: "Harbor安装及使用"
date: 2020-03-05 16:10:10 +0800
categories: 实践之路
tags: Docker
---


参考： https://istone.dev/2019/07/19/harbor-install/

`tar xvf harbor-offline-installer-v1.10.1.tgz`

`cd harbor`

修改配置文件 harbor.yml

`./install.sh --with-notary --with-clair --with-chartmuseum` 参数可选，分别对应三个扩展功能

完成后就可以访问了

将harbor加入开机自启，通过服务的形式

参考：https://www.cnblogs.com/kirito-c/p/11145881.html

`sudo systemctl enable harbor`

`sudo systemctl start harbor`

`systemctl status harbor.service -l`  检查是否开启

