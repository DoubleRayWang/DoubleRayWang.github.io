---
layout: post
title: "Git 修改远程仓库地址"
date: 2018-02-22 14:41:15 +0800
categories: 知识点
tags: git
---

方法有三种：

1、 修改命令

```
git remote set-url origin [url]
```

2、 先删后加

```
it remote rm origin
git remote add origin [url]
```

3、 直接修改config文件