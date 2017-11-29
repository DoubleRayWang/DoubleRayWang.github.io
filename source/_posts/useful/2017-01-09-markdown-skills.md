---
layout: post
title: "Markdown 实用技巧"
date: 2017-01-09 19:19:15 +0800
categories: 干货分享
tag: Markdown
---

* content
{:toc}


　　最开始接触了解 Markdown 是在 github 中写 README.md ，Markdown 的目标是实现「易读易写」，其语法格式最大灵感来源其实是纯文本电子邮件的格式，因此 Markdown 的语法全由一些符号所组成，这些符号经过精挑细选，其作用一目了然。

　　[Markdown 语法说明 (简体中文版)](http://www.appinn.com/markdown/index.html#em) 有对 Markdown 的比较完整的介绍，本文旨在总结一些实用的小技巧。<!-- more -->

-------------

## 1. 首行缩进

　　① 在段首加入 ```&ensp;``` 来输入一个空格；加入 ```&emsp;``` 来输入两个空格 （注：首行缩进为四个空格）；

　　② 将输入法切换到 ```全角``` 状态（Shift + Space），按两下空格即可，输完后记得切换回正常输入的 ```半角``` 状态（尽量不用）。

## 2. 插入代码

　　① 在每行代码前加入4个空格或者添加一个制表符（Tab键），其缩进是相对于当前格式状态下的，在列表项状态下，需要输入两次TAB键(制表符)才能以代码格式插入。

　　② 在代码两侧添加三个反引号 ‘ ``` ’ ，最好在代码的前后行添加，而不是直接加在代码两边。

## 3. 限制图片大小并居中

　　MarkDown 中是直接按原图大小显示图片的，让图片大小一致且居中显示可以通过内嵌 html 代码来实现。

```html
<div align="center">
    <img src="图片地址" width="宽度" height="高度" alt="图片名称"/>
</div>
```

**注意**：

+ HTML 的块元素――比如 ```<div>、<table>、<pre>、<p>``` 等标签，必须在前后加上空行与其它内容区隔开，并且开始标签与结尾标签不能用制表符或空格来缩进；
+ HTML 的行内元素——如 ```<span>、<cite>、<del>``` 可以在 Markdown 的段落、列表或是标题里随意使用，[特殊符号](http://blog.csdn.net/html5_/article/details/21639475) 的输入同 html。

## 4. 流程图画法

　　在编辑器中插入如下代码，在这之前需引入 [flowchart.js](https://cdnjs.com/libraries/flowchart) ,即可生成流程图，实例可参考 [Z.K.的博客](http://blog.csdn.net/ww1473345713/article/details/47620577)。

```smalltalk
st=>start: Start|past:>http://www.baidu.com
e=>end:  Ende|future:>http://www.baidu.com
op1=>operation:  My Operation
op2=>operation:  Stuff|current
sub1=>subroutine:  My Subroutine|invalid
cond=>condition:  Yes or No|approved:>http://www.google.com
c2=>condition:  Good idea|rejected
io=>inputoutput:  catch something...|future
st->op1(right)->cond
cond(yes, right)->c2
cond(no)->sub1(left)->op1
c2(yes)->io->e
c2(no)->op2->e  
```

<img src="{{ '/styles/images/jekyll/flow.png' | prepend: site.baseurl }}" alt="Markdown 流程图" />

<br>
