---
layout: post
title: "有趣的 fullPage.js"
date: 2017-02-03 20:57:15 +0800
categories: 学习笔记
tags: JavaScript
---

　　如今我们经常能见到全屏网站，尤其是国外网站。这些网站用几幅很大的图片或色块做背景，再添加一些简单的内容，显得格外的高端大气上档次。比如 iPhone 5C 的介绍页面（查看），QQ浏览器的官网站。如果你也希望你的网站能设计成全屏的，显得更上档次，你可以试试 fullPage.js。<!-- more -->

<p class="tc"><a class="btn btn-success btn-lg" title="演示" href="http://doubleray.win/FullPages/" target="_blank">演 示</a> <a class="btn btn-success btn-lg" title="下载" href="https://github.com/DoubleRayWang/FullPages" target="_blank">下 载</a></p>

<hr><br>

## 1. 简介

　　fullPage.js 是一个基于 jQuery 的插件，它能够很方便、很轻松的制作出全屏网站，主要功能有：

- 支持鼠标滚动
- 支持前进后退和键盘控制
- 多个回调函数
- 支持手机、平板触摸事件
- 支持 CSS3 动画
- 支持窗口缩放
- 窗口缩放时自动调整
- 可设置滚动宽度、背景颜色、滚动速度、循环选项、回调、文本对齐方式等等

<div class="tc"><img src="/styles/images/js/fullpage.png" alt="fullpage.js" /></div>

## 2. 兼容性

### jQuery 兼容

　　兼容 jQuery 1.7+。

### 浏览器兼容

<div class="table-responsive"><table class="table table-bordered table-hover"><thead><tr><th><img title="IE" src="/styles/images/browser/ie.png" alt="IE" /></th><th><img title="Chrome" src="/styles/images/browser/chrome.png" alt="Chrome" /></th><th><img title="Firefox" src="/styles/images/browser/firefox.png" alt="Firefox" /></th><th><img title="Opera" src="/styles/images/browser/opera.png" alt="Opera" /></th><th><img title="Safari" src="/styles/images/browser/safari.png" alt="Safari" /></th></tr></thead><tbody style="text-align:center"><tr><td>IE8+ ✔</td><td>Chrome ✔</td><td>Firefox ✔</td><td>Opera ✔</td><td>Safari ✔</td></tr></tbody></table></div>

## 3. 使用方法

### 1、引入文件

```html
<link rel="stylesheet" href="css/jquery.fullPage.css">
<script src="js/jquery.min.js"></script>

<!-- jquery.easings.min.js 用于 easing 参数，也可以使用完整的 jQuery UI 代替，如果不需要设置 easing 参数，可去掉改文件 -->
<script src="js/jquery.easings.min.js"></script>

<!-- 如果 scrollOverflow 设置为 true，则需要引入 jquery.slimscroll.min.js，一般情况下不需要 -->
<script src="js/jquery.slimscroll.min.js"></script>

<script src="js/jquery.fullPage.js"></script>
```

### 2、HTML

```html
<div id="dowebok">
    <div class="section">
        <h3>第一屏</h3>
    </div>
    <div class="section">
        <h3>第二屏</h3>
    </div>
    <div class="section">
        <h3>第三屏</h3>
    </div>
    <div class="section">
        <h3>第四屏</h3>
    </div>
</div>
```

　　每个 section 代表一屏，默认显示“第一屏”，如果要指定加载页面时显示的“屏幕”，可以在对应的 section 加上 class=”active”，如：

```html
<div class="section active">第三屏</div>
```

　　同时，可以在 section 内加入 slide，如：

```html
<div id="dowebok">
    <div class="section">第一屏</div>
    <div class="section">第二屏</div>
    <div class="section">
        <div class="slide">第三屏的第一屏</div>
        <div class="slide">第三屏的第二屏</div>
        <div class="slide">第三屏的第三屏</div>
        <div class="slide">第三屏的第四屏</div>
    </div>
    <div class="section">第四屏</div>
</div>
```

### 3、JavaScript

```js
$(function(){
    $('#dowebok').fullpage();
});
```

## 4. 配置

### 1、选项

