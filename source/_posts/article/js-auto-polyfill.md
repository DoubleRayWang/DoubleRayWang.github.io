---
layout: post
title: "Auto Polyfill"
date: 2019-11-14 13:13:05 +0800
categories: 实践之路
tags: [JavaScript,Polyfill]
---

## 什么是补丁？

> A polyfill, or polyfiller, is a piece of code (or plugin) that provides the technology that you, the developer, expect the browser to provide natively. Flattening the API landscape if you will.

我们希望浏览器提供一些特性，但是没有，然后我们自己写一段代码来实现他，那这段代码就是补丁。<!-- more -->

-------------

如果你是一个 3 年陈 + 的前端，应该会有听说过 shim、sham、es5-shim 和 es6-shim 等等现在看起来很古老的补丁方式。

那么，shim 和 sham 是啥？又有什么区别？

+ shim 是能用的补丁
+ sham 顾名思义，是假的意思，所以 sham 是一些假的方法，只能使用保证不出错，但不能用。至于为啥会有 sham，因为有些方法的低端浏览器里根本实现不了

-----------------

在 shim 和 sham 之后，还有一种补丁方式是引入包含所有语言层补丁的 babel-polyfill.js。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-polyfill/7.2.5/polyfill.js"></script>
```

-----------------

@babel/preset-env + useBuiltins: entry + targets

babel-polyfill 包含所有补丁，那我只需要支持某些浏览器的某些版本，是否有办法只包含这些浏览器的补丁？这就是 @babel/preset-env + useBuiltins: entry + targets 配置的方案。

我们先在入口文件里引入 @babel/polyfill，

```js
import '@babel/polyfill';
```

然后配置 .babelrc，添加 preset @babel/preset-env，并设置 useBuiltIns 和 targets，

```json
{
  "presets": [
    ["@babel/env", {
      useBuiltIns: 'entry',
      targets: { chrome: 62 }
    }]
  ]
}
```

useBuiltIns: entry 的含义是找到入口文件里引入的 @babel/polyfill，并替换为 targets 浏览器/环境需要的补丁列表。

替换后的内容，比如：

```js
import "core-js/modules/es7.string.pad-start";
import "core-js/modules/es7.string.pad-end";
//...
```

这样就只会引入 chrome@62 及以上所需要的补丁，什么 Promise 之类的都不会再打包引入。

这种方案存在的问题：

+ 特性列表是按浏览器整理的，那怎么知道哪些特性我用了，哪些没有用到，没有用到的部分也引入了是不是也是冗余？@babel/preset-env 有提供 exclude 的配置，如果我配置了 exclude，后面是否得小心翼翼地确保不要用到 exclude 掉的特性
+ 补丁是打包到静态文件的，如果我配置 targets 为 chrome: 62, ie: 9，那意味着 chrome 62 也得载入 ie 9 相关的补丁，这也是一份冗余
+ 我们是基于 core-js 打的补丁，所以只会包含 ecmascript 规范里的内容，其他比如说 dom 里的补丁，就不在此列，应该如何处理？

## 在线补丁，比如：polyfill.io

目前最流行的应该就是 polyfill.io，提供的是 cdn 服务，有些站点在用，例如 https://spectrum.chat/。另外，polyfill.io 还开源了 polyfill-service 供我们自己搭建使用。

使用上，比如：

```html
<script src="https://polyfill.io/v3/polyfill.min.js?features=default%2CPromise"></script>
<script src="https://polyfill.alicdn.com/polyfill.min.js?features=default,es5,es6,es7,RegeneratorRuntime"></script>
```

然后在 Chrome@71 下的输出是：

/* Disable minification (remove `.min` from URL path) for more info */
啥都没有，因为 Promsie 特性 Chrome@71 已经支持了。
