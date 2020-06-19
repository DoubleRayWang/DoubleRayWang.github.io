---
layout: post
title: "安装 node-sass 报错的解决记录"
date: 2020-06-18 23:13:05 +0800
categories: 实践之路
tags: [JavaScript,Node,yarn,npm]
---

## 问题

yarn 或 npm install 命令的时候，总是在安装node-sass这一步卡住。终其原因还是「墙」的问题。<!-- more -->

## 解决

npm和yarn的解决方法一样，这里以yarn为示例。

查看 yarn 配置 yarn config list

将 yarn 源切换至淘宝源

```powershell
yarn config set registry https://registry.npm.taobao.org
```

执行 `yarn install` 或者 `yarn`


1. 如果遇到错误 _error An unexpected error occurred: “EINVAL: invalid argument, symlink_

请在你的执行命令之后添加 `--no-bin-links`

```powershell
yarn install –no-bin-links
```

2. 如果遇到错误 _error: xxxx node-sass: Command failed_，将 sass-binary-site 添加至 config 中，指定 node-sass 从 npm 的淘宝源中下载。

```powershell
yarn config set sass-binary-site https://npm.taobao.org/mirrors/node-sass
```