<div class="table-responsive"><table class="table table-bordered table-hover"><thead><tr><th>选项</th><th style="width:80px">类型</th><th>默认值</th><th>说明</th></tr></thead><tbody><tr><td>verticalCentered</td><td>字符串</td><td>true</td><td>内容是否垂直居中</td></tr><tr><td>resize</td><td>布尔值</td><td>false</td><td>字体是否随着窗口缩放而缩放</td></tr><tr><td>slidesColor</td><td>函数</td><td>无</td><td>设置背景颜色</td></tr><tr><td>anchors</td><td>数组</td><td>无</td><td>定义锚链接</td></tr><tr><td>scrollingSpeed</td><td>整数</td><td>700</td><td>滚动速度，单位为毫秒</td></tr><tr><td>easing</td><td>字符串</td><td>easeInQuart</td><td>滚动动画方式</td></tr><tr><td>menu</td><td>布尔值</td><td>false</td><td>绑定菜单，设定的相关属性与 anchors 的值对应后，菜单可以控制滚动</td></tr><tr><td>navigation</td><td>布尔值</td><td>false</td><td>是否显示项目导航</td></tr><tr><td>navigationPosition</td><td>字符串</td><td>right</td><td>项目导航的位置，可选 left 或 right</td></tr><tr><td>navigationColor</td><td>字符串</td><td>#000</td><td>项目导航的颜色</td></tr><tr><td>navigationTooltips</td><td>数组</td><td>空</td><td>项目导航的 tip</td></tr><tr><td>slidesNavigation</td><td>布尔值</td><td>false</td><td>是否显示左右滑块的项目导航</td></tr><tr><td>slidesNavPosition</td><td>字符串</td><td>bottom</td><td>左右滑块的项目导航的位置，可选 top 或 bottom</td></tr><tr><td>controlArrowColor</td><td>字符串</td><td>#fff</td><td>左右滑块的箭头的背景颜色</td></tr><tr><td>loopBottom</td><td>布尔值</td><td>false</td><td>滚动到最底部后是否滚回顶部</td></tr><tr><td>loopTop</td><td>布尔值</td><td>false</td><td>滚动到最顶部后是否滚底部</td></tr><tr><td>loopHorizontal</td><td>布尔值</td><td>true</td><td>左右滑块是否循环滑动</td></tr><tr><td>autoScrolling</td><td>布尔值</td><td>true</td><td>是否使用插件的滚动方式，如果选择 false，则会出现浏览器自带的滚动条</td></tr><tr><td>scrollOverflow</td><td>布尔值</td><td>false</td><td>内容超过满屏后是否显示滚动条</td></tr><tr><td>css3</td><td>布尔值</td><td>false</td><td>是否使用 CSS3 transforms 滚动</td></tr><tr><td>paddingTop</td><td>字符串</td><td>0</td><td>与顶部的距离</td></tr><tr><td>paddingBottom</td><td>字符串</td><td>0</td><td>与底部距离</td></tr><tr><td>fixedElements</td><td>字符串</td><td>无</td><td></td></tr><tr><td>normalScrollElements</td><td></td><td>无</td><td></td></tr><tr><td>keyboardScrolling</td><td>布尔值</td><td>true</td><td>是否使用键盘方向键导航</td></tr><tr><td>touchSensitivity</td><td>整数</td><td>5</td><td></td></tr><tr><td>continuousVertical</td><td>布尔值</td><td>false</td><td>是否循环滚动，与 loopTop 及 loopBottom 不兼容</td></tr><tr><td>animateAnchor</td><td>布尔值</td><td>true</td><td></td></tr><tr><td>normalScrollElementTouchThreshold</td><td>整数</td><td>5</td><td></td></tr></tbody></table></div>

### 2、方法

<div class="table-responsive"><table class="table table-bordered table-hover"><thead><tr><th>名称</th><th>说明</th></tr></thead><tbody><tr><td>moveSectionUp()</td><td>向上滚动</td></tr><tr><td>moveSectionDown()</td><td>向下滚动</td></tr><tr><td>moveTo(section, slide)</td><td>滚动到</td></tr><tr><td>moveSlideRight()</td><td>slide 向右滚动</td></tr><tr><td>moveSlideLeft()</td><td>slide 向左滚动</td></tr><tr><td>setAutoScrolling()</td><td>设置页面滚动方式，设置为 true 时自动滚动</td></tr><tr><td>setAllowScrolling()</td><td>添加或删除鼠标滚轮/触控板控制</td></tr><tr><td>setKeyboardScrolling()</td><td>添加或删除键盘方向键控制</td></tr><tr><td>setScrollingSpeed()</td><td>定义以毫秒为单位的滚动速度</td></tr></tbody></table></div>


### 3、回调函数

<div class="table-responsive"><table class="table table-bordered table-hover"><thead><tr><th>名称</th><th>说明</th></tr></thead><tbody><tr><td>afterLoad</td><td>滚动到某一屏后的回调函数，接收 anchorLink 和 index 两个参数，anchorLink 是锚链接的名称，index 是序号，从1开始计算</td></tr><tr><td>onLeave</td><td><p>滚动前的回调函数，接收 index、nextIndex 和 direction 3个参数：index 是离开的“页面”的序号，从1开始计算；</p><p>nextIndex 是滚动到的“页面”的序号，从1开始计算；</p><p>direction 判断往上滚动还是往下滚动，值是 up 或 down。</p></td></tr><tr><td>afterRender</td><td>页面结构生成后的回调函数，或者说页面初始化完成后的回调函数</td></tr><tr><td>afterSlideLoad</td><td>滚动到某一水平滑块后的回调函数，与 afterLoad 类似，接收 anchorLink、index、slideIndex、direction 4个参数</td></tr><tr><td>onSlideLeave</td><td>某一水平滑块滚动前的回调函数，与 onLeave 类似，接收 anchorLink、index、slideIndex、direction 4个参数</td></tr></tbody></table></div>

<hr>