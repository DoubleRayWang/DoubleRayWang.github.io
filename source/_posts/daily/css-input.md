---
layout: post
title: "chorme浏览器记住密码后input黄色背景处理方法总结（三种）"
date: 2018-02-28 09:41:15 +0800
categories: 知识点
tags: [CSS]
---

### 问题分析

正常情况：

![正常情况](/styles/images/css/input/e9a0e22d4a1ea5ffe4b84ba92f7f6d9a.png)

记住密码后访问：

![记住密码后访问](/styles/images/css/input/7090b6f0388da0cb693a7cf32a0e59dd.png)

### 解决方法

#### 方法1：阴影覆盖

```css
input:-webkit-autofill {
    -webkit-box-shadow: 0 0 0 1000px white inset !important;
}
```

> 注：由于是设置颜色覆盖，所以只对非透明的纯色背景有效；

#### 方法2：修改chrome浏览器渲染黄色背景的时间（推荐）

```css
:-webkit-autofill {
    -webkit-text-fill-color: #fff !important;
    transition: background-color 5000s ease-in-out 0s;
}
```

> 注：此方法适用于上图那种背景为透明色的输入框

#### 方法3：jq方式去除

```js
if (navigator.userAgent.toLowerCase().indexOf("chrome") >= 0){
  let _interval = window.setInterval(function () {
    let autofills = $('input:-webkit-autofill');
    if (autofills.length > 0) {
      window.clearInterval(_interval); // 停止轮询
      autofills.each(function() {
        let clone = $(this).clone(true, true);
        $(this).after(clone).remove();
      });
    }
  }, 20);
}
```