---
layout: post
title: "文本溢出 text-overflow"
date: 2017-03-02 20:19:15 +0800
categories: 实践之路
tags: CSS
---


　　有时我们会遇到当长文本超出他的容器时做文本溢出的处理，之前有一个比较 low 的 [方法](http://doubleray.win/2017/02/03/js-effect/#9-超出部分以省略号显示)，用 jQuery 实现的（也是为了兼容性）。本文主要总结一下实现这个效果可用的方法及需要注意的小坑，方便日后查阅。<!-- more -->

## 单行文本

　　如果实现单行文本的溢出显示省略号同学们应该都知道用text-overflow:ellipsis属性来，当然还需要加宽度width属来兼容部分浏览。

> **实现方法：**

```css
p{
    /*display: block;需为块级元素*/
    overflow: hidden;
    text-overflow:ellipsis;
    white-space: nowrap;
}
```

> **效果如下：**

<p style="overflow: hidden;text-overflow:ellipsis;white-space: nowrap;width:300px;background-color:#ccc">Lorem ipsum dolor sit amet, consectetur adipisicing elit. Accusamus, animi beatae dicta dolorem excepturi facilis, illum maiores quia, repellat sit vero voluptate voluptates? Aspernatur et iusto, omnis qui quo velit.</p>

<hr>

## 多行文本

### 方法一

> **实现方法：**

```css
p{
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 3;
    overflow: hidden;
}
```

> **效果如下：**

<p style="overflow: hidden;display: -webkit-box;-webkit-box-orient: vertical;-webkit-line-clamp: 3;width:300px;background-color:#ccc">Lorem ipsum dolor sit amet, consectetur adipisicing elit. Accusamus, animi beatae dicta dolorem excepturi facilis, illum maiores quia, repellat sit vero voluptate voluptates? Aspernatur et iusto, omnis qui quo velit.</p>

<hr>

> **适用范围：**

因使用了 WebKit 的 CSS 扩展属性，该方法适用于 WebKit 浏览器及移动端；

**-webkit-line-clamp用来限制在一个块元素显示的文本的行数。 为了实现该效果，它需要组合其他的WebKit属性。**

常见结合属性：

- display: -webkit-box; 必须结合的属性 ，将对象作为弹性伸缩盒子模型显示；
- -webkit-box-orient 必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 。

### 方法二

> **实现方法：**

```css
p{
    position: relative; 
    line-height: 20px; 
    max-height: 40px;
    overflow: hidden;
}
p::after{
    content: "..."; 
    position: absolute;
    bottom: 0; 
    right: 0;
    padding-left: 40px;
    background: -webkit-linear-gradient(left, transparent, #fff 55%);
    background: -moz-linear-gradient(right, transparent, #fff 55%);
    background: linear-gradient(to right, transparent, #fff 55%);
}
```

> **效果如下：**

<style>.demo-css-ellipsis{position: relative;line-height: 20px;max-height: 60px;overflow: hidden;width: 300px}.demo-css-ellipsis::after{content: "..."; position: absolute;bottom: 0; right: 0;padding-left: 20px;background: -webkit-linear-gradient(left, transparent, #fff 35%);background: -moz-linear-gradient(right, transparent, #fff 35%);background: linear-gradient(to right, transparent, #fff 35%)}</style>

<p class="demo-css-ellipsis">Lorem ipsum dolor sit amet, consectetur adipisicing elit. Accusamus, animi beatae dicta dolorem excepturi facilis, illum maiores quia, repellat sit vero voluptate voluptates? Aspernatur et iusto, omnis qui quo velit.</p>

<hr>

> **适用范围：**

　　该方法适用范围广，但文字未超出行的情况下也会出现省略号,可结合 js 优化该方法。

- 将height设置为line-height的整数倍，防止超出的文字露出。
- 给p::after添加渐变背景可避免文字只显示一半。
- 由于ie6-7不显示content内容，所以要添加标签兼容ie6-7（如：`<span>…<span/>`）；**兼容ie8需要将::after替换成:after**。

<hr>