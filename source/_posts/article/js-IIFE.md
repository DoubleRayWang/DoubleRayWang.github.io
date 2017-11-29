---
layout: post
title: "IIFE 详解"
date: 2017-01-14 19:25:15 +0800
categories: 学习笔记
tags: JavaScript
---


　　在 JavaScript 中，每一个函数在被调用的时候都会创建一个执行上下文，在该函数内部定义的变量和函数只能在该函数内部被使用，而正是因为这个上下文，使得我们在调用函数的时候能创建一些私有变量。

　　**本文参考 [合理使用 IIFE 优化 JS 引擎的性能](https://zhuanlan.zhihu.com/p/23629542)、[详解javascript立即执行函数表达式（IIFE）](http://web.jobbole.com/82520/)。**<!-- more -->

## 1. 立即执行函数（IIFE）

　　说起**立即执行函数**（IIFE，Immediately-invoked function expression）大家应该都不陌生，在 `JavaScript` 中可以声明一个函数然后立即执行它：

```js
(function(){/* 函数体 */})()

!function(){/* 函数体 */}()
```

　　`IIFE` 通常用于实现私有变量、实现独立模块等等地方，比如喜闻乐见的 `jQuery` 最顶层的结构就是这样的：

```js
(function(global, factory) {
    //......
})(typeof window !== "undefined" ? window : this, function(window, noGlobal) {
    //......
})
```

### 1.1 写法

　　**立即执行函数** 的写法可以说是五花八门：

```js
// 最常用的两种写法
(function(){ /* code */ }()); // 老道推荐写法
(function(){ /* code */ })(); // 当然这种也可以
 
// 括号和JS的一些操作符（如 = && || ,等）可以在函数表达式和函数声明上消除歧义
// 如下代码中，解析器已经知道一个是表达式了，于是也会把另一个默认为表达式
// 但是两者交换则会报错
var i = function(){ return 10; }();
true && function(){ /* code */ }();
0, function(){ /* code */ }();
 
// 如果你不怕代码晦涩难读，也可以选择一元运算符
!function(){ /* code */ }();
~function(){ /* code */ }();
-function(){ /* code */ }();
+function(){ /* code */ }();
 
// 你也可以这样
new function(){ /* code */ }
new function(){ /* code */ }() // 带参数
```

　　**无论何时，给立即执行函数加上括号是个好习惯**。

### 1.2 与闭包的暧昧关系

　　立即执行函数能配合闭包保存状态。

　　像普通的函数传参一样，立即执行函数也能传参数。如果在函数内部再定义一个函数，而里面的那个函数能引用外部的变量和参数（闭包），利用这一点，我们能使用立即执行函数锁住变量保存状态。

```js
// 并不会像你想象那样的执行，因为i的值没有被锁住
// 当我们点击链接的时候，其实for循环已经执行完了
// 于是在点击的时候i的值其实已经是elems.length了
var elems = document.getElementsByTagName( 'a' );
 
for ( var i = 0; i < elems.length; i++ ) {
 
  elems[ i ].addEventListener( 'click', function(e){
    e.preventDefault();
    alert( 'I am link #' + i );
  }, 'false' );
 
}
 
// 这次我们得到了想要的结果
// 因为在立即执行函数内部，i的值传给了lockedIndex，并且被锁在内存中
// 尽管for循环结束后i的值已经改变，但是立即执行函数内部lockedIndex的值并不会改变
var elems = document.getElementsByTagName( 'a' );
 
for ( var i = 0; i < elems.length; i++ ) {
 
  (function( lockedInIndex ){
 
    elems[ i ].addEventListener( 'click', function(e){
      e.preventDefault();
      alert( 'I am link #' + lockedInIndex );
    }, 'false' );
 
  })( i );
 
}
 
// 你也可以这样，但是毫无疑问上面的代码更具有可读性
var elems = document.getElementsByTagName( 'a' );
 
for ( var i = 0; i < elems.length; i++ ) {
 
  elems[ i ].addEventListener( 'click', (function( lockedInIndex ){
    return function(e){
      e.preventDefault();
      alert( 'I am link #' + lockedInIndex );
    };
  })( i ), 'false' );
 
}
```

　　其实上面代码的`lockedIndex`也可以换成`i`，因为两个`i`是在不同的作用域里，所以不会互相干扰，但是写成不同的名字更好解释。以上便是**立即执行函数+闭包的作用**。

### 1.3 IIFE 与 Module 模式

　　立即执行函数在模块化中也大有用处。用立即执行函数处理模块化可以减少全局变量造成的空间污染，构造更多的私有变量。

```js
// 创建一个立即执行的匿名函数
// 该函数返回一个对象，包含你要暴露的属性
// 如下代码如果不使用立即执行函数，就会多一个属性i
// 如果有了属性i，我们就能调用counter.i改变i的值
// 对我们来说这种不确定的因素越少越好
 
var counter = (function(){
  var i = 0;
 
  return {
    get: function(){
      return i;
    },
    set: function( val ){
      i = val;
    },
    increment: function() {
      return ++i;
    }
  };
}());
 
// counter其实是一个对象
 
counter.get(); // 0
counter.set( 3 );
counter.increment(); // 4
counter.increment(); // 5
 
counter.i; // undefined i并不是counter的属性
i; // ReferenceError: i is not defined (函数内部的是局部变量)
```

### 1.4 自执行匿名函数和立即执行的函数表达式区别

　　在这篇帖子里，我们一直叫自执行函数，确切的说是自执行匿名函数（Self-executing anonymous function），但英文原文作者一直倡议使用立即调用的函数表达式（Immediately-Invoked Function Expression）这一名称，作者又举了一堆例子来解释，好吧，我们来看看：

```js
// 这是一个自执行的函数，函数内部执行自身，递归
function foo() { foo(); }

// 这是一个自执行的匿名函数，因为没有标示名称
// 必须使用arguments.callee属性来执行自己
var foo = function () { arguments.callee(); };

// 这可能也是一个自执行的匿名函数，仅仅是foo标示名称引用它自身
// 如果你将foo改变成其它的，你将得到一个used-to-self-execute匿名函数
var foo = function () { foo(); };

// 有些人叫这个是自执行的匿名函数（即便它不是），因为它没有调用自身，它只是立即执行而已。
(function () { /* code */ } ());

// 为函数表达式添加一个标示名称，可以方便Debug
// 但一定命名了，这个函数就不再是匿名的了
(function foo() { /* code */ } ());

// 立即调用的函数表达式（IIFE）也可以自执行，不过可能不常用罢了
(function () { arguments.callee(); } ());
(function foo() { foo(); } ());

// 另外，下面的代码在黑莓5里执行会出错，因为在一个命名的函数表达式里，他的名称是undefined
// 呵呵，奇怪
(function foo() { foo(); } ());
```

## 2. 针对 JS 引擎的一处性能优化

　　先从一个小问题说起吧，想实现一个立即执行的函数，上面提到有很多种写法，比如下面这两种：

```js
// 方法一，传入一个匿名函数
function run(f){
    return f();
}
run(function(){
    //......
});

// 方法二，使用IIFE
(function(){
    //......
})()
```

　　是的，这两种写法完全是等价的，无论怎么看都不会有什么区别，但是在一些 `JavaScript` 引擎中，它们其实性能相差甚远。

　　使用 `Node` 分别对两种情况运行了十万次，方法一的运行时间平均在 360 毫秒左右，方法二平均是 50 毫秒左右，性能相差 7 倍还多。

　　为什么使用 `IIFE` 之后性能会提升那么多呢？这就要从 `JS` 引擎（比如V8、SpiderMonkey）对于函数的优化上说起了。

　　现在的 `JS` 引擎都是十分聪明的，它们在真正执行代码之前会对代码中函数声明做一遍 pre-parse（预解析），为啥要做 pre-parse 呢？因为实际情况中大多数的函数都不是立即被使用的（甚至完全没被调用过），不需要对它们做一次完整的解析，只需要做一次性能开销更小的 pre-parse（比如检查一下语法错误），等函数真正被调用时，再进行完整的 full-parse。

```js
// 下面的函数会先进行pre-parse
function foo() {
    //......
}

// 2秒之后函数被执行，又会进行一遍full-parse
setTimeout(foo, 2000);
```

　　但是有个问题！对于立即执行函数这种奇葩来说，它不适用于上面的规则，应该直接进行 full-parse。现在的大多数引擎也完全考虑到了这一点：

```js
// 只会进行一次full-parse
(function() {
   //......
})();
```

　　但是还有个问题！！现在的大多数引擎检测 `IIFE` 的时候都不完全，大部分都是通过判别函数声明前有没有类似『 ( 』或者『 ! 』这样的字符来实现的，比如下面这种情况就被忽略掉了：

```js
// 这里要进行pre-parse和full-parse，而前者是多余的
function run(f){
    return f();
}
run(function(){
    //......
})
```

　　所以我们可以通过一个小 trick 来优化这里的性能（加了一对括号），这样引擎就会把这里识别为立即执行函数，然后只做一次 full-parse：

```js
// 只进行一次full-parse
function run(f){
    return f();
}
run((function(){
    //......
}));
```

　　所以针对这个问题，有一个专门的小工具来解决：

　　[nolanlawson/optimize-js: Optimize a JavaScript file for faster initial load by wrapping eagerly-invoked functions](https://github.com/nolanlawson/optimize-js)

　　还有一个相关的讨论：

　　[Turn off negate_iife by default as it hurts V8 performance. · Issue #886 · mishoo/UglifyJS2](https://github.com/mishoo/UglifyJS2/issues/886)

　　这个看似不起眼的 trick 实际对于性能有很显著的提升：

![trick](/styles/images/js/trick.png)

　　这个问题本质上来讲是 `JS` 引擎对于立即执行函数的识别有遗漏导致的，比如在 Safari 10 中这个问题基本不会发生，而 Chrome 的 V8 中就经常出现。不过感觉随着引擎版本的迭代，这个问题应该会得到修复。

<hr>