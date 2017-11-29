---
layout: post
title: "JavaScript 闭包可被利用的常见场景"
date: 2017-03-18 00:41:15 +0800
categories: 学习笔记
tags: JavaScript
---


　　闭包（Closures）在 JavaScript 这类支持现代函式编程（FP，Functional Programming）的语言中非常普遍。[到底什么是闭包?](/2017/01/16/js-closure/)由于一个函式本身作为一级对象（first-class object），就像传统 OOP 中的对象一样，可以出现在任何一个值（value）允许出现的地方，因此只要你在一个（父）函式的内部声明、创建或者返回了一个新（子）函式，都有可能获得一个闭包。

　　下面介绍几种常见的场景。<!-- more -->

## 采用函数引用方式的setTimeout调用

　　闭包的一个通常的用法是为一个在某一函数执行前先执行的函数提供参数。例如，在web环境中，一个函数作为setTimeout函数调用的第一个参数，是一种很常见的应用。

　　setTimeout将要执行的函数(或者一段javascript代码，但这不是我们要讨论的情况)作为它的第一个参数，下一个参数是需要延迟执行的时间。如果一段代码想通过setTimeout来调用，那么它需要传递一个函数对象的引用来作为第一个参数。延迟的毫秒数作为第二个参数，但这个函数对象的引用无法为将要被延迟执行的对象提供参数。

　　但是，可以调用另一个函数来返回一个内部函数的调用，将那个内部函数对象的引用传递给setTimeout函数。内部函数执行时需要的参数，在调用外部函数时传递给它。setTimeout在执行内部函数时无需传递参数，因为内部函数仍然能够访问外部函数调用时提供的参数：

```js
function callLater(paramA, paramB, paramC) {
    /*使用函数表达式创建并放回一个匿名内部函数的引用*/
    return (function () {
        /*
        这个内部函数将被setTimeout函数执行；
        并且当它被执行时，
        它能够访问并操作外部函数传递过来的参数
        */
        paramA[paramB] = paramC;
    });
}
 
/*
调用这个函数将在它的执行上下文中创建，并最终返回内部函数对象的引用
传递过来的参数，内部函数在最终被执行时，将使用外部函数的参数
返回的引用被赋予了一个变量
*/
var funcRef = callLater(elStyle, "display", "none");
/*调用setTimeout函数，传递内部函数的引用作为第一个参数*/
hideMenu = setTimeout(funcRef, 500);
```

## 将函数关联到对象的实例方法

　　有很多这样的场景：需要分配一个函数对象的引用，以便在未来的某个时间执行该函数。那么闭包对于为这个将要执行的函数提供引用会非常有帮助。因为该函数可能直到执行时才能够被访问。

　　有一个例子就是，一个javascript对象被封装用来参与一个特定DOM元素的交互。它有doOnClick、doMouseOver以及doMouseOut方法。并且想在DOM元素上对应的事件被触发时执行这些方法。但是，可能会有关联着DOM元素的任意数量的javascript对象被创建，并且单个的实例并不知道那些实例化它们的代码将如何处理它们。对象实例不知道怎样去“全局”地引用它们自己，因为它们不知道哪个全局变量(如果存在)的引用将被分配给它们。

　　所以，问题是执行一个与特定javascript对象实例关联的事件处理函数，并且知道调用那个对象的哪个方法。

　　接下来的一个例子，在有元素事件处理的对象实例的关联函数上使用一个简单的闭包。通过传递event对象以及要关联元素的一个引用，为事件处理器分配不同的对象实例方法以供调用。

```js
/*
一个给对象实例关联一个事件处理器的普通方法，
返回的内部函数被作为事件的处理器，
对象实例被作为obj参数，对象上将要被调用的方法名称被作为第二个参数
*/
function associateObjWithEvent(obj, methodName) {
    /*返回的内部函数被用来作为一个DOM元素的事件处理器*/
    return (function (e) {
        /*
        事件对象在DOM标准的浏览器中将被转换为e参数，
        如果没有传递参数给事件处理内部函数，将统一处理成IE的事件对象
        */
        e = e || window.event;
        /*
        事件处理器调用obj对象上的以methodName字符串标识的方法
        并传递两个对象：通用的事件对象，事件处理器被订阅的元素的引用
        这里this参数能够使用，因为内部函数已经被执行作为事件处理器所在元素的一个方法
        */
        return obj[methodName](e, this);
    });
}
 
/*
这个构造器函数，通过将元素的ID作为字符串参数传递进来，
来创建将自身关联到DOM元素上的对象，
对象实例想在对应的元素触发onclick、onmouseover、onmouseout事件时
对应的方法被调用。
*/
function DhtmlObject(elementId) {
    /*
    调用一个方法来获得一个DOM元素的引用
    如果没有找到，则为null
    */
    var el = getElementWith(elementId);
    /*
    因为if语句块，el变量的值在内部进行了类型转换，变成了boolean类型
    所以当它指向一个对象，结果就为true,如果为null则为false
    */
    if (el) {
        /*
        为了给元素指定一个事件处理函数，调用了associateObjWithEvent函数，
        利用它自己(this关键字)作为被调用方法的对象，并且提供方法名称
        */
        el.onclick = associateObjWithEvent(this, "doOnClick");
        el.onmouseover = associateObjWithEvent(this, "doOnMouseOver");
        el.onmouseout = associateObjWithEvent(this, "doOnMouseOut");
    }
}
 
DhtmlObject.prototype.doOnClick = function (event, element) {
    //doOnClick body
}
DhtmlObject.prototype.doMouseOver = function (event, element) {
    //doMouseOver body
}
 
DhtmlObject.prototype.doMouseOut = function (event, element) {
    //doMouseOut body
}
```

