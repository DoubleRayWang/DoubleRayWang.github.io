---
layout: post
title: "CSS 五种方式实现 Footer 置底"
date: 2017-10-04 14:41:15 +0800
categories: 学习笔记
tags: CSS
---

页脚置底（Sticky footer）就是让网页的footer部分始终在浏览器窗口的底部。

当网页内容足够长以至超出浏览器可视高度时，页脚会随着内容被推到网页底部；但如果网页内容不够长，置底的页脚就会保持在浏览器窗口底部。

<img src="/styles/images/css/css-footer_1.png" />

> 原文链接[ http://t.cn/RJ3nmhV ](http://t.cn/RJ3nmhV)  

<!-- more -->


## 1. 将内容部分的底部外边距设为负数

这是个比较主流的用法，把内容部分最小高度设为100%，再利用内容部分的负底部外边距值来达到当高度不满时，页脚保持在窗口底部，当高度超出则随之推出的效果。

```html
<body>
  <div class="wrapper">
  
      content
      
    <div class="push"></div>
  </div>
  <footer class="footer"></footer>
</body>
```

```css
html, body {
  height: 100%;
  margin: 0;
}
.wrapper {
  min-height: 100%;

  /* 等于footer的高度 */
  margin-bottom: -50px;
}
.footer,
.push {
  height: 50px;
}
```

需要注意的是.wrapper的margin-bottom值需要和.footer的负的height值保持一致，这一点不太友好。

## 2. 将页脚的顶部外边距设为负数

 既然能在容器上使用负的margin bottom，那能否使用负margin top吗？当然可以。
 
 给内容外增加父元素，并让内容部分的底部内边距与页脚高度的值相等。
 
 ```html
 <body>
   <div class="content">
     <div class="content-inside">
       content
     </div>
   </div>
   <footer class="footer"></footer>
 </body>
 ```

```css
html, body {
  height: 100%;
  margin: 0;
}
.content {
  min-height: 100%;
}
.content-inside {
  padding: 20px;
  padding-bottom: 50px;
}
.footer {
  height: 50px;
  margin-top: -50px;
}
```

不过这种方法和上一种一样，都需要额外添加不必要的html元素。

## 3. 使用calc()设置内容高度

有一种方法不需要任何多余元素——使用CSS3新增的计算函数calc()

这样元素间就不会有重叠发生，也不需要控制内外边距了～

```html
<body>
  <div class="content">
    content
  </div>
  <footer class="footer"></footer>
</body>

```

```css
.content {
  min-height: calc(100vh - 70px);
}
.footer {
  height: 50px;
}
```

可能你会疑惑内容高度calc()中为什么减去70px，而不是footer的高度50px，因为假设俩元素有20px的间距，所以70px=50px+20px

不过，你不必在意这些~


## 4. 使用flexbox弹性盒布局

以上三种方法的footer高度都是固定的，通常来说这不利于网页布局：内容会改变，它们都是弹性的，一旦内容超出固定高度就会破坏布局。所以给footer使用flexbox吧，让它的高度可以变大变小变漂亮~

```html
<body>
  <div class="content">
    content
  </div>
  <footer class="footer"></footer>
</body>
```

```css
html {
  height: 100%;
}
body {
  min-height: 100%;
  display: flex;
  flex-direction: column;
}
.content {
  flex: 1;
}
```

你还可以在上面添加header或在下面添加更多元素。可从以下技巧选择其一：

+ `flex:1`使内容（如：.content）高度可以自动伸缩

+ `margin-top: auto`

可参考[Flex布局新旧混合写法详解](/2017/01/15/article-css-flex/)

## 5. 使用Grid网格布局

grid比flexbox还要新很多，并且更佳很简洁，可参考[CSS：Grid布局](http://ghmagical.com/article/page/id/eAhyFiDCFGb6)

```html
<body>
  <div class="content">
    content
  </div>
  <footer class="footer"></footer>
</body>
```

```css
html {
  height: 100%;
}
body {
  min-height: 100%;
  display: grid;
  grid-template-rows: 1fr auto;
}
.footer {
  grid-row-start: 2;
  grid-row-end: 3;
}
```

遗憾的是，网格布局（Grid layout）目前仅支持Chrome Canary和Firefox Developer Edition版本。