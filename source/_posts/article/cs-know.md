---
layout: post
title: "初探 CoffeeScript"
date: 2017-01-18 12:19:15 +0800
categories: 实践之路
tags: CoffeeScript
---

　　`CoffeeScript`是一门简洁的，构架于`JavaScript`之上的预处理器语言，可以静态编译成`JavaScript`，语法主要受`ruby`和`python`影响，目前已经为众多`rails`和`node`项目采用。<!-- more -->

## 1. 前言

>　为什么要用`CoffeeScript`?

+ 更少，更紧凑，和更清晰的代码
+ 通过规避和改变对`JavaScript`中不良部分的使用，只留下精华，让代码减少出错率，更容易维护
+ 在很多常用模式的实现上采用了`JavaScript`中的最佳实践
+ `CoffeeScript`生成的`JavaScript`代码都可以完全通过`JSLint`的检测

>　什么情况下不推荐使用`CoffeeScript`?

+ `CoffeeScript`不是`JavaScript`的超集，也**不是完全替代品**，不应该在不会`JavaScript`的情况下使用`CoffeeScript`工作。

　　`CoffeeScript`是一种需要预编译的语言，不能在运行时(`Runtime`)解释，这造成了她普遍被人质疑的一点，就是如果代码中出现运行时错误时难以调试，不过从实际使用上来看，因为`CoffeeScript`的编译结果大部分情况下自然而合理，至少我从来没有发现从生成的`JavaScript`代码回溯到对应的`CoffeeScript`代码有什么困难之处，我们稍后会看到这种对应关系的细节。

　　这种静态编译还有一个额外的好处，就是`CoffeeScript`和现有的环境(浏览器, Node , Rhino 等)与库完全兼容。

>　`CoffeeScript`安装

　　最简单的安装和测试`CoffeeScript`的方法，是使用`node.js`的`npm`安装，然后使用命令行脚本实时编译:

```
npm install -g coffee-script
# watch and compile
coffee -w --output lib --compile src
```

　　这里假设你的`coffee`代码在`src`目录下，这个 demo 会自动检测文件的改变，并编译成`js`文件放到`lib`目录下。

## 2. 语法

　　与`SASS/LESS`和`CSS`的关系不同，**`CoffeeScript`不是`JavaScript`的超集，不能在`CoffeeScript`程序中写`JavaScript`代码**，比如`function`等关键字。

### 2.1 格式

　　在`js`中，如果认为当前语句和随后语句是一个整体的话，就不会自己加`;`，比如以下`javascript`代码:

```js
//javascript code
var y = x+f
(a+b).toString()

//parsed to:
var y = x+f(a+b).toString();
```

　　很多`js`中的问题由此引起(实际上现在把`;`放在哪里，在`js`社区内也是个争论的话题)；

　　而`CoffeeScript`在编译时为每条语句加上`;`，因此在代码中不需要写`;`；

　　`CoffeeScript`中的注释采用`#`:

```coffeescript
# single line comment
### 
  multi line comment
###    
```

　　`CoffeeScript`中对空白敏感，这种做法来自`python`，任何需要(`{}`)的场合下，可以用缩进代替。

### 2.2 作用域

　　在`js`中最糟糕的设计就是全局变量，当你忘记用`var`声明变量的时候，这个变量会成为全局对象上的一个属性;`CoffeeScript`避免了这点：

```coffeescript
foo = "bar"
```

　　会编译成：

```js
(function() {
  var foo;
  foo = "bar";
}).call(this);
```

　　任何的代码都会使用**`Immediate Function`**包装，这样`foo`成为了本地变量，并且，可以通过`call`指定的`this`引用全局对象，为了方便起见，之后的编译后代码描述不会再加上这个包装。

　　实际上在`CoffeeScript`中，你也不需要再用`var`声明变量，编译后会自动加上`var`，并且将声明`hoisting`，即放到作用域的顶部，看一个来自官方文档的例子：

```coffeescript
outer = 1
change = ->
  inner = -1
  outer = 10
inner = change()
```

