---
layout: post
title: "node多版本管理（Windows）"
date: 2020-04-13 10:10:10 +0800
categories: 杂记
tags: NODE
---

旧的项目依赖低版本的node，但是现在的node版本变化太快，所以使用 nvm-windows 来管理不同版本的node。<!-- more -->

软件地址：https://github.com/coreybutler/nvm-windows/releases

### 卸载现有的node和npm

请注意，在安装NVM for Windows之前，您需要卸载任何现有版本的node.js. 还删除可能保留的任何现有nodejs安装目录（例如，C\Program Files\nodejs）。NVM生成的符号链接不会覆盖现有（甚至是空的）安装目录。

还应该删除现有的npm安装位置（例如C\Users\<user>\AppData\Roaming\npm），以便正确使用nvm安装位置。

### 安装

在releases中下载最新版本nvm-setup.zip，解压后，是一个安装文件，直接安装即可。

由于国内在一些情况下有些特殊。Node.js 官方镜像源又在国外，经常通过 nvm 安装 Node.js 时，速度比较慢，或者没有响应。

根据这种情况，nvm 允许更改安装的镜像源，我们可以将镜像源切换到国内的淘宝提供的镜像源。

默认安装目录（C:\Users\[username]\AppData\Roaming\nvm）下的settings.txt文件中添加：

```
node_mirror: https://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```


### 使用
```
# 查看已安装的node
nvm -ls 
# 安装指定版本的node
nvm install <version>
# 指定自己要使用的版本号
nvm use <version>
# 卸载指定版本的node
nvm uninstall <version>
```

> 具体参考：https://github.com/coreybutler/nvm-windows#usage

