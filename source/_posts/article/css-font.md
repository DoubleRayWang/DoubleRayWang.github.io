---
layout: post
title: "CSS：font 总结"
date: 2017-02-04 14:41:15 +0800
categories: 学习笔记
tags: CSS
---


　　本文记录总结一些平时经常用的字体知识，方便查阅。（`font-family`、`font-size`、`font-weight`等。）<!-- more -->

## font-family

　　中文字体多数使用宋体、雅黑，英文用Helvetica，如下：

```css
body { font-family: Microsoft Yahei,SimSun,Helvetica; } 
```

　　那么常用的中文字体的英文名称：

- 宋体      SimSun
- 黑体      SimHei
- 微信雅黑   Microsoft Yahei
- 微软正黑体 Microsoft JhengHei
- 新宋体    NSimSun
- 新细明体  MingLiU 
- 细明体    MingLiU
- 标楷体    DFKai-SB
- 仿宋     FangSong
- 楷体     KaiTi
- 仿宋_GB2312  FangSong_GB2312
- 楷体_GB2312  KaiTi_GB2312  

## font-size

　　移动端字体单位 font-size 选择 px 还是 rem：

```css
/*如需适配多种移动设备，建议使用rem。以下为参考值：*/
html { font-size: 62.5%; }   //10*16 = 62.5%
/*设置12px字体   这里注意在rem前要加上对应的px值，解决不支持rem的浏览器的兼容问题，做到优雅降级*/
body { font-size:12px; font-size:1.2rem; }

```

　　针对谷歌默认最小字体12px的正确解决方案：

```css
.small-font{
    font-size:12px;
    -webkit-transform-origin-x: 0;
    -webkit-transform: scale(0.90);
}
/*兼容IE7，IE8*/
.smallsize-font {
    font-size:10.8px;
}
```

<hr>