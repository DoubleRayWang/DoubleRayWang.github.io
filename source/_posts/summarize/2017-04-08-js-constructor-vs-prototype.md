---
layout: post
title: "JS 构造函数内的方法与构造函数prototype属性上方法的对比"
date: 2017-04-08 10:41:15 +0800
categories: 学习笔记
tags: JavaScript
---


　　最近用面向对象的思路写一个小小的H5小游戏，对之前零散的知识点进行了一下总结，本文的目的是让大家理解什么情况下把函数的方法写在JavaScript的构造函数上,什么时候把方法写在函数的`prototype`属性上;以及这样做的好处。

　　为了阅读方便,我们约定一下:把方法写在构造函数内的情况我们简称为**函数内方法**,把方法写在`prototype`属性上的情况我们简称为**prototype上的方法**。<!-- more -->

**首先我们先了解一下这篇文章的重点:**

+ **函数内的方法:** 使用函数内的方法我们可以**访问到函数内部的私有变量**,如果我们通过构造函数`new`出来的对象需要我们操作构造函数内部的私有变量的话,
  我们这个时候就要考虑使用函数内的方法.
+ **prototype上的方法:** 当我们需要**通过一个函数创建大量的对象**,并且这些对象还都有许多的方法的时候;这时我们就要考虑在函数的`prototype`上添加这些方法.
  这种情况下我们代码的**内存占用**就比较小.
+ **在实际的应用中,这两种方法往往是结合使用的;所以我们要首先了解我们需要的是什么,然后再去选择如何使用.**

我们还是根据下面的代码来说明一下这些要点吧,下面是代码部分:
```javascript
// 构造函数A
function A(name) {
    this.name = name || 'a';
    this.sayHello = function() {
        console.log('Hello, my name is: ' + this.name);
    }
}

// 构造函数B
function B(name) {
    this.name = name || 'b';
}
B.prototype.sayHello = function() {
    console.log('Hello, my name is: ' + this.name);
};

var a1 = new A('a1');
var a2 = new A('a2');
a1.sayHello();
a2.sayHello();

var b1 = new B('b1');
var b2 = new B('b2');
b1.sayHello();
b2.sayHello();
```

我们首先写了两个构造函数,第一个是`A`,这个构造函数里面包含了一个方法`sayHello`;第二个是构造函数`B`,
我们把那个方法`sayHello`写在了构造函数`B`的`prototype`属性上面.

需要指出的是,通过这两个构造函数`new`出来的对象具有一样的属性和方法,但是它们的区别我们可以通过下面的一个图来说明:

![1](/styles/images/js/constructor-vs-prototype.jpg)

我们通过使用构造函数`A`创建了两个对象,分别是`a1`,`a2`;通过构造函数`B`创建了两个对象`b1`,`b2`;我们可以发现`b1`,`b2`这两个对象的那个`sayHello`方法都是指向了它们的构造函数的`prototype`属性的`sayHello`方法.而`a1`,`a2`都是在自己内部定义了这个方法。

**定义在构造函数内部的方法,会在它的每一个实例上都克隆这个方法;定义在构造函数的`prototype`属性上的方法会让它的所有示例都共享这个方法,但是不会在每个实例的内部重新定义这个方法.**

**如果我们的应用需要创建很多新的对象,并且这些对象还有许多的方法,为了节省内存,我们建议把这些方法都定义在构造函数的`prototype`属性上**

当然,在某些情况下,我们需要将某些方法定义在构造函数中,这种情况一般是因为我们需要**访问构造函数内部的私有变量**.


下面我们举一个两者结合的例子,代码如下:
```javascript
function Person(name, family) {
    this.name = name;
    this.family = family;
    
    var records = [{type: "in", amount: 0}];

    this.addTransaction = function(trans) {
        if(trans.hasOwnProperty("type") && trans.hasOwnProperty("amount")) {
           records.push(trans);
        }
    }

    this.balance = function() {
       var total = 0;

       records.forEach(function(record) {
           if(record.type === "in") {
             total += record.amount;
           }
           else {
             total -= record.amount;
           }
       });
    
        return total;
    };
};

Person.prototype.getFull = function() {
    return this.name + " " + this.family;
};

Person.prototype.getProfile = function() {
     return this.getFull() + ", total balance: " + this.balance();
};
```

在上面的代码中,我们定义了一个`Person`构造函数;这个函数有一个内部的私有变量`records`,这个变量我们是不希望通过函数内部以外的方法去操作这个变量,所以我们把操作这个变量的方法都写在了函数的内部.而把一些可以公开的方法写在了`Person`的`prototype`属性上,比如方法`getFull`和`getProfile`.

**把方法写在构造函数的内部,增加了通过构造函数初始化一个对象的成本,把方法写在`prototype`属性上就有效的减少了这种成本.**

**你也许会觉得,调用对象上的方法要比调用它的原型链上的方法快得多,其实并不是这样的,如果你的那个对象上面不是有很多的原型的话,它们的速度其实是差不多的**



另外,需要注意的一些地方:
 + 首先如果是在函数的`prototype`属性上定义方法的话,要牢记一点,如果你改变某个方法,那么由这个构造函数产生的所有对象的那个方法都会被改变.
 + 还有一点就是变量提升的问题,我们可以稍微的看一下下面的代码:
```javascript
   func1(); // 这里会报错,因为在函数执行的时候,func1还没有被赋值. error: func1 is not a function
   var func1 = function() {
       console.log('func1');
   };
   
   func2(); // 这个会被正确执行,因为函数的声明会被提升.
   function func2() {
       console.log('func2');
   }
```
 + 关于对象序列化的问题.定义在函数的`prototype`上的属性不会被序列化,可以看下面的代码:
```javascript
   function A(name) {
       this.name = name;
   }
   A.prototype.sayWhat = 'say what...';
   
   var a = new A('dreamapple');
   console.log(JSON.stringify(a));
```
   我们可以看到输出结果是`{"name":"dreamapple"}`
   
<hr>