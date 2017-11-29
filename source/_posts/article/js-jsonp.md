---
layout: post
title: "详解五种跨域方式及其原理"
date: 2017-02-10 14:41:15 +0800
categories: 学习笔记
tags: JavaScript
---


　　这里说的 js 跨域是指通过 js 在不同的域之间进行数据传输或通信，比如用 ajax 向一个不同的域请求数据，或者通过 js 获取页面中不同域的框架中(iframe)的数据。只要协议、域名、端口有任何一个不同，都被当作是不同的域。<!-- more -->

　　 **本文参考自 [JavaScript （微信公众号）](http://mp.weixin.qq.com/s/YykAwXyEqMDgc_5-b_cdsA)。**

> - 同源定义：

　　如果两个页面拥有相同的协议（protocol），端口（如果指定），和主机，那么这两个页面就属于同一个源（origin）。以下是同源检测的示例：

<div class="table-responsive"><table class="table table-bordered table-hover">
   <thead>
    <tr align="center">
        <td>URL</td>
        <td>结果</td>
        <td>原因</td>
   </tr>
    </thead>
    <tbody>
    <tr>
        <td>http://store.company.com/dir2/other.html</td>
        <td>Success</td>
        <td></td>
    </tr>
    <tr>
        <td>http://store.company.com/dir/inner/another.html</td>
        <td>Success</td>
        <td></td>
    </tr>
    <tr>
        <td>https://store.company.com/secure.html</td>
        <td>Failure</td>
        <td>协议不同</td>
    </tr>
    <tr>
        <td>http://store.company.com:81/dir/etc.html</td>
        <td>Failure</td>
        <td>端口不同</td>
    </tr>
    <tr>
        <td>http://news.company.com/dir/other.html</td>
        <td>Failure</td>
        <td>主机不同</td>
    </tr>
   </tbody>
</table></div>

## 1. jsonp

　　script 标签是不受同源策略影响的，它可以引入来自任何地方的 js 文件。 

　　而 jsonp 的原理就是，在客户端和服务端定义一个函数，当客户端发起一个请求时，服务端返回一段`javascript`代码，其中调用了在客户端定义的函数，并将相应的数据作为参数传入该函数。

```js
function jsonp_cb(data) {
    console.log(data);
}
function ajax(){
    var url = "http://xx.com/test.php?jsonp_callback=jsonp_cb";
    var script = document.createElement('script');
    // 发送请求
    script.src = url;
    document.head.appendChild(script);
}
ajax()
```

　　服务端获取到jsonp_callback传递的函数名jsonp_cb，返回一段对该函数调用的js代码：

```js
jsonp_cb({
   "name": "story"
});
```

## 2. img ping

　　`img` 标签也是没有跨域限制的，但它只能用来发送 GET 请求，且无法获取服务端的响应文本，可以利用它实现一些简单的、单向的跨域通信，例如跟踪用户的点击：

```js
var img = new Image();
img.onload = function(){
    console.log('done')
    img.onload = null;
    img = null;
}
img.src = "http://xx/xx.gif"
```

## 3. window.name

　　window 对象拥有 name 属性，它有一个特点：相同协议下，在一个页面中，不随URL的改变而改变。

> 示例代码:

```js
window.name = 'string' // 字符串，一般允许的最大值为2M
console.log(window.name)
location = 'http://funteas.com/'
```

　　此时，在控制台输入 window.name，结果依然是”string”

```js
window.name // "string"
```

　　window.name 的值只能是字符串，任何其他类型的值都会“转化”为字符串，例如：

```js
window.name = function(){}
console.log(window.name)
// "function(){}"
```

　　通过 window.name 实现跨域也很简单，iframe 拥有 contentWindow 属性，其指向该 iframe 的 window 对象的引用，如果在 iframe 的 src 指向的页面中设置 window.name 值，那么就可以通过 iframe.contentWindow.name 就可以拿到这个值了。

```js
var url = "http://funteas.com/lab/windowName";
var iframe = document.createElement('iframe')
iframe.onload = function(){
    var data = iframe.contentWindow.name
    console.log(data)
}
iframe.src = url
document.body.appendChild(iframe)
```

　　然而，chrome 会提示你跨域了！而我们已经知道 window.name 不随 URL 的改变而改版，也就是说，onload 时，已经获取到了 name，只不过因为不同源，当前页面的脚本无法拿到 iframe.contentWindow.name，此时只需要把 iframe.src 改为同源即可。

```js
var url = "http://funteas.com/lab/windowName";
var iframe = document.createElement('iframe')
iframe.onload = function(){
    iframe.src = 'favicon.ico';
    var data = iframe.contentWindow.name
    console.log(data)
}
iframe.src = url
document.body.appendChild(iframe)
```

　　刷新页面，你会发现 iframe 不断刷新，这是因为每次 onload，iframe 的 src 被修改，然后再次触发 onload，从而导致iframe 循环刷新，修改下即可：

```js
var url = "http://funteas.com/lab/windowName";
var iframe = document.createElement('iframe')
var state = true;
iframe.onload = function(){
    if(state === true){
        iframe.src = 'favicon.ico';
        state = false;
    }else if(state === false){
        state = null
        var data = iframe.contentWindow.name
        console.log(data)
    }
}
iframe.src = url
document.body.appendChild(iframe)
```

　　上面请求的是一个静态页面，而服务端通常需要的是动态数据：

```java
echo '<script> window.name = "{\"name\":\"story\"}"</script>';
```

## 4. postMessage

　　postMessage 允许不同源之间的脚本进行通信，用法：

```js
otherWindow.postMessage(message, targetOrigin);
```

- otherWindow 引用窗口 iframe.contentwindow 或 window.open返回的对象
- message 为要传递的数据
- targetOrigin 为目标源

```js
// http://127.0.0.1:80
var iframe = document.createElement('iframe')
iframe.onload = function(){
    var popup = iframe.contentWindow
    popup.postMessage("hello", "http://127.0.0.1:5000");
}
iframe.src = 'http://127.0.0.1:5000/lab/postMessage'
document.body.appendChild(iframe)
// 监听返回的postMessage
window.addEventListener("message", function(event){
    if (event.origin !== "http://127.0.0.1:5000") return;
    console.log(event.data)
}, false)
// http://127.0.0.1:5000/lab/postMessage
window.addEventListener("message", function(event){
    // 验证消息来源
    if (event.origin !== "http://127.0.0.1") return;
    console.log(event.source); // 消息源 popup
    console.log(event.origin); // 消息源URI https://secure.example.net 
    console.log(event.data); // 来自消息源的数据 hello
    // 返回数据
    var message = 'world';
    event.source.postMessage(message, event.origin);
}, false);
```

## 5. CORS

　　CORS（跨域资源共享）是一种跨域访问的机制，可以让 AJAX 实现跨域访问。它允许一个域上的脚本向另一个域提交跨域 AJAX 请求。实现此功能非常简单，只需由服务器发送一个响应标头即可。

```js
Access-Control-Allow-Origin: * // 允许来自任何域的请求
Access-Control-Allow-Origin: http://funteas.com/ // 仅允许来自http://funteas.com/的请求
```

　　当客户端的 ajax 请求的 url 为其他域时，对于支持 CORS 的浏览器，请求头会自动添加 Origin，值为当前 host。

```js
var xhr = new XMLHttpRequest();
var url = 'http://bar.other/resources/public-data/';
xhr.open('GET', url, true);
xhr.send();
```

　　CORS 默认不发送 cookie，如果要发送 cookie，需要设置 withCredentials：

```js
var xhr = new XMLHttpRequest();
xhr.withCredentials = true;
```

　　同时，服务端也要设置：

```js
Access-Control-Allow-Credentials: true
```

　　**详细可查看MDN [关于CORS的介绍](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)。**

<hr>