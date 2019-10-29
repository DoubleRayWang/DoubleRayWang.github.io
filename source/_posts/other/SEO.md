---
layout: post
title: "单页面 SEO 优化"
date: 2018-10-10 10:10:10 +0800
categories: 杂记
tags: SEO
---


## 通用方法

1. logo上加首页的地址，并且只将logo是放在`h1`标签中
2. 外链优化，提高关键词排名，`meta`信息
3. 对外联css,以及js使用了延迟加载以及`dns-prefetch`，`preload`
4. 使用`Robots.txt`

... 等等

## 服务端渲染(后端渲染SSR)

可以使用[Nuxt.js](https://zh.nuxtjs.org/guide)作为框架来处理项目的所有UI呈现

+ 好处：前端耗时少（前端只负责将`html`进行展示），利于SEO
+ 坏处：网络传输数据量大，占用（部分、少部分）服务器运算资源，response 出的数据量会（稍）大点，模板改了前端的交互和样式什么的一样得跟着联动修改

## 预渲染的方式(PreRender)

[vue-meta-info](https://github.com/muwoo/vue-meta-info) 动态设置`meta`信息，配合 [prerender-spa-plugin](https://github.com/chrisvfritz/prerender-spa-plugin) 实现关键页面的预渲染。

> 需要路由使用`history`模式，使用`hash`模式会导致失效

+ 缺点：不能很好地处理用户独特性路由，预渲染路由过多的话会增加编译时间。

