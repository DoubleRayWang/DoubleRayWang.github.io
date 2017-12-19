---
layout: post
title: "使用 Flex 实现 5 种常用布局"
date: 2017-12-14 09:12:15 +0800
categories: 学习笔记
tags: [CSS,Flex]
---


{% centerquote %}flex 实现 5 种常用布局，学习一下! {% endcenterquote %}

> 参考：https://github.com/meikidd/flex-layout

<!-- more -->


## Sticky Footer

经典的上-中-下布局。

当页面内容高度小于可视区域高度时，footer 吸附在底部；当页面内容高度大于可视区域高度时，footer 被撑开排在 content 下方

![demo 1 - Sticky Footer](/styles/images/css/flex-layout/demo1.png)

```html
<body>
  <header>HEADER</header>
  <article>CONTENT</article>
  <footer>FOOTER</footer>
</body>
```

```css
body {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
article {
  flex: auto;
}
```

## Fixed-Width Sidebar

在上-中-下布局的基础上，加了左侧定宽 sidebar。

![demo 2 - Fixed-Width Sidebar](/styles/images/css/flex-layout/demo2.png)

```html
<body>
  <header>HEADER</header>
  <div class="content">
    <aside>ASIDE</aside>
    <article>CONTENT</article>
  </div>
  <footer>FOOTER</footer>
</body>
```

```css
body {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
.content {
  flex: auto;
  display: flex;
}
.content article {
  flex: auto;
}
```

## Sidebar

左边是定宽 sidebar，右边是上-中-下布局。

![demo 3](/styles/images/css/flex-layout/demo3.png)

```html
<body>
  <aside>ASIDE</aside>
  <div class="content">
    <header>HEADER</header>
    <article>CONTENT</article>
    <footer>FOOTER</footer>
  </div>
</body>
```

```css
body {
  min-height: 100vh;
  display: flex;
}
aside {
  flex: none;
}
.content {
  flex: auto;
  display: flex;
  flex-direction: column;
}
.content article {
  flex: auto;
}
```

## Sticky Header

还是上-中-下布局，区别是 header 固定在顶部，不会随着页面滚动。

![demo 4 - Sticky Header](/styles/images/css/flex-layout/demo4.png)

```html
<body>
  <header>HEADER</header>
  <article>CONTENT</article>
  <footer>FOOTER</footer>
</body>
```

```css
body {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  padding-top: 60px;
}
header {
  height: 60px;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  padding: 0;
}
article {
  flex: auto;
  height: 1000px;
}
```

## Sticky Sidebar

左侧 sidebar 固定在左侧且与视窗同高，当内容超出视窗高度时，在 sidebar 内部出现滚动条。左右两侧滚动条互相独立。

![demo 5 - Sticky Sidebar](/styles/images/css/flex-layout/demo5.png)


```html
<body>
  <aside>
    ASIDE
    <p>item</p>
    <p>item</p>
    <!-- many items -->
    <p>item</p>
  </aside>
  <div class="content">
    <header>HEADER</header>
    <article>CONTENT</article>
    <footer>FOOTER</footer>
  </div>
</body>
```

```css
body {
  height: 100vh;
  display: flex;
}
aside {
  flex: none;
  width: 200px;
  overflow-y: auto;
  display: block;
}
.content {
  flex: auto;
  display: flex;
  flex-direction: column;
  overflow-y: auto;
}
.content article {
  flex: auto;
}
```