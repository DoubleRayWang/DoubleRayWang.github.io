---
layout: post
title: "JavaScript 实现继承的方式"
date: 2017-04-09 10:41:15 +0800
categories: 学习笔记
tags: JavaScript
---


　　这篇文章主要介绍 JavaScript 实现继承的方式：

- 类式继承
- 构造函数继承
- 组合继承
- 寄生组合式继承
- extends继承

　　**本文参考[TG-路虽远，无所畏](http://ghmagical.com/) 的[JavaScript实现继承的方式](http://ghmagical.com/article/page/id/omIoPb1AIBPu)。**<!-- more -->

## 1. 类式继承

简单的类式继承：

```js
// 声明父类
function Animal() {
  this.name = 'animal';
  this.type = ['pig', 'cat'];
}
// 为父类添加共有方法
Animal.prototype.greet = function(sound) {
  console.log(sound);
}
// 声明子类
function Dog() {
  this.name = 'dog';
}
// 继承父类
Dog.prototype = new Animal();
var dog = new Dog();
dog.greet('汪汪');  //  "汪汪"
console.log(dog.type); // ["pig", "cat"]
```

　　在上面的代码中，我们创建了两个类Animal和Dog，而且给`Animal.prototype`原型上添加了一个greet共有方法，然后通过new命令实例化一个Animal，并且赋值给`Dog.prototype`原型。

　　原理说明：在实例化一个类时，新创建的对象复制了父类的构造函数内的属性与方法并且将原型`__proto__`指向了父类的原型对象，这样就拥有了父类的原型对象上的属性与方法。

　　不过，通过类式继承方式，有两个缺点:

- **第一个是引用缺陷**：

```js
dog.type.push('dog');
var dog2 = new Dog();
console.log(dog2.type);  // ["dog", "cat", "dog"]
```

　　通过上面的执行结果，我们看到当通过dog实例对象修改继承自Animal中的数组type(引用类型)时，另外一个新创建的实例dog2也会受到影响。

- **第二个是我们无法为不同的实例初始化继承来的属性，我们可以修改一下上面的例子**：

```js
function Animal(color) {
  this.color = color;
}
Dog.prototype = new Animal('白色');
console.log(dog.color); // "白色"
console.log(do2.color); // "白色"
```

　　通过上面的代码可以看到，我们无法为不同dog赋值不同的颜色，所有dog只能同一种颜色。

## 2. 构造函数继承

构造函数继承方式可以避免类式继承的缺陷：

```js
// 添加共有方法
Animal.prototype.greet = function(sound) {
  console.log(sound);
}
// 声明子类
function Dog(color) {
  Animal.apply(this, arguments);
}

var dog = new Dog('白色');
var dog2 = new Dog('黑色');

dog.type.push('dog');
console.log(dog.color);  // "白色"
console.log(dog.type);  // ["pig", "cat", "dog"]
console.log(dog2.type);  // ["pig", "cat"]
console.log(dog2.color);  // "黑色"
```

　　首先要知道`apply`方法的运用，它是可以更改函数的作用域，所以在上面的例子中，我们在Dog子类中调用这个方法也就是将Dog子类的变量在父类中执行一遍，这样子类就拥有了父类中的共有属性和方法。

　　但是，构造函数继承也是有缺陷的，那就是我们无法获取到父类的共有方法，也就是通过原型prototype绑定的方法：

```js
dog.greet();  // Uncaught TypeError: dog.greet is not a function
```

## 3. 组合继承

组合继承其实就是将类式继承和构造函数继承组合在一起：

```js
// 声明父类   
function Animal(color) {    
  this.name = 'animal';    
  this.type = ['pig','cat'];    
  this.color = color;   
}
// 添加共有方法  
Animal.prototype.greet = function(sound) {    
  console.log(sound);   
}
// 声明子类   
function Dog(color) { 
  // 构造函数继承    
  Animal.apply(this, arguments);   
}   
// 类式继承
Dog.prototype = new Animal();   
var dog = new Dog('白色');   
var dog2 = new Dog('黑色');     
dog.type.push('dog');   
console.log(dog.color); // "白色"
console.log(dog.type);  // ["pig", "cat", "dog"]
console.log(dog2.type); // ["pig", "cat"]
console.log(dog2.color);  // "黑色"
dog.greet('汪汪');  // "汪汪"
```

　　在上面的例子中，我们在子类构造函数中执行父类构造函数，在子类原型上实例化父类，这就是组合继承了，可以看到它综合了类式继承和构造函数继承的优点，同时去除了缺陷。

　　可能你会奇怪为什么组合式继承可以去除类式继承中的引用缺陷？其实这是由于原型链来决定的，由于JavaScript引擎在访问对象的属性时，会先在对象本身中查找，如果没有找到，才会去原型链中查找，如果找到，则返回值，如果整个原型链中都没有找到这个属性，则返回undefined。

　　也就是说，我们访问到的引用类型(比如上面的type)其实是通过apply复制到子类中的，所以不会发生共享。

　　这种组合继承也是有点小缺陷的，那就是它调用了两次父类的构造函数。

## 4. 寄生组合式继承

寄生组合式继承强化的部分就是在组合继承的基础上减少一次多余的调用父类的构造函数：

```js
function Animal(color) {
  this.color = color;
  this.name = 'animal';
  this.type = ['pig', 'cat'];
}
Animal.prototype.greet = function(sound) {
  console.log(sound);
}
function Dog(color) {
  Animal.apply(this, arguments);
  this.name = 'dog';
}
/* 注意下面两行 */
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;
Dog.prototype.getName = function() {
  console.log(this.name);
}
var dog = new Dog('白色');   
var dog2 = new Dog('黑色');     
dog.type.push('dog');   
console.log(dog.color);   // "白色"
console.log(dog.type);   // ["pig", "cat", "dog"]
console.log(dog2.type);  // ["pig", "cat"]
console.log(dog2.color);  // "黑色"
dog.greet('汪汪');  //  "汪汪"
```

　　在上面的例子中，我们并不像构造函数继承一样直接将父类Animal的一个实例赋值给Dog.prototype，而是使用Object.create()进行一次浅拷贝，将父类原型上的方法拷贝后赋给Dog.prototype，这样子类上就能拥有了父类的共有方法，而且少了一次调用父类的构造函数。

　　`Object.create()`的浅拷贝的作用类式下面的函数：

```js
function create(obj) {
  function F() {};
  F.prototype = obj;
  return new F();
}
```

　　这里还需注意一点，由于对Animal的原型进行了拷贝后赋给Dog.prototype，因此Dog.prototype上的constructor属性也被重写了，所以我们要修复这一个问题：

```js
Dog.prototype.constructor = Dog;
```

## 5. extends继承

　　`Class`和`extends`是在ES6中新增的，`Class`用来创建一个类，`extends`用来实现继承：

```js
class Animal {   
  constructor(color) {   
    this.color = color;   
  }   
  greet(sound) {   
    console.log(sound);   
  }  
}   
class Dog extends Animal {   
  constructor(color) {   
    super(color);   
    this.color = color;   
  }  
}   
let dog = new Dog('黑色');  
dog.greet('汪汪');  // "汪汪"
console.log(dog.color); // "黑色"
```

　　在上面的代码中，创建了父类Animal，然后Dog子类继承父类，两个类中都有一个`constructor`构造方法，实质就是构造函数Animal和Dog。

　　不知道你有没有注意到一点，我在子类的构造方法中调用了`super`方法，它表示父类的构造函数，用来新建父类的`this`对象。

> **注意**：子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。这是因为子类没有自己的`this`对象，而是继承父类的`this`对象，然后对其进行加工。如果不调用`super`方法，子类就得不到`this`对象。

<hr>