　　`->`是函数定义的简写方式，之后我们会探讨；编译后的`js`如下：

```js
var change, inner, outer;

outer = 1;

change = function() {
  var inner;
  inner = -1;
  return outer = 10;
};

inner = change();
```

　　这是类似`ruby`中的自然的作用域实现方式，`inner`在`change()`内定义成了局部变量，因为在代码中之前没有定义过。

### 2.3 赋值

　　首先是字符串可以用类`ruby`的语法内嵌：

```coffeescript
target = "world"
alert "hello, #{target}"
```

　　其次是字面量，可以用类似`YAML`的方法定义对象字面量：

```coffeescript
object1 = one: 1, two: 2
object2 =
  one: 1
  two: 2
  class: "numbers"
```

　　注意保留字`class`，现在可以直接作为对象的`key`了。

　　数组也可以分行：

```coffeescript
arr = [
  1
  2
]
```

　　也可以解构赋值(Destructuring)：

```coffeescript
obj = {a:"foo", b:"bar"}
{a, b} = obj
arr = [1, 2]
[a, b] = arr
```

### 2.4 数组

　　数组的操作引入了来自`ruby`的`Range`概念，并且可以将字符串完全作为数组操作：

```coffeescript
numbers = [0..9]
numbers[3..5] = [-3, -4, -5]
my = "my string"[0..1]
```

　　判断一个值是否在数组内，在`js`中可以用`Array.prototype.indexOf`，不过 IE8 及以下不支持，`CoffeeScript`提供了跨浏览器的`in`操作符解决：

```coffeescript
arr = ["foo", "bar"]
"foo" in arr
```

　　具体的实现上，是一个对`indexOf`的`Shim`：

```js
 var arr,
   __indexOf = [].indexOf || function(item) { 
     for (var i = 0, l = this.length; i < l; i++) { 
       if (i in this && this[i] === item) 
         return i; 
     } 
     return -1; 
   };

arr = ["foo", "bar"];

__indexOf.call(arr, "foo") >= 0;
```

　　`for..in`语法可以用在数组上了，背后是用`js`的`for`循环实现的，这比数组的迭代器方法要效率高一些：

```coffeescript
for name, i in ["Roger", "Roderick"]
  alert "#{i} - Release #{name}"
```

　　　也具有过滤器`when`：

```coffeescript
prisoners = ["Roger", "Roderick", "Brian"]
release prisoner for prisoner in prisoners when prisoner[0] is "R"
```

　　看起来很像普通英语了，也可以用`()`收集遍历的结果：

```coffeescript
result = (item for item in array when item.name is "test")
```

　　遍历对象的属性可以用`of`,这是用`js`自己的`for..in`实现的：

```coffeescript
names = sam: seaborn, donna: moss
alert("#{first} #{last}") for first, last of names
```

### 2.5 流程控制

　　`CoffeeScript`使用来自`ruby`的省略语法，让控制流变得很紧凑，也引进了`unless,not,then`等语法糖式的关键字：

```coffeescript
result = if not true then "false"
result = unless true then "false"
```

　　`CoffeeScript`中非常好的一点，就是直接取消了`js`中的`==`判断，改成全部用`===`进行严格比较，`js`中的`==`会做大量诡异的类型转换，很多情况下是 bug 的来源；

```coffeescript
if "1" == 1 
  alert("equal")
else
  alert("not equal")
```

　　在使用`if`来进行空值的判断时，`js`有时会让人困扰，因为`""`和`0`都会被转换成`false`，Coffee 提供了`?`操作符解决这个问题，她只有在变量为`null`或`undefined`时才为`false`。

```coffeescript
""? #true
null? #false
```

　　也可以用常见的类似`ruby`中`||=`的方法，判断赋值，此外还可以用`and,or,is`关键字代替`&&,||,==`。

```coffeescript
hash or= {}
hash ?= {}
```

　　经常有当某个属性存在的时候，才会调用属性上的方法的情况，这时候也可以用`?`；

