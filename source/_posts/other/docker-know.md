---
layout: post
title: "Docker入门及实践"
date: 2020-02-25 18:10:10 +0800
categories: 实践之路
tags: Docker
---

Docker官方文档：https://docs.docker.com/get-started/

腾讯云的中文文档：https://cloud.tencent.com/developer/information/docker%E4%B8%AD%E6%96%87%E6%96%87%E6%A1%A3

DockerInfo中文文档：http://www.dockerinfo.net/document

Docker — 从入门到实践： https://vuepress.mirror.docker-practice.com/
<!-- more -->

----------------------------------------------------------------------------------------
所有命令可参考：https://www.runoob.com/docker/docker-command-manual.html


- 列出本地已有镜像：  `docker images`
- 移除镜像： `docker rmi 或者 docker image rm [id|name]`

>注意：在删除镜像之前要先用 docker rm 删掉依赖于这个镜像的所有容器。可加参数 -f 强制删除。
>
- 生成镜像： `docker build -t [repositoryName]:[tag] [Directory]`

命令行参数参考：https://www.runoob.com/docker/docker-build-command.html

> “Directory” 是 Dockerfile 所在的路径
> 一个镜像不能超过 127 层,编写Dockerfile时应注意

- 从镜像run容器：`docker run -d -p 1202:3000 --name="quoted" tmd.ictr.cn:89/web-server/quoted:1.0`

命令行参数参考：https://www.runoob.com/docker/docker-run-command.html

- docker重启退出的容器 docker attach [container]

`docker ps`: 查看当前运行的容器
`docker ps -a`:查看所有容器，包括停止的。
`docker ps -l `:查看最新创建的容器，只列出最后创建的。
`docker ps -n=2`:-n=x选项，会列出最后创建的x个容器。

- 移除容器：`docker rm [id]`

查看日志： `docker logs name`
