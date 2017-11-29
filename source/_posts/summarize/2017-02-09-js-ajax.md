---
layout: post
title: "Ajax 基础详解"
date: 2017-02-09 21:25:15 +0800
categories: 学习笔记
tags: Ajax
---

　　Ajax 全称为“Asynchronous JavaScript And XML”（异步 JavaScript 和 XML） 是指一种创建交互式网页应用的开发技术、改善用户体验，实现无刷新效果。<!-- more -->

## Ajax 基础

### 1. 什么是 Ajax

　　Ajax 不是一种新的编程语言，而是一种用于创建更好更快以及交互性更强的 Web 应用程序的技术。它是一套综合了多项技术的浏览器端网页开发技术。这些技术包括`Javascript`、`XHTML` 和 `CSS`、`DOM`、`XML` 和 `XMLHttpRequest`。

### 2. 为什么使用 Ajax？

> 优点:

　　使用 Ajax 的最大优点，就是能在不更新整个页面的前提下维护数据。这使得 Web 应用程序更为迅捷地响应用户动作，并避免了在网络上发送那些没有改变的 `html` 代码信息。

1. 减轻服务器负担，按需要获得数据。
2. 无刷新更新页面，减少用户的实际和心理的等待时间。
3. 更好的用户体验。
4. 减轻宽带的负担。
5. 主流浏览器支持。

> 缺点:

1. Ajax 的程序必须测试针对各个浏览器的兼容性。
2. Ajax 更新页面内容的时候并没有刷新整个页面，因此，网页的后退功能是失效的；需提醒用户。
3. 对搜索引擎支持不好。

### 3. Ajax 原理

- 运用 `HTML` 和 `css` 来实现页面,表达信息

- 通过浏览器的 `XmlHttpRequest` ( Ajax 引擎)对象

- 来向服务器(发送异步请求)并(接收服务器)的响应数据，

- 然后用 JavaScript 来操作 DOM，实现动态局部刷新。

<img src="/styles/images/ajax/ajax.gif" />

### 4. Ajax 实现过程

通常是：

> 第一步: `Javascript` 监听浏览器网页事件(点击,提交,更改等)

> 第二步:由 `Javascript` 创建 Ajax 引擎对象

> 第三步:通过 Ajax 引擎对象发出请求

> 第四步: Ajax 引擎等待并且接受服务器的响应内容

> 第五步: `Javascript` 再从 *Ajax引擎对象* 中获取响应内容,并且通过 DOM 改变网页界面显示效果

#### 4.1 创建Ajax引擎对象

　　Ajax 引擎对象，就是 `XMLHtttpRequest` 对象,所有现代浏览器均支持 `XMLHttpRequest` 对象。

> 兼容性处理：

```js
var xhr = null;
if (window.XMLHttpRequest) {
    //所有现代浏览器(IE7+.Firefox.Chrome.Safari以及opera)
    xhr = new XMLHttpRequest;
}else{
    //老版本的Internet Explorer使用ActiveX对象
    xhr = new ActiveXObject("Microsoft.XMLHttp");
}
```

#### 4.2  配置请求对象的信息

```js
xhr.open('GET/POST','url地址',['是否异步']);

//注意：如果使用post请求,就一定要设置请求头

xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded;charset=gb2312");
```

#### 4.3 发送请求

```js
xhr.send([post请求参数字符串]);
```

　　发送请求分为两种情况:

1. 监控用户的事件(onclick,onchange等),通过ajax发送请求
2. 监控浏览器的事件(onload),通过ajax发送请求

#### 4.4 监听 Ajax 引擎对象的改变(是否正确做出了响应)

```js
xmlHttpRequest.onreadystatechange = function(){
    if (xmlHttpRequest.readyState== 4 && xmlHttpRequest==200) {
            //xmlHttpRequest.responseText
            //xmlHttpRequest.responseText
    }
}
```

> - **详解 `onreadystatechange`**

　　`onreadystatechange`属性是一个方法，监控到响应内容的返回，根据响应内容使用`javascript`改变当前页面的部分`html`代码,从而达到不刷新改变局部`html`代码。当 Ajax 引擎的状态发生改变时都会执行`onreadystatechange`属性对应的方法。

> 其他方法：

    abort:取消当前请求

    getAllResponseHeaders:获取响应的所有http头

    getResponseHeader:从响应信息中获取指定的http头

    open(方式get/post，url地址，同步异步):
    创建一个新的http请求，打开请求，并指定此请求的方法、URL以及验证信息(用户名/密码)

    send():发送请求到http服务器并接收回应

    setRequestHeader:单独指定请求的某个http头，header()设置http头协议信息
    
> 其他属性：

    onreadystatechange:指定当readyState属性改变时的事件处理句柄。

    readyState:返回当前请求的状态，ajax行进过程中不同状态

    responseBody:将回应信息正文以unsigned byte数组形式返回.

    responseStream:以Ado Stream对象的形式返回响应信息。

    responseText:将响应信息作为字符串返回.只读

    responseXML:将响应信息格式化为Xml Document对象并返回，只读

    status:返回当前请求的http状态码. 200 ok 304 缓存；404 not found; 403 没有权限；501 服务器级别错误
    
    statusText:返回当前请求的响应行状态文本， ok not found forbidden