```coffeescript
knight.hasSword()?.poke()
```

　　只有当`hasSword()`返回对象不为空时，才会调用`poke`方法，以下是编译的`js`代码：

```js
var _ref;
if ((_ref = knight.hasSword()) != null) {
  _ref.poke();
}
```

　　另一种情况是当`poke`方法存在时才调用：

```coffeescript
knight.hasSword().poke?()
```

　　对应的js代码：

```js
var _base;
if (typeof (_base = knight.hasSword()).poke === "function") {
  _base.poke();
}
```

　　`switch case`语句也有了一些语法糖，并且会默认加上`break`：

```coffeescript
switch day
  when "Sun" then go relax
  when "Sat" then go dancing
  else go work
```

### 2.6 函数

　　`CoffeeScript`对`JavaScript`的函数做了很大的简化，举个例子，看一个求和函数：

```coffeescript
sum = (nums...) ->
  nums.reduce (x, y) -> x+y

sum 1,2,3
```

　　对应`JavaScript`：

```js
var sum,
    __slice = [].slice;

sum = function() {
  var nums;
  nums = 1 <= arguments.length ? __slice.call(arguments, 0) : [];
  return nums.reduce(function(x, y) {
    return x + y;
  });
};

sum(1, 2, 3);
```

+ 可以使用和`ruby 1.9`类似的`lambda`函数写法`->`来代替`function`
+ 参数列表放在`->`的前边，且可省略
+ 取消了函数声明，只能将函数作为值定义
+ 在`CoffeeScript`中，任何语句都是表达式(除了`break`和`continue`)，都有返回值，因此像`ruby`一样，不需要显式`return`
+ `js`的函数参数有一个很讨厌的地方，就是参数对象`arguments`不是一个真正的数组，要使用数组方法，必须转换成数组`[].slice.call(arguments, 0)`这样，而在`CoffeeScript`中收束`(加...)`的参数是一个真正的数组

　　`CoffeeScript`的函数可以有默认参数，如：

```coffeescript
times = (a = 1, b = 2) -> a * b
```

　　`CoffeeScript`的函数调用可以不用`()`语法包围参数，像`ruby`一样跟在函数名后面就可以，不过这也有时候会带来问题，特别是没有参数的调用：

```coffeescript
alert
```

　　对应的`js`：

```js
alert;
```

　　而不是`alert()`，这和`ruby`不同，需要注意!

　　缩进的格式有时需要小心，比如用多个函数做参数的时候，需要这样写：

```coffee
$(".toggle").toggle ->
  "on"
, ->
  "off"
```

　　对应`js`：

```js
$(".toggle").toggle(function() {
  return "on";
}, function() {
  return "off";
});
```

## 3. 模式

　　使用`CoffeeScript`的一个重要理由，就是她用自己的语法实现了很多很常用的`js`编程模式，而且，通常是在社区内广泛被承认的最佳实践，如果不熟悉`JavaScript`的这些模式，可能会在调试代码上遇到一些麻烦，不过，基本上来说还是比较简单易懂的，下面我们会花一些时间研究一下`CoffeeScript`是用什么样的方法来封装这些通用编程模式的。

### 3.1 闭包

　　在`js`中，普遍会使用闭包实现各种事件的`handler`或封装模块，以下是`CoffeeScript`对这一普遍模式的实现。

```coffee
closure = do ->
  _private = "foo"
  -> _private

console.log(closure()) #=> "foo"
```

　　`do`关键词可以产生一个**Immediate Function**,下面是对应js代码：

```js
var closure;

closure = (function() {
  var _private;
  _private = "foo";
  return function() {
    return _private;
  };
})();
```

　　闭包中经常需要绑定`this`的值给闭包的私有变量，`CoffeeScript`使用特殊的`=>`语法省去了这个麻烦。

```coffee
@clickHandler = -> alert "clicked"
element.addEventListener "click", (e) => @clickHandler(e)
```

　　使用`=>`生成函数，可以看到生成代码中会加上对`this`的绑定：

