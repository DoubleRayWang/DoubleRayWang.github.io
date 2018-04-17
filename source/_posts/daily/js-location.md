---
layout: post
title: "JavaScript 跳转页面"
date: 2018-04-16 12:41:15 +0800
categories: 知识点
tags: JavaScript
---


JavaScript 如何跳转页面？也许你不知道有这么多解法

```js
// 跳转
window.location.replace('https://www.awesomes.cn') // 不将被跳转页面加入浏览器记录
window.location.assign('https://www.awesomes.cn')
window.location.href = 'https://www.awesomes.cn'
window.location = 'https://www.awesomes.cn'

// 返回上一页
window.history.back()
window.history.go(-1)

// 刷新当前页
window.location.reload()
```
