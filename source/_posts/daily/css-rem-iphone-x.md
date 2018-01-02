---
layout: post
title: "高清适配方案中iPhone X全屏兼容"
date: 2018-01-02 09:12:15 +0800
categories: 知识点
tags: [CSS]
---

1. 需要在viewport中添加 `viewport-fit=cover`，js 和 meta 标签中都要添加；

2. 同时iPhone X屏幕底部圆弧区域占位高度使用 `calc(constant(safe-area-inset-bottom) * 3)` 作为`padding-bottom`， `margin-bottom`等高度属性的值。iPhone X上，高清适配方案中，此值等价于`.34rem`，即34逻辑像素。根据实际情况可以修改* 3这个系数来调整占位区域的高度，但一定要使用`constant(safe-area-inset-bottom)`。在ios11下，非iPhone X的设备此值为0，使用时需要注意样式覆盖的情况。