```js
var _this = this;

this.clickHandler = function() {
  return alert("clicked");
};

element.addEventListener("click", function(e) {
  return _this.clickHandler(e);
});
```

　　这里`CoffeeScript`对于`this`有简单的别名`@`。

### 3.2 扩展

　　在`js`中，所有的对象都是开放的，有时候会扩展原有对象的行为(比如对数组的ECMA5 shim)，这也称为`Monkey patching`：

```coffee
String::dasherize = -> @replace /_/g, "-"
```

　　`::`代表原型的引用，`js`代码如下：

```js
String.prototype.dasherize = function() {
  return this.replace(/_/g, "-");
};
```

### 3.3 类

　　在`js`中是否要模拟传统编程语言的类，是个一直以来都有争议的话题，不同的项目，不同的团队，在类的使用上会有不同的看法，不过，一旦决定要使用类，那么至少需要一套良好的实现，`CoffeeScript`在语言内部实现了类的模拟，我们来看一看一个完整的例子：

```coffee
class Gadget
  @CITY = "beijing"

  @create: (name, price) ->
    new Gadget(name, price)

  _price = 0

  constructor: (@name, price) ->
    _price = price

  sell: =>
    "Buy #{@name} with #{_price} in #{Gadget.CITY}"

iphone = new Gadget("iphone", 4999)
console.log iphone.name #=> "iphone"
console.log iphone.sell() #=> "Buy iphone with 4999 in beijing"

ipad = Gadget.create("ipad", 3999)
console.log ipad.sell() #=> "Buy ipad with 3999 in beijing"
```

　　这个`Gadget`类具有通常语言中类的功能：

* `constructor`是构造函数，必须用这个名称，类似`ruby`中的`initialize`
+ `name`是实例变量,可以通过`iphone.name`获取
+ 构造函数中如果给实例变量赋值，直接将`@name`写在参数中即可，等价于在函数体中的`@name = name`
+ `_price`是私有变量,需要赋初始值
+ `sell`是实例方法
+ `create`是类方法，注意这里使用了`@create`，这和`ruby`有些像，在定义时的`this`指的是这个类本身
+ `CITY`是类变量

　　要注意的是，对于实例方法，要用`=>`来绑定`this`，这样可以作为闭包传递，比如：

```coffee
iphone = new Gadget("iphone", 4999)
$("#sell").click(iphone.sell())
```

　　如果不用`=>`，闭包被调用时就会丢失实例对象的值(`iphone`)。

　　对于熟悉基于类的面向对象编程的人，`CoffeeScript`的类是一目了然的，下面来看看对应的`js`代码：

```js
var Gadget,
  __bind = function(fn, me){ return function(){ return fn.apply(me, arguments); }; };

Gadget = (function() {
  var _price;

  Gadget.name = 'Gadget';

  Gadget.CITY = "beijing";

  Gadget.create = function(name, price) {
    return new Gadget(name, price);
  };

  _price = 0;

  function Gadget(name, price) {
    this.sell = __bind(this.sell, this);
    this.name = name;
    _price = price;
  }

  Gadget.prototype.sell = function() {
    return "Buy " + this.name + " with " + _price + " in " + Gadget.CITY;
  };

  return Gadget;

})();
```

　　以上的代码有很多值得注意的地方：

+ 整体上来说，`CoffeeScript`的类模拟使用的是一个构造函数闭包，这是最常用的模拟类的模式，好处是可以完整地封装内部变量，且可以使用`new`来生成实例对象
+ `_price`就是被封装在闭包内部的私有变量
+ `sell`这样的实例方法是原型方法，并且在初始化时使用自定义的`bind`函数绑定实例(用`=>`定义的情况)
+ `reate`和`CITY`这样的类成员使用构造函数的属性实现，重复一下，在`CoffeeScript`类定义中的`this`指的是整个闭包`Gadget`
+ `Gadget.name`是额外定义的类名属性

### 3.4 类的继承

