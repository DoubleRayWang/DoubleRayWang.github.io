---
layout: post
title: "css-alt"
date: 2017-11-14 14:41:15 +0800
categories: 知识点
tags: CSS
---

为了增强可访问性，让自己不忘给图片标签加上 alt 属性，有人在项目中加入了如下的全局 CSS 样式，值得学习

```css
img[alt=""],img:not([alt]){
    border: 2px solid #c00;
}
```