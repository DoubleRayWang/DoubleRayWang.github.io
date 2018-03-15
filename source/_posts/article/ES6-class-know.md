---
layout: post
title: "ES6新特性 —— Class"
date: 2018-03-14 19:41:15 +0800
categories: 前沿技术
tags: [ES6.JavaScript]
---

## 前言
类语法(`class`)是`ES6`中新增的一个亮点特性，下文简单对`class`的理解做一个简要的记录说明。
<!-- more -->

## js传统模式实例化对象方法——`prototype`
在JavaScript中，实例化一个对象的传统使用方法是通过构造函数。
如下示例：

```js
function Count(num1,num2){
    this.num1 = num1;
    this.num2 = num2;
}
//构造函数
Count.prototype.number = function(){
    console.log(this.num1,this.num2);
}
//实例化对象
var count = new Count(1,2);
count.number(1,3);

```
## ES6新特性——使用Class（类）实例化对象

ES6引入了`Class`(类)这一概念，可以通过class关键字，定义类。

如下示例：

```js
//定义类
class Count{

    //构造方法
    constructor(x,y){
        //this表示实例对象
        this.x = x;
        this.y = y;
    }

    //非静态方法，可以直接用实例.方法名来访问
    sum(x,y){
        let sum = x+y;
        console.log('sum is :',sum)
    }

    //静态方法，需要通过构造器或者类才能访问的到
    static sub(x,y){
        let sub = x - y;
        console.log('sub is :',sub);
    }
}

var count = new Count();

//非静态方法，可以直接访问
count.sum(1,2);                         // sum is :3

//静态方法：
//（1）实例对象.constructor.方法名
count.constructor.sub(5,1);             // sub is :4

//（2）类名.方法名
Count.sub(5,1);                         // sub is :4

count.hasOwnProperty('x');              // 定义在this上，是实例对象自身的属性true
count.hasOwnProperty('y');              // true
count.hasOwnProperty('sum');            // 定义在原型对象上，false
count._proto_.hasOwnProperty('sum');    // true
```

通过以上的小实例，总结下面几点:

+ 定义“类”的方法时，不用加function关键字。
+ 方法之间不要加逗号，加了会报错。
+ 类中必须要有constructor方法，若没有显示定义，则一个空的会被添加。
+ 类必须使用new调用。否则报错，这是类和构造函数的一个主要区别。
+ 类中使用static关键字声明的方法，需要通过构造函数constructor或者使用类名才可以访问的到。
+ 静态方法，实例对象不能直接访问。但是父类的静态方法，子类可以继承。
+ 非静态方法，可以直接通过“实例对象.方法名”访问。
+ 实例自身的属性，除非显示定义在this对象上，否则都是定义在原型上（即class上）。

> 其实，ES6的类，可以看作是构造函数的另外一种写法。

```js
class B{}
var b = new B();
typeof B;               //`function`
typeof  B === B.prototype.constructor                   //true
typeof  b.constructor === B.prototype.constructor      //true
```

### 对比ES6和ES5，有以下相同点

+ 类的数据类型就是函数，类本身就指向构造函数。
+ 在类的实例上调用方法，就是调用原型上的方法。

### 再来看ES6与ES5的不同点

 > 由于类的方法都定义在prototype上面，那么我们可以使用Object.assign方法给类一次添加多个方法。prototype的constructor方法，直接指向“类”本身，这和ES5的行为是有区别的。另外，类内部所有定义的方法，都是不可枚举的，而ES5中，prototype上的方法都是可枚举的。
 
示例如下：

```js
//ES5
function Test(){}
Test.prototype.log = function(){}
var test = new Test();

Object.keys(Test.prototype);    //列举出给定对象自身可枚举属性(不包括原型上的属性)   
//['log']

Object.getOwnPropertyNames(Test.prototype);      //列举出给定对象所有自身属性的名称（包括不可枚举属性但不包括Symbol值作为名称的属性）  
//['prototype','log']

//ES6
class Test2{
    toValue(){}
}
var test2 = new Test2();

Object.keys(Test2.prototype);
//[]

Object.getOwnPropertyNames(Test2.prototype);
//['constructor','toValue'];
```

## Class的静态方法

 类中所定义的方法，都会被实例继承，如果在一个方法前，加上static关键字，那该静态方法不会被实例继承，而是直接通过类来调用的。

```js
class Foo{
    static addMethod(){
        console.log(this);
        console.log('hello class');
        //this.
    }
}

//直接通过类来调用
Foo.addMethod();        //Foo ,  hello class

//实例不会继承静态方法，所以调用会报错
var foo = new Foo();
foo.addMethod();    //TypeError:foo.addMethod is not a function
```

**注意：**

+ 如果static静态方法包含this关键字，这个this指的是类，而不是实例。
+ 静态方法可以与非静态方法同名。
+ 父类的静态方法，可以被子类继承。
+ 静态方法可以从super对象上调用。
