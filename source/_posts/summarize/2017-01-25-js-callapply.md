---
layout: post
title: "call() 和 apply() 详解"
date: 2017-01-25 10:41:15 +0800
categories: 学习笔记
tags: JavaScript
---


　　call() 和 apply() 都是为了改变某个函数运行时的 context 即上下文而存在的，换句话说，就是为了改变函数体内部 this 的指向。因为 JavaScript 的函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。

　　二者的作用完全一样，只是接受参数的方式不太一样!<!-- more -->

## 1. 定义

apply() : 方法能劫持另外一个对象的方法，继承另外一个对象的属性。

- Function.apply(obj,args)方法能接收两个参数
- obj：这个对象将代替Function类里this对象
- args：这个是数组，它将作为参数传给Function（args-->arguments）
   
call() : 和 apply() 的意思一样,只不过是参数列表不一样。
 
- Function.call(obj,[param1[,param2[,…[,paramN]]]])
- obj：这个对象将代替Function类里this对象
- params：这个是一个参数列表

## 2. 应用

　　**首先要非常明确几个概念**：

- JS 是面向对象语言；

- JS 的函数本质是对象，有对象就会有方法；

- call() 和 apply() 方法是JS函数对象的特有的方法，也就是自己调用自己。

　　以下是一段很简单的代码： 

```js
function writeZhihu(){
   console.log(this)
}
writeZhihu(); // [Object global] 在浏览器就是window; 在nodeJS中就是指node;
```

　　大家都知道在JS函数中都会有一个固定的实参this，这个this是指什么？

> 因为我们在全局环境中声明的这个函数，这个函数目前不属于任何对象，只属于全局对象；在浏览器环境中，该函数他爹就是 window;在 nodeJS 环境中就是 node;

　　因为我们要面向对象编程么？编程就要声明对象，有对象就有方法，或者说对象就要调用函数。好了，以下我们就要给这个函数找个新爹；

```js
function writeZhihu(){  //声明一个写知乎的能力函数；
   console.log(this)
}

var author ={name:"Zhangwei"} //我们声明一个对象，这个人的名字叫Zhangwei;
writeZhihu.call(author);  //所以当writeZhihu()这个函数自己调用自己的时候，同时传入一个对象的时候，我就牛逼了，我就有写作的超能力了。

//这时候console.log(this)的this打印出来就是{name:"Zhangwei"}；
//writeZhihu()这个函数表示有了归属感，么么哒。
```

　　那么这个方法的意义是什么？

```js
function writeZhihu(){  //写知乎的能力不可能就一个人拥有吧，很多人都可以啊？
   console.log(this)
}

var author1 ={name:"Zhangwei"}
var author2 ={name:"Mayun"}
var author3 ={name:"Mahuateng"}//我们创建很多个对象；

writeZhihu.call(author1);  //{name:"Zhangwei"}
writeZhihu.call(author2);   //{name:"Mayun"}
writeZhihu.call(author3);   //{name:"Mahuateng"}

//这样这些人就有写知乎的能力了，writeZhihu()函数表示好多土豪包养我呀！！！
```

　　那么这些人，每个人写知乎的内容都应该不一样吧，所以这就涉及到传参数的问题；

```js
function writeZhihu(header,content){  //写知乎的能力不可能就一个人拥有吧，很多人都可以啊？
   console.log(this.name+"的文章"+header+"，内容："+content)
}

var author1 ={name:"Zhangwei"}
var author2 ={name:"Mayun"}
var author3 ={name:"Mahuateng"}//我们创建很多个对象；

writeZhihu.call(author1,"《JS权威指南》","js不拉不拉...");  
writeZhihu.call(author2,"《老司机带带我》","江南皮革厂倒闭了...");  
writeZhihu.call(author3,"《不转不是中国人》","14亿中国人都看哭了...");   

/*那么打印出来的内容就是：
1.zhangwei的文章《JS权威指南》，内容：js不拉不拉...
2.Mayun的文章《老司机带带我》，内容：江南皮革厂倒闭了...
3.Mahuateng的文章《不转不是中国人》，内容：14亿中国人都看哭了...*/
```

　　那么好了，问题来了，尼玛，你标题不是说call()方法和apply()方法吗？为什么一直都在说call()方法，apply()方法喂狗了吗？

### call()和apply()的方法的区别

　　**call()和apply()的方法的区别在于参数的传递；**

　　call()方法有个缺陷就是传实参的时候走位比较耿直，比如我要传6个实参(header, content,license,platform,date,language;)；

```js
writeZhihu.call(author1,"《五年高考三年模拟》","我想你最爱","教育部","知乎","2016","Chinese");
```

　　是不是觉得这样传参数很傻逼。最要命的是，如果有的对象传递参数的数量的3个有的是4有的是5个，你是不是就彻底整懵逼了。

　　apply() 方法就是解决这个问题的，apply() 方法实参的传递是数组传递；

　　这个时候就可以这样咯：

```js
var args = ["《五年高考三年模拟》","我想你最爱","教育部","知乎","2016","Chinese"];
var args2 = ["《黄冈密卷》","童年阴影","教育部","2016","Chinese"];
writeZhihu.apply(author,args);
writeZhihu.apply(author,args2)
```

## 3. 总结

　　这个一定要多敲代码，理解，目前我的理解是aplly,call都是调用别人的方法并且讲方法里面的this改变，两者传参数不同，正因为传参数不同，所以可以用aplly用在一些目标函数只需要接受n个参数列表，而不接受数组的函数里面，文章中部分内容参考自[这里](http://www.cnblogs.com/xiaohongwu/archive/2011/06/15/2081237.html)、[JS中的call、apply、bind方法](http://ghmagical.com/article/page/id/UPLfoGI9vJ91)。

<hr>