　　任何DhtmlObject的实例都能够将它们自身关联到它们感兴趣的DOM元素上去，不需要去担心这些元素将被其他的代码怎么处理，以及被全局命名空间“污染”或者与其他的DhtmlObject的实例产生冲突。

## 封装相关的功能集

　　闭包可以创建额外的scope，这可以被用来组合相关的或有依赖性的代码。用这种方式可以最大限度地减少代码干扰的危害。假设，一个函数被用来创建一个字符串并且避免重复串联的操作(比如创建一系列的中间字符串)。一个想法是，用一个数组来顺序存储字符串的一部分，然后使用Array.prototype.join方法输出结果(使用一个空字符串作为它的参数)。数组将扮演着输出缓冲区的角色，但局部定义它又将会导致它在函数的每次执行时再次创建。如果这个数组只是作为唯一的变量被分配给每一个函数调用，这将会有点小题大做。

　　一个解决方案是将数组提升为全局变量，让它不需要被再次创建也能够再次使用。但结果并不是想的那么简单，另外，一个全局变量关联这使用缓冲数组的函数，那将会有第二个全局属性(函数本身也是window对象的属性)关联这个数组，这将让代码失去一定的可控性。因为如果将它使用在其他地方。这段代码的创建者不得不记住包含函数的定义以及数组的定义逻辑。它也使得代码不那么容易与其他代码整合，因为将从原来只需要确定函数名是否在全局命名空间中唯一，变成有必要确定和该函数关联的数组的名称是否在全局命名空间中保持唯一。

　　一个闭包可以让缓冲数组关联(干净地包含)它依赖的函数，并且同时保持缓冲数组的属性名称，像被分配在全局空间中一样，同时能够避免名称冲突以及代码交互干扰的危险。

　　这里有一招就是通过执行一个内联的函数表达式创建一个额外的执行上下文，让那个函数表达式返回一个内联的函数，该函数被外部代码使用。缓冲数组被定义在内联执行的函数表达式中，作为一个局部变量。它仅被调用一次，所以该数组只被创建一次。但是对于依赖它的函数来说该数组是一直可访问的，并且可被重用的。

　　接一下的代码创建了一个函数，将返回一个HTML字符串，其中的一部分是不变的，但那些不变的字符串需要被穿插进作为参数传递进来的变量中。

　　一个内联执行的函数表达式返回了内部函数对象的一个引用。并且分配了一个全局变量，让它可以被作为一个全局函数来调用。而缓冲数组作为一个局部变量被定义在外部函数表达式中。它没有被扩展到全局命名空间中，并且无论函数什么时候使用它都不需要被再次创建。

```js
/*
 定义一个全局变量：getImgInPositionedDivHtml
 被赋予对外部函数表达式一次调用返回的一个内部函数表达式
 
 内部函数返回了一个HTML字符串，代表一个绝对定位的DIV
 包裹这一个IMG元素，而所有的变量值都被作为函数调用的参数
*/
var getImgInPositionedDivHtml = (function () {
    /*
    buffAr 数组被定义在外部函数表达式中，作为一个局部变量
    它只被创建一次。数组的唯一实例对内部函数是可见的，
    所以它可以被用于每一次的内部函数执行
 
    空字符串仅仅被用来作为一个占位符，它将被内部函数的参数代替
    */
    var buffAr = [
         '<div id="',
        '',   //index 1, DIV ID attribute
        '" style="position:absolute;top:',
        '',   //index 3, DIV top position
        'px;left:',
        '',   //index 5, DIV left position
        'px;width:',
        '',   //index 7, DIV width
        'px;height:',
        '',   //index 9, DIV height
        'px;overflow:hidden;\"><img src=\"',
        '',   //index 11, IMG URL
        '\" width=\"',
        '',   //index 13, IMG width
        '\" height=\"',
        '',   //index 15, IMG height
        '\" alt=\"',
        '',   //index 17, IMG alt text
        '\"><\/div>'
    ];
 
    /*
    返回一个内部函数对象，他是函数表达式执行返回的结果
    */
    return (function (url, id, width, height, top, left, altText) {
        /*
        分配各种参数给对应的数组元素
        */
        buffAr[1] = id;
        buffAr[3] = top;
        buffAr[5] = left;
        buffAr[13] = (buffAr[7] = width);
        buffAr[15] = (buffAr[9] = height);
        buffAr[11] = url;
        buffAr[17] = altText;
 
        /*
        返回连接每个元素后创建的字符串
        */
        return buffAr.join('');
    });
})();
```

　　如果一个函数依赖另一个或几个函数，但那些其他的函数并不期望与任何其他的代码产生交互。那么这个简单的技巧(使用一个对外公开的函数来扩展那些函数)就可以被用来组织那些函数。

<hr>