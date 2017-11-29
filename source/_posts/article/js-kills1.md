---
layout: post
title: "你可能学了假的 JavaScript！"
date: 2017-02-08 14:41:15 +0800
categories: 学习笔记
tags: JavaScript
---


　　Adobe 一个高级工程师 Dmitry Baranovskiy 在[ 他的博客 ](http://dmitry.baranovskiy.com/)上写了这么一篇博文[ So,you think you know JavaScript? ](http://dmitry.baranovskiy.com/post/91403200)，并在博文中贴出了五个小示例（先不要看我贴的测试结果），大家可以感受一下，看看你是不是学了假的 JavaScript！<!-- more -->

## 1. 示例展示

```js
if (!("a" in window)) {
    var a = 1;
}
alert(a);
```

```js
var a = 1,
    b = function a(x) {
        x && a(--x);
    };
alert(a);
```

```js
function a(x) {
    return x * 2;
}
var a;
alert(a);
```

```js
function b(x, y, a) {
    arguments[2] = 10;
    alert(a);
}
b(1, 2, 3);
```

```js
function a() {
    alert(this);
}
a.call(null);
```

　　感受完了请先将你的答案写出来，然后再去实际测试一下 … … 如果错了一两个说明你 JavaScript 基础不牢，如果错了三四个了，那就只能说明你学了假的 JavaScript！

## 2. 示例测试

1. undefined 

2. 1 

3. function a (x) { 
   return x * 2; 
   }
   
4. 1、2、3、undefined、10 

5. object Windows

## 3. 示例分析

### 3.1 示例 1

> 示例1原型：

```
分析：首先会解析所有函数，其次是var声明的变量，但是不会赋值。因为javascript没有块的概念。像for(var i in array)这里的i依然是全局变量。 
执行顺序： 
1.var a;//声明一个变量，但是不会赋值 
2.if语句，全局变量相当于window的属性，所以”a” in window为真，取反为假。故不会执行大括号里面的语句。 
3.alert(a);//undefined
```

> 示例1变形：

```js
if (("a" in window)) {
        var a = 1;
    }
    alert (a);
```

　　Javascript变量的scope是根据方法块来划分的（也就是说以function的一对大括号｛ ｝来划分）。切记，是function块，而for、while、if块并不是作用域的划分标准!所以这里的a依然是全局变量。

### 3.2 示例 2

> 示例2原型：

```
分析： JavaScript中定义变量时，也可以同时定义多个，例如var a,b,c;在定义变量时使用函数表达式就类似于局部变量，不会被全局作用域中访问到。 
执行顺序： 
1.声明两个变量var a,b;并给他们赋值a=1,b=function a(){…};这里的function a是局部变量，外部无法访问到。因此全局a还是1. 
2.alert(a);//1
```

> 示例2变形：

　　这样写的话大家比较一下可能更能理解一点。

```js
    var a = 1,
     b = function a (x) {
        x && a (--x);
        alert(a);//打印看一下a是什么值，和外面打印的a进行比较
        return x;//返回一个值，这个值其实就是给b赋的值
    };
    alert (a);//这里只能取到全局变量a
    alert (a(1)); //报错: a is not a function
    alert(b);
    alert(b(1));
```

### 3.3 示例 3

> 示例3原型：

```
分析： js永远是先解析声明式函数，再解析变量。 
执行顺序： 
1.解析函数a; 
2.声明变量var a;因为a此时没有被赋值，所以它还是指向原来的值。即function a; 
3.alert(a);
```

> 示例3变形：

　　如果要证明js解析的顺序，很简单，把代码位置换一下：

```js
    var a;
    function a (x) {
        return x * 2;
    }
    alert (a);
```

### 3.4 示例 4

> 示例4原型：

```
分析：在函数内部可以引用一个对象，它是arguments类似数组，但不是数组。它代表了函数实际接收参数的集合。可以通过下标对相应参数进行访问。如果修改此对象某些属性，如arguments[index]，则被传进来的第index(如果有的话，下标从0开始)变量的值也会被修改。 
执行顺序： 
1.声明一个函数b; 
2.执行函数b(1,2,3);因为这里arguments[2]与变量a引用的是一个值，所以当arguments[2]改变时，a也随之改变。
```

### 3.5 示例 5

> 示例5原型：

```
call方法: 
语法：call([thisObj[,arg1[, arg2[, [,.argN]]]]]) 
定义：调用一个对象的一个方法，以另一个对象替换当前对象。 
说明： call 方法可以用来代替另一个对象调用一个方法。call 方法可将一个函数的对象上下文从初始的上下文改变为由 thisObj 指定的新对象。 
如果没有提供 thisObj 参数，那么 Global 对象被用作 thisObj。

分析： 
call方法接受多个参数，其作用是借用别人的方法当作自己的方法。这样能保证执行的时候this能够指向自己。call方法的第二个参数到最后一个参数是传给借用过来函数的。第一个参数是借用的对象，如果这个对象为空，那么将会作为全局window对象调用。即函数中的this指向window。
```

<hr>