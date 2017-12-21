---
layout: post
title: "animationend 事件"
date: 2017-12-21 09:12:15 +0800
categories: 学习笔记
tags: [CSS,JacaScript]
---


### CSS动画事件

> CSS 动画播放时，会发生以下三个事件：

```
animationstart - CSS 动画开始后触发
animationiteration - CSS 动画重复播放时触发
animationend - CSS 动画完成后触发
```

> 注意标准语法都是小写，兼容大小写驼峰写法

```
webkitAnimationStart
webkitAnimationIteration
webkitAnimationEnd
```

### 语法

```js
object.addEventListener("webkitAnimationEnd", myScript);  // Chrome, Safari 和 Opera 
object.addEventListener("animationend", myScript);        // 标准语法  
```

> 参考：[http://www.runoob.com/jsref/event-animationend.html](http://www.runoob.com/jsref/event-animationend.html)