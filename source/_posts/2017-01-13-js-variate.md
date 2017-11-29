layout: post
title: "JavaScript 变量作用域"
date: 2017-01-13 12:19:15 +0800
comments: false
type: "categories"
categories: 面试总结
tag: JavaScript
---

　　在 `JavaScript` 当中一个变量的作用域（scope）是程序中定义这个变量的区域。变量分为两类：全局（global）的和局部的。其中全局变量的作用域是全局性的，即在 `JavaScript` 代码中，它处处都有定义。而在函数之内声明的变量，就只在函数体内部有定义。它们是局部变量，作用域是局部性的。 **函数的参数也是局部变量**，它们只在函数体内部有定义，**局部变量省略了 var 也就默认成为了全局变量**。

　　在函数体内部，局部变量的优先级高于同名的全局变量，如果在函数体内声明一个局部变量或者函数的参数中带有的变量和全局变量重名，那么全局变量就会被局部变量所遮盖，**函数体内部可以读取到函数外的变量，而函数外不能读取到函数内的变量**。<!-- more -->

先看一个实例：

+ code 1

```js
var i=10;   
function a() {   
    alert(i);   
};   
a();  
```

+ code 2

```js
var i=10;   
function a() {   
    alert(i);   
    var i = 2;   
};   
a();   
```

　　根据“多年”的编程经验你可能觉得这两个代码输出是一样的，但是事实却是第一个代码正常输出了变量的值---10，而第二个代码输出的却是 undefined。由此可以引出关于变量应该关注的几个问题：

### 1. 函数作用域

　　为什么说`JavaScript`没有块级作用域呢，有以下代码为证：

```js
var name="global";  
if(true){  
    var name="local";  
    console.log(name)  
}  
console.log(name);  
```

　　都输出是 “local"，如果有块级作用域，明显 `if` 语句将创建局部变量 name，并不会修改全局 name，可是没有这样，所以Js没有块级作用域。

### 2. 声明提升

　　`JavaScript` 的变量声明具有`hoisting`机制，`JavaScript`引擎在执行的时候，会把所有变量的声明都提升到当前作用域的最前面，包括变量和函数的声明。

　　先看一段代码:

```js
(function(){
  var a = "1";
  var f = function(){};
  var b = "2";
  var c = "3";
})();
```

　　变量 a,f,b,c 的声明会被提升到函数作用域的最前面，类似如下：

```js
(function(){
  var a,f,b,c;
  a = "1";
  f = function(){};
  b = "2";
  c = "3";
})();
```

　　请注意函数表达式并没有被提升，这也是函数表达式与函数声明的区别。进一步看二者的区别：

```js
(function(){
  //var f1,function f2(){}; //hoisting,被隐式提升的声明
 
  f1(); //ReferenceError: f1 is not defined
  f2();
 
  var f1 = function(){};
  function f2(){}
})();
```

　　上面代码中函数声明`f2`被提升，所以在前面调用`f2`是没问题的。虽然变量`f1`也被提升，但`f1`提升后的值为`undefined`,其真正的初始值是在执行到函数表达式处被赋予的。所以只有声明是被提升的。

+ **名字解析顺序**

　　`JavaScript` 中一个名字(name)以四种方式进入作用域(scope)，其优先级顺序如下：

1. 语言内置：所有的作用域中都有 this 和 arguments 关键字
2. 形式参数：函数的参数在函数作用域中都是有效的
3. 函数声明：形如function foo() {}
4. 变量声明：形如var bar;

　　名字声明的优先级如上所示，也就是说如果一个变量的名字与函数的名字相同，那么函数的名字会覆盖变量的名字，无论其在代码中的顺序如何。但名字的初始化却是按其在代码中书写的顺序进行的，不受以上优先级的影响。看代码：

```js
(function(){
    var foo;
    console.log(typeof foo); //function
    function foo(){}
    foo = "foo";
    console.log(typeof foo); //string
})();
```

　　如果形式参数中有多个同名变量，那么最后一个同名参数会覆盖其他同名参数，即使最后一个同名参数并没有定义。
  
　　以上的名字解析优先级存在例外，比如可以覆盖语言内置的名字arguments。

### 3. 作用域链

　　先来看一段代码：

```js

var name="lwy";  
function t(){  
    var name="tlwy";  
    function s(){  
        var name="slwy";  
        console.log(name);  
    }  
    function ss(){  
        console.log(name);  
    }  
    s();  
    ss();  
}  
t();  
```

　　当执行 `s()` 时，将创建函数s的执行环境(调用对象),并将该对象置于链表开头，然后将函数 t 的调用对象链接在之后，最后是全局对象。然后从链表开头寻找变量 name,很明显 name 是 "slwy"。但执行 `ss()`时，作用域链是： `ss() -> t() -> window`，所以 name 是 “tlwy”，下面看一个很容易犯错的例子：

```html
<html>  
<head>  
<script type="text/javascript">  
function buttonInit(){  
    for(var i=1;i<4;i++){  
        var b=document.getElementById("button"+i);  
        b.addEventListener("click",function(){ alert("Button"+i);},false);  
    }  
}  
window.onload=buttonInit;  
</script>  
</head>  
<body>  
    <button id="button1">Button1</button>  
    <button id="button2">Button2</button>  
    <button id="button3">Button3</button>  
</body>  
</html>  
```

　　当文档加载完毕，给几个按钮注册点击事件，当我们点击按钮时，会弹出什么提示框呢？

　　很容易犯错，对是的，三个按钮都是弹出："Button4"，你答对了吗？当注册事件结束后， i 的值为4，当点击按钮时，事件函数即`function(){ alert("Button"+i);}`这个匿名函数中没有i，根据作用域链，所以到`buttonInit`函数中找，此时i的值为4，所以弹出 ”button4“。

+ **with 语句**

　　说到作用域链，不得不说 with` 语句。`with` 语句主要用来临时扩展作用域链，将语句中的对象添加到作用域的头部。看下面代码：

```js
person={name:"yhb",age:22,height:175,wife:{name:"lwy",age:21}};  
with(person.wife){  
    console.log(name);  
}  
```

　　with 语句将 person.wife 添加到当前作用域链的头部，所以输出的就是：“lwy"。with 语句结束后，作用域链恢复正常。