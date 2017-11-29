---
layout: post
title: "掌握Chrome开发工具"
date: 2017-07-31 13:13:05 +0800
categories: 干货分享
tags: Debug
---


你可能已经熟悉了Chrome开发工具的基本功能。: DOM检查器、样式面板和JavaScript控制台。 但也有一些不太为人所知的特性可以显著提高你调试或开发应用的速度。<!-- more -->

<img src="/styles/images/chrome/tools.png" />


### 黑色主题

<img src="/styles/images/chrome/black.png" />

Chrome开发工具的内置了黑色主题。你可以通过点击开发工具窗口右上角的三点图标，之后点击进入设置页面，切换主题。

有时候我觉得黑色主题让我的眼睛更舒服,并且黑色主题显然看起来更酷一些！

### 选择模式

<img src="/styles/images/chrome/modul.png" />

Chrome开发者工具提供了很多选择元素的方法，其中最快捷的方法就是使用选择模式。

该功能通过点击调试面板左上角的按钮开启(或者通过组合键 ctrl + shift + c), 该模式下你只需单击页面上的元素就可以选中它。

一旦开启该模式，你可以将鼠标移动到页面来预览选定内容，然后单击来选择要检查的元素。

通过ctrl + shitf + c键，你可以直接打开调试工具并开启调试模式，来在页面上快速选择一个元素。

### 存储为全局变量

<img src="/styles/images/chrome/global.png" />

有时在控制台中查看一个复杂对象是一件很麻烦的事，因为他们可能有很多的键值或者一些很难手动解析的值。幸运的是，Chrome可以让检查这类JavaScript对象变得很容易。 你只需在控制台中右键点击对象后选择“存储为全局变量”，chrome就会将这个变量存储为一个名叫“temp1”的变量，之后你可以通过Javascript对其进行操作。

### 动画工具

<img src="/styles/images/chrome/animation.png" />

最近，Chrome团队为调试和创建动画添加了一些新特性。

单击控制台左上角的下拉框中的“动画”开启动画调试工具，你可以通过它限制站点上所有动画的速度。

你也可以暂停所有动画。这对于一个充斥着动画内容的站点尤其有用。

<img src="/styles/images/chrome/animation_1.png" />

动画查看器允许你单独控制每个属性的时间曲线！

<img src="/styles/images/chrome/saibeier.png" />

通过点击一个元素 `transition` 属性中的紫色曲线图表按钮，你可以看到动画的移动曲线，并且微调他的属性。此外，你还可以使用一些预置的时间曲线来应用到你的元素上。

### 模拟元素伪态

<img src="/styles/images/chrome/hover.png" />

通过点击样式窗口右上角的`:hover` 图标，你可以打开元素状态模拟工具。

该工具可以让你模拟一个元素的hover,active,focused和visited伪态，并且看到不同伪态的相关样式与选择符。

<img src="/styles/images/chrome/hover_1.png" />

如果要为这些伪态添加样式，可以添加一个新的选择器（使用“+”图标），并将:添加到选择器的结尾。

例如，如果我想要给一个logo类的li标签添加hover伪态样式，我需要构造一个新的伪类`li.logo:hover`，并且给他添加相关属性。

之后可以通过模拟`hover`状态来检查你的样式是否正确。

### 美化CSS和JavaScript

<img src="/styles/images/chrome/good.png" />

调试、浏览压缩后的JavaScript和CSS是一件非常困难的事情，好在调试工具让这件事情变得容易了一些。

在“Source”的标签栏打开了一个压缩的文档后，你可以点击左下角的花括号logo，之后调试工具就会将代码进行格式化处理。

尽管在压缩过程中丢失了一些信息（例如变量名）,该工具对调试CSS和JavaScript文件还是很有用的。

### Alt + Up / Alt + Down

<img src="/styles/images/chrome/alt.png" />

在调试CSS时，你可以选择一个属性然后使用上下箭头来调整它的值。默认情况下，上下箭头会将值加减1。但如果你按住了`alt`键，再通过上下箭头调整值时候，每次增减的值就会变成0.1，这在处理一些浮点类型的数值属性时非常有用。

相反的，你可以按住`shift`键一次将数值加减10;

### 保存日志

<img src="/styles/images/chrome/save.png" />

保存日志是一个复选框，它允许在页面刷新后仍然保存日志。这在调试需要刷新页面的网站问题时非常有用，因为默认情况下，所有控制台输出的信息在页面刷新后都会被清除。

启用此选项后，控制台中会出现一个新的“导航”日志，它指向了刷新或是导航到的页面。

### 网络 + 日志过滤

<img src="/styles/images/chrome/filter.png" />

当调试具有大量网络请求或控制台日志的应用程序时，过滤特定类型的事件是很有用的。

Chrome有一种支持多种属性的过滤语言，以及类似于*的通配符。

如果你输入了“-”，Chrome会出现一个包含了可选过滤选项的提示框。你也可以打开“正则模式”来对每一行的数据进行正则匹配。

### 代码覆盖率

<img src="/styles/images/chrome/cover.png" />

代码覆盖率可以在运行Web应用程序后针对每个JavaScript和CSS文件，查看哪些代码行运行了，哪些代码没有运行。这是很有用的，因为在处理复杂或长期项目时，很容易在项目中累积无用的代码。

如果想要使用这个功能的话，首先升级Chrome到59或更高版本，之后切换到“Coverage”页面。点击“record”，之后开始使用你的Web应用。当你使用结束后，Chrome将向你显示操作期间运行的具体代码。

### 调试用户遇到的问题

通常来说调试工具只有在你自己的机器上才会生效。但如果你有兴趣了解用户在使用过程中遇到的的bug和性能问题，不妨尝试一下 [LogRocket](https://logrocket.com/)。

<img src="/styles/images/chrome/LogRocket.png" />

`LogRocket` 是一个前端日志记录工具，它可以让你重放问题，就像它们发生在你自己的浏览器中一样。 不是通过猜测错误发生的原因，或要求用户提供截图或者错误日志，`logrocket`可以让你通过重放快速了解到问题出在哪里。它的兼容性很好，无论在任何框架下都可以使用，并且`LogRocket`对于React、Angular、和Vue的提供了额外的日志插件。

LogRocket 会记录你应用程序中的日志信息、带有header和body的网络请求、浏览器的元数据、Redux的行为与状态、以及应用进行时间与性能。并且他还能记录页面的HTML和CSS样式，完美的还原页面。

> **LogRocket | JavaScript应用的日志记录和会话回放 _LogRocket帮助你理解那些影响用户的问题, 这样你就可以重新构建更好的的软件**。


> 本文转载自：[众成翻译](http://www.zcfy.cc)

> 译者：[一只叫做兰胖的食人魔魔法师](http://www.zcfy.cc/@mirlysky)

> 链接：[http://www.zcfy.cc/article/3722](http://www.zcfy.cc/article/3722)

> 原文：[https://medium.freecodecamp.com/mastering-chrome-developer-tools-next-level-front-end-development-techniques-3ac0b6fe8a3](https://medium.freecodecamp.com/mastering-chrome-developer-tools-next-level-front-end-development-techniques-3ac0b6fe8a3)

<hr>





















