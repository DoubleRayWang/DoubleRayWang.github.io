---
layout: post
title: "原生 JavaScript 实现 AJAX、JSONP"
date: 2017-02-14 13:15:15 +0800
categories: 学习笔记
tags: [Ajax,jsonp]
---

　　**本文转自 [TG](http://ghmagical.com/) 的 [原生 JavaScript 实现 AJAX、JSONP](http://ghmagical.com/article/page/id/AASiankfBJWp)。**

　　相信大多数前端开发者在需要与后端进行数据交互时，为了方便快捷，都会选择`JQuery`中封装的AJAX方法，但是有些时候，我们只需要`JQuery`的`AJAX`请求方法，而其他的功能用到的很少，这显然是没必要的。

　　其实，原生`JavaScript`实现`AJAX`并不难，这篇文章将会讲解如何实现简单的`AJAX`，还有跨域请求`JSONP`！<!-- more -->


## 1. AJAX

一个完整的AJAX请求一般包括以下步骤：

- 实例化XMLHttpRequest对象
- 连接服务器
- 发送请求
- 接收响应数据

我将`AJAX`请求封装成`ajax()`方法，它接受一个配置对象`params`。

```js
function ajax(params) {
    params = params || {};
    params.data = params.data || {};
    // 判断是ajax请求还是jsonp请求
    var json = params.jsonp ? jsonp(params) : json(params);
    // ajax请求   
    function json(params) {
            //  请求方式，默认是GET
            params.type = (params.type || 'GET').toUpperCase();
            // 避免有特殊字符，必须格式化传输数据  
            params.data = formatParams(params.data);
            var xhr = null;
            // 实例化XMLHttpRequest对象   
            if (window.XMLHttpRequest) {
                xhr = new XMLHttpRequest();
            } else {
                // IE6及其以下版本   
                xhr = new ActiveXObjcet('Microsoft.XMLHTTP');
            };
            // 监听事件，只要 readyState 的值变化，就会调用 readystatechange 事件 
            xhr.onreadystatechange = function() {
                //  readyState属性表示请求/响应过程的当前活动阶段，4为完成，已经接收到全部响应数据
                if (xhr.readyState == 4) {
                    var status = xhr.status;
                    //  status：响应的HTTP状态码，以2开头的都是成功
                    if (status >= 200 && status < 300) {
                        var response = '';
                        // 判断接受数据的内容类型  
                        var type = xhr.getResponseHeader('Content-type');
                        if (type.indexOf('xml') !== -1 && xhr.responseXML) {
                            response = xhr.responseXML; //Document对象响应   
                        } else if (type === 'application/json') {
                            response = JSON.parse(xhr.responseText); //JSON响应   
                        } else {
                            response = xhr.responseText; //字符串响应   
                        };
                        // 成功回调函数 
                        params.success && params.success(response);
                    } else {
                        params.error && params.error(status);
                    }
                };
            };
            // 连接和传输数据   
            if (params.type == 'GET') {
                // 三个参数：请求方式、请求地址(get方式时，传输数据是加在地址后的)、是否异步请求(同步请求的情况极少)；
                xhr.open(params.type, params.url + '?' + params.data, true);
                xhr.send(null);
            } else {
                xhr.open(params.type, params.url, true);
                //必须，设置提交时的内容类型   
                xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded; charset=UTF-8');
                // 传输数据  
                xhr.send(params.data);
            }
        }
        //格式化参数   
    function formatParams(data) {
            var arr = [];
            for (var name in data) {
                //   encodeURIComponent() ：用于对 URI 中的某一部分进行编码
                arr.push(encodeURIComponent(name) + '=' + encodeURIComponent(data[name]));
            };
            // 添加一个随机数参数，防止缓存   
            arr.push('v=' + random());
            return arr.join('&');
        }
        // 获取随机数   
    function random() {
        return Math.floor(Math.random() * 10000 + 500);
    }
}
```

　　_在上面的代码中，已经添加具体的注释，如需了解更多`AJAX`，可查看 [TG](http://ghmagical.com/) 的书籍《[JavaScript半知半解](http://www.kancloud.cn/dennis/tgjavascript)》中的`AJAX`章节：[AJAX](http://www.kancloud.cn/dennis/tgjavascript/241825)。_

使用实例：

```js
ajax({   
    url: 'test.php',   // 请求地址
    type: 'POST',   // 请求类型，默认"GET"，还可以是"POST"
    data: {'b': '异步请求'},   // 传输数据
    success: function(res){   // 请求成功的回调函数
    console.log(JSON.parse(res));   
  },
    error: function(error) {}   // 请求失败的回调函数
});
```

## 2. JSONP

> **同源策略**

　　`AJAX`之所以需要“跨域”，罪魁祸首就是浏览器的同源策略。即，一个页面的`AJAX`只能获取这个页面相同源或者相同域的数据。 如何叫“同源”或者“同域”呢？——_协议、域名、端口号都必须相同_。例如：

```
http://example.com 和 https://example.com 不同，因为协议不同；   
http://localhost:8080 和 http://localhost:1000 不同，因为端口不同；   
http://localhost:8080 和 https://example.com 不同，协议、域名、端口号都不同，根本不是一家的。
```

　　当跨域请求时，一般都会看到这个错误：

```
XMLHttpRequest cannot load http://ghmagical.com/article/?intro=jsonp%E8%AF%B7%E6%B1%82&v=5520. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost' is therefore not allowed access.
```

　　那如何跨域请求呢？这时，**JSONP**就登场了！

　　**JSONP(JSON with Padding)** 是一种跨域请求方式。主要原理是利用了`script` 标签可以跨域请求的特性，由其 `src` 属性发送请求到服务器，服务器返回 `JavaScript` 代码，浏览器接受响应，然后就直接执行了，这和通过 `script` 标签引用外部文件的原理是一样的。

　　**JSONP**由两部分组成：回调函数和数据，回调函数一般是在浏览器控制，作为参数发往服务器端（当然，你也可以固定回调函数的名字，但客户端和服务器端的名称一定要一致）。当服务器响应时，服务器端就会把该函数和数据拼成字符串返回。 
　　JSONP的请求过程：

- 请求阶段：浏览器创建一个 script 标签，并给其src 赋值(类似 http://example.com/api/?callback=jsonpCallback）。
- 发送请求：当给`script`的`src`赋值时，浏览器就会发起一个请求。
- 数据响应：服务端将要返回的数据作为参数和函数名称拼接在一起(格式类似”`jsonpCallback({name: 'abc'})`”)返回。当浏览器接收到了响应数据，由于发起请求的是 `script`，所以相当于直接调用 `jsonpCallback` 方法，并且传入了一个参数。

> **用原生JavaScript如何实现**

　　依旧是`ajax()`方法里添加`JSONP`，后面会将两者整合在一起，`JSONP`的配置参数主要多了一个`jsonp`参数，它就是你的回调函数名。

```js
function ajax(params) {
    params = params || {};
    params.data = params.data || {};
    var json = params.jsonp ? jsonp(params) : json(params);
    // jsonp请求   
    function jsonp(params) {
        //创建script标签并加入到页面中   
        var callbackName = params.jsonp;
        var head = document.getElementsByTagName('head')[0];
        // 设置传递给后台的回调参数名   
        params.data['callback'] = callbackName;
        var data = formatParams(params.data);
        var script = document.createElement('script');
        head.appendChild(script);
        //创建jsonp回调函数   
        window[callbackName] = function(json) {
            head.removeChild(script);
            clearTimeout(script.timer);
            window[callbackName] = null;
            params.success && params.success(json);
        };
        //发送请求   
        script.src = params.url + '?' + data;
        //为了得知此次请求是否成功，设置超时处理   
        if (params.time) {
            script.timer = setTimeout(function() {
                window[callbackName] = null;
                head.removeChild(script);
                params.error && params.error({
                    message: '超时'
                });
            }, time);
        }
    };
    //格式化参数   
    function formatParams(data) {
        var arr = [];
        for (var name in data) {
            arr.push(encodeURIComponent(name) + '=' + encodeURIComponent(data[name]));
        };
        // 添加一个随机数，防止缓存   
        arr.push('v=' + random());
        return arr.join('&');
    }
    // 获取随机数   
    function random() {
        return Math.floor(Math.random() * 10000 + 500);
    }
}
```

**`注意：`**因为 script 标签的 src 属性只在第一次设置的时候起作用，导致 script 标签没法重用，所以每次完成操作之后要移除；

使用实例：

```js
ajax({   

  url: 'test.php',    // 请求地址

  jsonp: 'jsonpCallback',  // 采用jsonp请求，且回调函数名为"jsonpCallbak"，可以设置为合法的字符串

  data: {'b': '异步请求'},   // 传输数据

  success:function(res){   // 请求成功的回调函数

    console.log(res);   

  },

  error: function(error) {}   // 请求失败的回调函数

});
```

**`注意：`**别漏了用函数名与数据拼接返回。

当然，前面也说过，你可以给定固定回调函数名：

```php
function jsonpCallback() {}
<?php 
  echo 'jsonpCallback('.$data.')';
```

最后我已经将`AJAX`和`JSONP`请求合并在一起了，下载链接：[ajax.zip](/download/ajax.zip)。

<hr>