> - **详解状态属性 `readyState(0 1 2 3 4)`**

1. 请求未初始化
2. 服务器连接已建立
3. 请求已接收
4. 请求处理中
5. 请求已完成,并且响应已就绪

>当Ajax引擎的状态属性为`readyState`为 **4** 时,说明服务器的响应已经发送给 Ajax 请求了；

>但是响应的状态吗为**200**时：说明响应的内容是正确的,这时才会根据相应内容对当前页面的`html`处理。

### 5. 总结示例

```js
var xhr = new XMLHttpRequest;
xhr.open('GET/POST','url地址',['是否异步']);
xhr.send([post请求参数字符串]);
xhr.onreadystatechange = function(){
    if (xmlHttpRequest.readyState== 4 && xmlHttpRequest==200) {
            //做一些事情
            //xhr.responseText
    }
}
```

## Ajax 相关知识点

### 1. 同步请求和异步请求

　　先解释一下同步和异步的概念：

- 同步是指：发送方发出数据后，等接收方发回响应以后才发下一个数据包的通讯方式。

- 异步是指：发送方发出数据后，不等接收方发回响应，接着发送下个数据包的通讯方式。

<hr>

#### 1.1 同步请求

　　客户端请求(等待) -> 服务端处理 -> 响应 -> 页面载入（缺少对象:`XMLhttpRequest`）这时候如果有错误，只能再次发送请求，再次等待。

#### 1.2 异步请求

　　比如当你填写邮箱地址的时候，页面当时就把邮箱地址发送到了服务器（也就是页面发送了一个请求），服务器做**处理和响应**，得到你的邮箱地址填写重复了，把响应结果发给页面，在这个过程中你仍然可以填写其他内容，这时候服务器会提示你有重复的邮箱地址，在页面上的表现只是邮箱地址旁别加了一行字或者把整个文本框标红，并没有重新刷你的页面，所有的填写错误会实时的显示出来，你也会实时的更正。这个过程中，你会有整个页面的刷新，也不会有整个页面的提交和等待，最后提交，一切都会搞定的

> **页面上的操作和服务器端的操作互相之间不会造成阻塞**！

### 2. HTTP是什么

　　http 是计算机通过网络进行通信的规则。

> **一个完整的HTTP请求过程的七个步骤**：

1. 建立 TCP 连接
2. web 浏览器向 web 服务器发送请求命令
3. web 浏览器发送请求头信息
4. web 服务器应答
5. web 服务器发送应答头信息
6. web 服务器向浏览器发送数据
7. web 服务器关闭 TCP 连接

#### 2.1 作用

　　客户端向服务端发送请求信息和服务。

> **客户端**：自己的计算机的浏览器(web浏览器)

> **服务端**：服务器,一台高性能的计算机，作为存储，处理网络上的数据，信息(web服务器) ，服务器指一个管理资源并为用户提供服务的计算机软件，通常分为文件服务器、数据库服务器和应用程序服务器。运行以上软件的计算机或计算机系统也被称为服务器。

#### 2.2 特点

　　是一种无状态的协议(不建立持久的连接,服务端不保留连接的信息)。

#### 2.4 请求方法

+ **`GET`：**

   - 一般用于信息获取,用get请求-->查询或获取的操作
   - 使用URL传递参数
   - 对所发送信息的数量有限制,一般在2000个字
   

+ **`POST`：**

   - 一般用于修改服务器上的数据.比如:新建,修改,删除,发送表单数据
   - 对所发送信息的数量无限制
   
#### 2.5 HTTP 状态码

- **100**：信息类,表示web浏览器请求,正在进一步的处理中
- **200**：成功,表示用户请求被正确接收正在进一步的处理中 200 OK
- **300**：表示请求没有成功,客户端必须采取进一步的动作
- **400**：客户端错误,表示客户端提交的请求有错误 例如:404 NOT Found,意味着请求中所引用的文档不存在
- **500**：服务器错误 表示服务器不能完成对请求的处理,如500

#### 2.6 HTTP 响应

　　HTTP 响应一般由三部分组成：

1. 一个数字和文字组成的状态码,用来显示请求是成功还是失败
2. 响应头:响应头也和请求头一样包含许多有用的信息,例如：服务器类型,日期时间,内容类型和长度等
3. 响应体,也就是响应正文

#### 2.7 HTTP 请求

　　HTTP 请求的四部分组成：

1. HTTP 请求的方法或动作,比如 GET 还是 POST
2. 正在请求的 URL,总得知道请求的地址是什么
3. 请求头,包含一些客户端环境信息,身份验证信息等
4. 请求体,也就是请求正文,请求正文中可以包含客户提交的查询字符串信息,表单信息等等

<hr>