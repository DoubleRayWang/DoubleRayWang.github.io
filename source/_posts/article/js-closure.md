---
layout: post
title: "JavaScript 闭包（Closure）"
date: 2017-01-16 12:19:15 +0800
categories: 面试总结
tags: JavaScript
---

　　`JavaScript` 里的闭包（Closure），对于跟 `js` 接触不够深的前端新手来说基本是闻之变色，那么闭包真的有那么难以理解和使用吗？前辈们有很多有声有色的解释和阐述，比如[阮一峰老师](http://www.ruanyifeng.com/blog/)的 [学习Javascript闭包（Closure）](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)、[明城](https://www.gracecode.com/)的 [闭包的秘密](https://www.gracecode.com/posts/2385.html) 、[vuturn的专栏](http://blog.csdn.net/vuturn/) 的 [JavaScript闭包详解](http://blog.csdn.net/vuturn/article/details/44560717) 等等好多，今天正好看到《JavaScript 高级程序设计》里的闭包一块，我就集百家之长再说一说闭包。<!-- more -->

## 1. 什么是闭包

　　**闭包** 是指有权访问另一个函数作用域中的变量的函数。

　　或者这么理解，**函数** 和 **函数内部能访问到的变量**（也叫环境）的总和，就是一个闭包。

　　闭包的常见形式一般就是一个函数内部创建另一个函数，如下：

```javascript
//原生js经典案例
function fx(){
  var local = 88;
  function bar(){
    local++
    return local;
  }
  return bar;
} //local 变量和 bar 函数就组成了一个闭包（Closure）
var func = fx();
func()

//jQuery经典案例
$(function() {
   var selections = []; 
   $(".niners").click(function() { // 此闭包可以访问选择变量​
   selections.push (this.prop("name")); // 更新外部函数作用域中的选择变量
   })
});
```

### 1.1 为什么要函数套函数呢？ 

　　其实函数套函数只是为了造出一个**局部变量**，闭包的原文是 `Closure`，其实跟 **包** 没有任何关系，并不是说非得用什么包起什么来才行。

### 1.2 闭包与作用域链

　　闭包中内部函数访问了外部函数的变量 *local*。即使这个内部函数被返回了，而且是在其他地方被调用了，但是他仍然可以访问外部函数的变量 *local*，之所以能够访问变量 *local*，是因为内部函数的作用域链中包含了 `fx()` 的作用域。

　　*小提示*：理解 [js 的作用域链](/2017/01/13/js-variate/) 及 [js 的变量范围](/2017/01/13/js-variate/) 对真正的彻底的理解闭包至关重要。

## 2. 闭包的作用

　　闭包可以用在许多地方。它的最大用处有两个，一个是可以**读取函数内部的变量**，另一个就是让这些**变量的值始终保持在内存中**([参考这里](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html))。

```javascript
　　function f1(){
　　　　var n=999;
　　　　nAdd=function(){n+=1}
　　　　function f2(){
　　　　　　alert(n);
　　　　}
　　　　return f2;
　　}
　　var result=f1();
　　result(); // 999
　　nAdd();
　　result(); // 1000
```

　　`函数 result` 执行了两次，第一次的值是 999，第二次的值是 1000。这证明了，函数 f1 中的局部变量 n 一直保存在内存中，并没有在 f1 调用后被自动清除。

　　这段代码中另一个值得注意的地方，就是 "`nAdd=function(){n+=1}`" 这一行，首先在 `nAdd` 前面没有使用 `var` 关键字，因此 `nAdd` 是一个全局变量，而不是局部变量。其次，`nAdd` 的值是一个匿名函数（anonymous function），而这个匿名函数本身也是一个闭包，可以在函数外部对函数内部的局部变量进行操作。

　　通过上面的阐述，其实可以延伸出闭包在实际中的应用：

### 2.1 匿名自执行函数

　　我们知道所有的变量，如果不加上var关键字，则默认的会添加到全局对象的属性上去，这样的临时变量加入全局对象有很多坏处，比如：别的函数可能误用这些变量；造成全局对象过于庞大，影响访问速度(因为变量的取值是需要从原型链上遍历的)。

　　除了每次使用变量都是用var关键字外，我们在实际情况下经常遇到这样一种情况，即有的函数只需要执行一次，其内部变量无需维护，比如UI的初始化，那么我们可以使用闭包：

```javascript
var datamodel = {    
    table : [],    
    tree : {}    
};      
(function(dm){    
    for(var i = 0; i < dm.table.rows; i++){    
       var row = dm.table.rows[i];    
       for(var j = 0; j < row.cells; i++){    
           drawCell(i, j);    
       }    
    }    
})(datamodel);   
```

　　我们创建了一个匿名的函数，并立即执行它，由于外部无法引用它内部的变量，因此在执行完后很快就会被释放，关键是这种机制不会污染全局对象。但是这种立即执行函数 （IIFE） 也要合理的使用，具体可以参看如何 [合理使用IIFE](/2017/01/14/js-IIFE/)。

### 2.2 闭包缓存

　　设想我们有一个处理过程很耗时的函数对象，每次调用都会花费很长时间，那么我们就需要将计算出来的值存储起来，当调用这个函数的时候，首先在缓存中查找，如果找不到，则进行计算，然后更新缓存并返回值，如果找到了，直接返回查找到的值即可。闭包正是可以做到这一点，因为它不会释放外部的引用，从而函数内部的值可以得以保留。

```javascript
var CachedSearchBox = (function(){    
    var cache = {},    
       count = [];    
    return {    
       attachSearchBox : function(dsid){    
           if(dsid in cache){//如果结果在缓存中    
              return cache[dsid];//直接返回缓存中的对象    
           }    
           var fsb = new uikit.webctrl.SearchBox(dsid);//新建    
           cache[dsid] = fsb;//更新缓存    
           if(count.length > 100){//保正缓存的大小<=100    
              delete cache[count.shift()];    
           }    
           return fsb;          
       },    
       clearSearchBox : function(dsid){    
           if(dsid in cache){    
              cache[dsid].clearSelection();      
           }    
       }    
    };    
})();    
CachedSearchBox.attachSearchBox("input1");    
```

　　这样，当我们第二次调用 `CachedSearchBox.attachSerachBox(“input1”)` 的时候，我们就可以从缓存中取道该对象，而不用再去创建一个新的`searchbox` 对象。

### 2.3 实现封装

　　先看一个关于封装的经典例子：

```javascript
function celebrityID () {
    var celebrityID = 999;
    //用内部函数返回一个对象 
    // 所有的内部函数可以访问外部函数的变量 
    return { getID: function () { 
                //这个内部函数将返回更新celebrityID变量 
                return celebrityID; 
                // 它将返回celebrityID的当前值，即使changeTheID函数更改 
            },
            setID: function (theNewID) { 
                //这个内部函数会随时改变外部函数的变量 
                celebrityID = theNewID;
            }
    };
};
var mjID = celebrityID (); 
// 此时，该celebrityID外部函数返回。
mjID.getID(); // 999​ 
mjID.setID(567); // 改变外部函数变量 
mjID.getID(); // 567
```

　　这个例子又说明了 **闭包引用外部函数的变量存储 ，不存储实际值**，而是储存的整个变量对象；这个例子同时也实现了面向对象中的对象。

## 3. 闭包的副作用

### 3.1 内存消耗

　　通常来说，函数的活动对象会随着执行期上下文一起销毁，但是，由于闭包引用另外一个函数的活动对象，因此这个活动对象无法被销毁，也就是要维护额外的作用域，这意味着，闭包比一般的函数需要更多的内存消耗。尤其在 IE 浏览器中需要关注。由于 IE 使用非原生 `javascript` 对象实现 DOM 对象，因此闭包会导致内存泄露问题，例如：

```javascript
function A(){  
      var a=document.createElement("div"),//  
            msg="Hello";  
       a.onclick=function(){  
          alert(msg);  
          }  
   }  
 A();
```

　　以上的闭包会在IE下导致内存泄露，假设`A()`执行时创建的作用域对象 ScopeA，ScopeA 引用了 DOM 对象 a,DOM 对象 a 引用了 `function(aleert(msg))`，函数 `function(alert(msg))` 引用了 ScopeA，这是一个循环引用，在 IE 会导致内存泄露。

### 3.2 性能问题

　　使用闭包时，会涉及到跨作用域访问，每次访问都会导致性能损失。因此在脚本中，最好小心使用闭包，它同时会涉及到内存和速度问题。

　　不过我们可以通过把跨作用域变量存储在局部变量中，然后直接访问局部变量，来减轻对执行速度的影响。

<hr>