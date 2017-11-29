---
layout: post
title: "理解 Web 缓存"
date: 2017-01-29 15:41:15 +0800
categories: 学习笔记
tags: Web
---


　　提到 `Web` 缓存大家都会觉得很简单，不就是检查资源是否有缓存，如果有就加以利用。如果追究下去，多数人还能扯出 `cache-control, last-modified, etag `之类的名词，但如果真的考究一下这些字段之间有什么区别，又是怎么工作的，其实很多人只有一个极为模糊的认识，所以写一篇文章来梳理一下 `Web` 缓存的工作方式。<!-- more -->

> **缓存不仅仅是浏览器的事情**

　　很多人对 Web 缓存的认识就是浏览器判断是否使用缓存，但缓存不是浏览器本身能够完成的事情，因为在没有服务器端的其他信息的情况下浏览器是无法判断一个资源是否过期的。代理缓存，CDN 缓存等不在本文的讨论范围内。

## Headers

　　因为针对缓存的控制需要浏览器和服务器端协同完成，所以他们需要一个传递的信息的方式，事实上目前的 Web 缓存主要通过 `Headers` 来传递信息。

- **以知乎的一张图片资源为例**

　　随便以知乎的一张图片资源为例，我们可以打开 `Chrome` 的开发者工具中的 `network`，随便找一个 200(from cache) 的资源，可以看到请求报文和响应报文的 `Headers`。

<img src="/styles/images/web/cache_1.png" alt="Headers" />

### cache-control 和 max-age

　　`cache-control` 大概是最广为人知的控制缓存的 `Headers` 了，这也是最简单的缓存控制策略，即浏览器通过最大生存时间来判断资源的缓存是否有效。

　　如图所见，来自服务器端的 `response headers` 的 `Headers` 中有 `cache-control:public, max-age=31536000`，这就是告诉浏览器这个资源的生存时间，在这个时间以内，浏览器不需要向服务器端再做任何确认，直接使用即可。下面我们也可以看到 `request headers` 一栏是空的。因为浏览器根本没有发出请求，这里显示的 `response headers` 是之前的请求中缓存的。

　　`cache-control` 除了 `max-age` 外还有一些其他的参数，本文暂不阐述。

### expires

　　我们注意到在缓存的 response headers 里还有一个 Expires:Mon, 29 Jan 2018 07:38:47 GMT 字段。这个字段的意义实际上和 cache-control: max-age 的效果是相似的，在指定的时间之前浏览器都可以认为缓存是有效的。但当两个字段同时存在时，expires 会被 cache-control 覆盖。

　　那么为什么知乎要同时设置两个字段呢？由于 expires 是 HTTP/1.0 定义的而 cache-control 是 HTTP/1.1 定义的，我猜测可能是为了保持尽可能大的兼容性（待考证）。

### 304 Not Modified

　　上面的缓存策略只能很简单的让浏览器来确定缓存是否有效，而浏览器能够依赖的只有上次请求时服务器端留给它的资源存活时间。我们不能把存活时间设成永远，因为可能什么时候我们会更新资源，但隔一段时间重新请求一次并没有改变的资源同样浪费带宽。所以我们必须要有让服务器告诉浏览器缓存仍然有效的方法，那便是 304 Not Modified。

　　在服务器端判断缓存仍然有效时将会返回状态码 304 的响应。

　　那么服务器如何判断浏览器持有的缓存是否有效呢？这就需要浏览器将一些信息传递给服务器。

### If-None-Match/ETag

　　知乎采用的是 ETag 来判断缓存是否有效，服务器端会在 response headers 中返回 ETag（文件的 hash）：

```
etag:"Ft-ifpb80DCLRrATs5py8dg84mtf"
```

　　而当资源改变时 ETag 也会发生改变。浏览器在发起请求时在 If-None-Match字段携带缓存的 ETag：

```
If-None-Match:"2afd9676ae9046ed99dedd4635bb6e4a-gzip"
```

　　服务器接到请求后如果一致（即资源没有修改），则返回 304 Not Modified，否则返回新的资源（200）。

### If-Modified-Since/Last-Modified

　　除了文件特征码之外也可以通过上次修改时间，服务器端返回资源时通过 Last-Modified 携带资源修改时间，浏览器通过 If-Modified-Since 携带缓存中的资源的修改时间。

### ETag 和 Last-Modified 的区别

　　Last-Modified 有个缺点就是它是精确到秒的，如果一秒中资源多次服务器不会感知到缓存失效，但这不是一个常见的需求。

　　而一般不推荐使用 ETag，原因有几点：

- Last-Modified 的缺点基本可以忽略不计
- ETag 本身需要消耗 CPU，而它的优先级比 Last-Modified 高，当它存在时服务器无论 Last-Modified 是否存在都会使用它判断
- ETag 在分布式系统中生成的值可能不一样，会导致缓存失效

### 一图胜千言

<img src="/styles/images/web/cache_2.png" alt="Cache" />

## 总结

　　大公司的静态资源优化方案，基本上要实现这么几个东西：

1. 配置超长时间的本地缓存 —— 节省带宽，提高性能

2. 版本控制 —— 精确的缓存控制

3. 静态资源 **CDN部署** —— 优化网络请求

4. 更新资源发布路径实现 **非覆盖式发布** —— 平滑升级

<br>

　　*本文参考自 [M-x codefalling 的 理解 Web 缓存](https://codefalling.com/2017/01/understand-web-cache) 和 [SimplyY 的 浏览器缓存介绍精简版](http://simplyy.space/article/57e8f1777a73cae6287343d0)*。

<hr>