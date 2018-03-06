---
layout: post
title: "移动端滚动穿透问题"
date: 2018-03-06 09:41:15 +0800
categories: 知识点
tags: [CSS,JavaScript]
---

滚动穿透是指在移动端当有 `fixed` 遮罩背景和弹出层时，在屏幕上滑动能够滑动背景下面的内容。网上整理了几种解决方案，但有些还是存在一定的问题。<!-- more -->

### 设置overflow为hidden

```less
.modal-open {
    &, body {
        overflow: hidden;
        height: 100%
    }
}
```

即当弹出层弹出时在html上添加.modal-open,禁用 html 和 body 的滚动条,但实际用上就会发现：

1. 由于 html 和 body的滚动条都被禁用，弹出层后页面的滚动位置会丢失，需要用 js 来计算原来滚动的位置，在弹出时保持滚动位置；

2. 但是页面的背景还是能够有滚的动的效果。


### js 之 touchmove + preventDefault

```js
modal.addEventListener('touchmove', function(e) {
  e.preventDefault();
}, false);
```

即通过阻止移动端touchmove事件，但实际用上会发现弹出层需要滚动时也会被阻止。

### 最后解决方案：position: fixed

```css
body.modal-open {
    position: fixed;
    width: 100%;
}
```

这种方式同样当弹出层弹出时滚动条会丢失，所以还需要使用js来保存滚动条的位置，在关闭弹出层时将滚动位置还原；

```javascript
var ModalHelper = (function(bodyCls) {
  var scrollTop; // 在闭包中定义一个用来保存滚动位置的变量
  return {
    afterOpen: function() { //弹出之后记录保存滚动位置，并且给body添加.modal-open
      scrollTop = document.scrollingElement.scrollTop;
      document.body.classList.add(bodyCls);
      document.body.style.top = -scrollTop + 'px';
    },
    beforeClose: function() { //关闭时将.modal-open移除并还原之前保存滚动位置
      document.body.classList.remove(bodyCls);
      document.scrollingElement.scrollTop = scrollTop;
    }
  };
})('modal-open');
```

这个方案比较完美的解决了移动端滚动穿透问题！
