---
layout: post
title: "5种你未必知道的JavaScript和CSS交互的方法"
date: 2017-06-24 13:13:05 +0800
categories: 干货分享
tags: JavaScript
---


随着浏览器不断的升级改进，CSS和JavaScript之间的界限越来越模糊。本来它们是负责着完全不同的功能，但最终，它们都属于网页前端技术，它们需要相互密切的合作。我们的网页中都有.js文件和.css文件，但这并不意味着CSS和js是独立不能交互的。下面要讲的这五种JavaScript和CSS共同合作的方法你也许未必知道！<!-- more -->

### 用JavaScript获取伪元素(pseudo-element)属性

大家都知道如何通过一个元素的`style`属性获取它的CSS样式值，但能获取伪元素(pseudo-element)的属性值吗？可以的，使用`JavaScript`也可以访问页面中的伪元素。

```js
// Get the color value of .element:before
var color = window.getComputedStyle(
	document.querySelector('.element'), ':before'
).getPropertyValue('color');

// Get the content value of .element:before
var content = window.getComputedStyle(
	document.querySelector('.element'), ':before'
).getPropertyValue('content');
```

看见了吗，我能访问伪元素里的`content`属性值。如果你想创建一个动态的，风格别致的网站，这是一种非常有用的技术！

### classList API
    
很多的JavaScript工具库里都有`addClass`，`removeClass`和`toggleClass`等方法。为了对老式浏览器的兼容，这些类库采用的方法都是先搜索元素的`className`，追加和删除这个类，然后更新`className`。其实有一个新型的API提供了添加，删除和反转CSS类属性的方法，叫做`classList`：

```js
myDiv.classList.add('myCssClass'); // Adds a class

myDiv.classList.remove('myCssClass'); // Removes a class

myDiv.classList.toggle('myCssClass'); // Toggles a class
```

大多数的浏览器里很早就实现了`classList`API，而且最终 IE10 里也实现了它。

### 直接对样式表进行添加和删除样式规则

我们都非常熟悉使用`element.style.propertyName`来修改样式，使用JavaScript能帮助我们做到这些，但你知道如何新增或修一个现有的CSS样式规则吗？其实非常的简单。

```js
function addCSSRule(sheet, selector, rules, index) {
	if(sheet.insertRule) {
		sheet.insertRule(selector + "{" + rules + "}", index);
	}
	else {
		sheet.addRule(selector, rules, index);
	}
}

// Use it!
addCSSRule(document.styleSheets[0], "header", "float: left");
```

这种方法通常是用来创建一个新的样式规则，但如果你想修改一个现有的规则，也可以这样做。

### 加载CSS文件

延迟加载图片、JSON、脚本等是用来加快页面显示速度的好方法。我们可以使用curl.js等这样JavaScript加载器来延迟加载这些外部资源，可你知道CSS样式表也可以延迟加载吗，而且在加载成功后回调函数会给予通知。

```js
curl(
	[
		"namespace/MyWidget",
		"css!namespace/resources/MyWidget.css"
	], 
	function(MyWidget) {
		// 你可以对MyWidget进行操作
		// 这里没有对这个CSS文件引用，因为不需要;
		// 我们只需要它已经加载到页面上了
	});
```

[原文站点](http://www.webhek.com/) 使用的PrismJS语法高亮脚本就是延迟加载的。当所有的资源都加载后，回调函数就会触发，可在回调函数里加载它。据说非常有用（还未尝试）！

### CSS鼠标指针事件

CSS鼠标指针事件`pointer-events`属性非常的有趣，它的功效非常像JavaScript，当你把这个属性设置为`none`时，它能有效的阻止禁止这个元素，你也许会说“这又如何？”，但事实上，它是禁止了这个元素上的任何JavaScript事件或回调函数！

```css
.disabled { pointer-events: none; }
```

点击这个元素，你会发现任何你放置在这个元素上的监听器都不会触发任何事件。一个神奇的功能，真的——你不在需要为了防止某个事件会被触发而去检查某个css类是否存在。

> 原文[5 Ways that CSS and JavaScript Interact That You May Not Know About.](https://davidwalsh.name/ways-css-javascript-interact)

<hr>