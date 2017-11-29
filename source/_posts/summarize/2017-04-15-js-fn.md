---
layout: post
title: "深入理解 JavaScript 中的函数"
date: 2017-04-15 16:41:15 +0800
categories: 学习笔记
tags: JavaScript
---


本文旨在提供web开发人员必须了解的所有JavaScript函数的基本知识。

函数于软件开发者而言并不是什么奇幻世界。如果你的日常活动涉及到编码，哪怕是一点点，那么在一天结束的时候，你一定创建/修改了一个或多个函数。

简而言之函数只不过是一组执行某个操作的语句。函数可能会有一些输入参数（在函数体中使用），并在执行后返回值。

> 本文转自 [码农网](http://www.codeceo.com/) 的 [深入理解 JavaScript 中的函数](http://www.codeceo.com/article/javascript-function-coding.html)。
<!-- more -->

JavaScript函数也具有这些特性，但它们不仅仅是常规函数。JavaScript函数是对象。你可以查看我曾经写的关于JavaScript对象的文章，里面我提到几乎JavaScript中的所有一切都是对象。

作为对象，JavaScript函数可能会有属性和其他函数（方法）。让我们来看看JavaScript中的一个典型的函数定义。

```js
function myNotSoGreatFunc(visitor) {
   console.log("Welcome to Code Morning Mr. " + visitor);
}
```

没错。上面的函数不涉及什么宏伟大业，因为它仅是对博客访问者表示了欢迎。但它展示了JavaScript函数的样子。函数定义从关键字function开始，然后是函数名，空的或有参数的括号。实际的函数代码（JavaScript语句）被封装在一对花括号内{ }。对于函数而言，return语句是可选的。JavaScript函数总是会返回一个值。当function主体中没有return语句时，那么function返回undefined。

![函数](/styles/images/js/function.png)

下面的代码调用传递visitor name作为参数的函数。

```js
myNotSoGreatFunc("Bob Martin");
// Output:&nbsp;
// Welcome to Code Morning Mr. Bob Martin.
```

到现在为止，我们了解了函数非常基本的特征。现在，我们将对JavaScript函数的一些高级概念一探究竟。

## 1. 匿名函数

JavaScript函数可以是匿名的。这意味着你可以从函数声明中省略函数名。但是，函数必须存储在变量中。

```js
var addNumbers = function (x, y) { return x + y; }
```

上述语法被也被称为函数表达式。你可以把变量addNumbers 当作函数名，以及像下面这样调用该函数。

```js
var sum = addNumbers(2, 3);
```

当你想传递一个函数作为参数给另一个函数时，函数表达式就非常方便了。让我们用一个简单的例子来试着了解这一点。

```js
var add = function (first, second) { return first + second };
var multiply = function (first, second) { return first * second };
 
function calculate(fun, a, b) {
    return fun(a, b);
}
```

首先我已经创建了两个匿名函数。第一个返回两个数的加法运算，第二个返回两个数的乘法运算。相当简单，没有什么可值得炫耀的地方。然后，我定义函数calculate，这个函数接受函数作为第一个参数后跟两个参数接受两个数字。

我可以通过传递任意函数作为第一个参数来调用函数calculate。

```js
var sum = calculate(add, 2, 3); // sum = 5
var multiplication = calculate(multiply, 2, 3); // multiplication = 6
```

你可以看到将函数作为参数传递是多么容易。这种模式在AJAX中大量使用，当你在AJAX调用完成后，传递回调函数处理成功或失败的场景时。

## 2. 关于参数的更多内容

JavaScript是非常灵活的，当涉及到传递或访问函数参数的时候。让我们看一下函数参数可以被操纵的方式。

### 2.1 缺少参数

调用函数时，函数的参数数量可以比要求的更少或更多。如果你调用的函数的参数比声明的少，那么缺少的参数被设置为undefined。

```js
function callMe(a, b, c) {
   console.log("c is " + typeof c);
}
 
callMe("Code", "Morning"); 
// Output: "c is undefined"
callMe("Learn", "JavaScript", "Functions"); 
// Output: "c is string"
```

### 2.2 Arguments对象

所有的JavaScript函数有一个特殊的对象，叫做arguments，它是在函数调用过程中传递的参数数组。该对象可以被用来访问单个参数或获得传递到函数的参数总数。

```js
function callMe() {
   var i;
   for (i = 0; i < arguments.length; i++) {
      console.log(arguments[i]);
   }
   console.log("Total arguments passed: " + arguments.length);
}
```

此函数假设没有传递任何参数，但就像我说的，你可以传递任何数量的参数到JavaScript函数。我可以像这样调用这个函数：

```js
callMe("Code", "Morning", "Mr. Programmer");
// Output":
// Code
// Morning
// Mr. Programmer
// Total arguments passed: 3
```

每个参数可以从arguments对象作为一个数组项被访问。被传递给函数的arguments的总数可从arguments.length属性获得。

### 2.3 默认参数

你是C ++或C#程序员吗？你见过使用默认参数的函数吗？也许你会回答yes！ ECMAScript 6带来了JavaScript的这一特性，就是你可以定义带有默认参数的函数。

```js
function greetMyVisitors(name, profession = "The cool programmer") {
    alert("Welcome Mr. " + name + ", " + profession);
}
```

该函数有礼貌地地迎接了博客访问者。它有两个参数name 和profession，并在消息框中显示一个欢迎消息。如果在调用过程中没有参数（或“undefined”）传递，那么第二个参数取用默认值。

```js
greetMyVisitors("Justin Bieber", "The singer"); 
// Shows the message "Welcome Mr. Justin Bieber, The singer"
 
greetMyVisitors("Bob Martin"); 
// Shows the message "Welcome Mr. Bob Martin, The cool programmer"
 
greetMyVisitors("John Papa", undefined); 
// Shows the message "Welcome Mr. John Papa, The cool programmer"
```

## 3. 嵌套函数

函数可以在它的内部包含一个或多个函数。内部函数可能会在内部再次包含函数。让我们来看看以下操作。

```js
function wakeUpAndCode() {
   function wakeUp() {
      console.log("I just woke up");
   }
 
   function code() {
      console.log("I am ready to code now");
   }
 
   wakeUp();
   code();
}
 
wakeUpAndCode();
 
// Output:
// I just woke up
// I am ready to code now
```

函数wakeUpAndCode包含两个内部函数wakeUp和code。当调用wakeUpAndCode时，函数主体开始执行函数主体。在外部函数中只有两个可执行语句，调用wakeUp和code的方法。调用wakeUp将执行内部wakeUp函数，这将写入string“I just woke up”到控制台。调用code将会写入“I am ready to code now”string到控制台。

内部函数可以访问所有外部函数的变量和参数。内部函数是函数内部某种private实现，并且不能从外部函数以外被调用。内部函数的使用生成了JavaScript闭包，这个我将另起一篇文章讨论。

## 4. 立即执行函数表达式（IIFE）

IIFE是被立即调用执行的匿名函数表达式。IIFE看上去像这样：

```js
(function() {
   // Your awesome code here
}());
```

所有你要做的就是创建一个匿名函数，在函数定义后马上放一对圆括号以调用函数，最后将所有代码封装在另一对圆括号中。最外层的括号将它里面的所有一切转变成一个表达式，因为括号不能包含JavaScript语句。函数定义后面的圆括号则立即调用函数。

IIFE块中定义的任何变量或函数对块而言是本地的，并且不能被这个范围以外的任何代码改变。

看看IIFE的这个例子。此函数没有调用也会自动执行。

```js
(function() {
   console.log("I run on my own.");
}());
```

只需在plunker中复制并粘贴代码，看看在浏览器控制台中的输出。如果你不知道去哪里找浏览器控制台，那么只要在浏览器窗口中按下F12就会出现开发者工具。跳转console选项卡以查看`console.log`语句的所有输出。

IIFE是一个在代码中创建局部范围的很好方法。它们可以帮助你保护变量和函数，以避免被应用程序的其他部分更改或覆盖。JavaScript中IIFE的其他优势？它们是如何解决全局范围污染问题的？欢迎点击查看我关于[立即执行函数表达式](/2017/01/14/js-IIFE/)的文章。

## 5. 构造函数

函数可以充当构造器的角色，并且可以使用构造函数来创建新的对象。这是使JavaScript面向对象的特点之一。使用构造函数的好处是，你将能够通过预定义的属性和方法，创造尽可能多的对象。如果你由此关联到其他语言中的类和对象，那么你做的对。

让我们创建一个带有一些属性和方法的构造函数Programmer。你可以假设它在你最喜欢的语言中是一个类。

```js
function Programmer(name, company, expertise) {
   this.name = name;
   this.company = company;
   this.expertise = expertise;
 
   this.writeCode = function() {
      console.log("Writing some public static thing..");
   }
 
   this.makeSkypeCall = function() {
      console.log("Making skype call..");
   }
 
   this.doSalsa = function() {
      console.log("I'm a programmer, I can only do Gangnam style..");
   }
 
   this.canWriteJavaScript = function() {
      return expertise === "JavaScript";
   }
}
```

函数有三个参数，并创建了一个具有三个属性和四种方法的对象。我不认为上面的代码需要任何解释。此外，我可以创建任意数量程序员对象。

```js
var javaProgrammer = new Programmer("Mohit Srivastava", "Infosys", "Java");
var dotnetProgrammer = new Programmer("Atul Mishra", "Prowareness", ".NET");
```

虽然也可以创建一个使用对象文本语法带有相同属性和方法的对象，但我们需要多次编写相同的代码，这可不是什么伟大的实践。如果你知道编程DRY原则，那么你就不会不赞同我。构造函数使得可以一次定义对象，并创建真正的实例，无论什么时候你想要。

> **注意**：

始终使用new关键字来从构造器创建新的对象。忘记了new而像这个创建一个实例：

```js
var jsProgrammer = Programmer("Douglas Crockford", "Yahoo", "JavaScript")
```

最终将添加所有属性和方法到全局的window对象，哇哦，这将是太可怕了。原因是，除非明确指定，否则“this”指向全局的window对象。使用new 设置“this”上下文到被创建的当前对象。

然而，有一种变通方法可以来克服这个问题。你可以改变构造函数的实现以使域安全，然后在创建新的对象时，你就可以愉快地忽略new 关键字了。请参见以下修改了的构造函数代码。为了便于查看，我已删除了一些方法。

```js
function Programmer(name, company, expertise) {
   if(!(this instanceof Programmer)) {
      return new Programmer(name, company, expertise);
   }
 
   this.name = name;
   this.company = company;
   this.expertise = expertise;
 
   this.writeCode = function() {
      console.log("Writing some public static thing..");
   }
}
```

if 条件检查了this 对象是否是Programmer的一个实例。如果不是，它会创建一个新的Programmer对象，并通过再次调用构造器返回相同的内容。

注意：你无法在不使用’new’关键字的情况下，在Strict模式下从构造器创建一个新的对象。Strict模式强制一些编码准则，并且在你写的东西不安全的情况下会抛出错误。要启用Strict模式，你只需要添加在你的代码开头添加字符串 ‘use strict’。在Strict模式下运行代码是一个良好的实践，可以查看我的另一篇文章[JavaScript 严格模式（strict）](/2017/01/24/js-strict/)了解更多。

```js
'use strict'
 function doSomething() { 
    //...
}
 //....
 //....
```

## 结语

在这篇文章中，我几乎已经涵盖了有关函数的所有内容。函数被认为是JavaScript中的一等公民。如果你想掌握JavaScript的话，理解函数可能是最重要的事情。

<hr>