　　`CoffeeScript`中为方便地实现类的继承也定义了自己的语法，我们把上面的类简化，来看一下如何继承：

```coffee
class Gadget
  constructor: (@name) ->
  sell: =>
    "Buy #{@name}" 

class IPhone extends Gadget
  constructor: -> super("iphone")
  nosell: =>
    "Don't #{@sell()}"

iphone = new IPhone
iphone.nosell() #=> Don't Buy iphone
```

+ 使用`extends`关键字可以继承父类中的所有实例属性,比如`sell`
+ `super`方法可以调用父类的同名方法
+ 如果不覆盖`constructor`，则她被子类默认调用

　　来看一下对应的`js`代码，这有一些复杂，我们把和上边类定义中重复的地方去掉，只留下继承的实现部分；

```js
var Gadget, IPhone,
  __extends = function(child, parent) { 
    for (var key in parent) { 
      if ({}.hasOwnProperty.call(parent, key)) 
        child[key] = parent[key]; 
    } 

    function ctor() { this.constructor = child; } 

    ctor.prototype = parent.prototype; 
    child.prototype = new ctor; 
    child.__super__ = parent.prototype; 

    return child; 
  };

IPhone = (function(_super) {

  __extends(IPhone, _super);

  IPhone.name = 'IPhone';

  function IPhone() {
    this.nosell = __bind(this.nosell, this);
    IPhone.__super__.constructor.call(this, "iphone");
  }

  IPhone.prototype.nosell = function() {
    return "Don't " + (this.sell());
  };

  return IPhone;

})(Gadget);
```

　　这里重点有三个：

+ `__extends`函数使用了代理构造函数ctor来实现继承，这是非常普遍的`js`中对象继承的实践模式，进一步解释一下：

  - 使用代理构造函数的目的是为了避免子类被更改时父类受到影响
  
  - 使用`ctor.prototype = parent.prototype`的意义是只继承定义在`prototype`上的公用属性

+ 父类的类成员被直接引用拷贝到子类，而不是原型继承
+ `super`的实现方法是`parent.prototype.constructor.call(this)`

### 3.5 混入(Mixin)

　　在`ruby`语言中的`Mixin`，能够让你的类获得多个模块的方法，可以说是对多重继承一种很好的实现，虽然在`CoffeeScript`中并没有像`ruby`的`include`一样的内置功能，但很容易实现他：

```coffee
class Module
  @extend: (obj) ->
    for key, value of obj 
      @[key] = value

  @include: (obj) ->
    for key, value of obj 
      @::[key] = value

classProperties =
  find: (id) ->
    console.log("find #{id}")

instanceProperties =
  save: ->
    console.log("save")

class User extends Module
  @extend classProperties
  @include instanceProperties

user = User.find(1)
user = new User
user.save()
```

- 继承了`Module`的类才可以`Mixin`，当然，这里也可以用组合或者直接为`js`的构造函数做`Monkey patching`
- `classProperties`是类成员模块，使用`@extend`来Mixin`，实现是简单的拷贝对象的属性
- `instanceProperties`是实例成员模块，使用`@include`来`Mixin`，实现是拷贝对象原型的属性
- 需要指出的是，这里的拷贝是引用拷贝，有可能外部会更改被`Mixin`的模块内部值，更好的方法是深层值拷贝(`clone`)，包括`JQuery`在内的很多类库都实现了这类扩展方法

## 4. 结语

　　`CoffeeScript`提供了一门比`JavaScript`更强大，优雅，表现力丰富的语言，但她毕竟架构于`JavaScript`之上，而且是静态地编译成`JavaScript`代码，也就是说，她不能完全避免对`JavaScript`中一些不良部分的滥用，比如`eval,typeof,instanceof`等，所以，在任何情况下，建议始终开启`Strict Mode`：

```js
"use strict"
```

　　严格模式是一个`ECMA5`标准提出的`js`子集，禁用了很多js设计中不好的方面，在未来会逐渐成为`js`的语言标准，详细介绍在[这里](/2017/01/24/js-strict/)。

<hr>