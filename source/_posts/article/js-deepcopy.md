---
layout: post
title: "JavaScript 深拷贝性能分析"
date: 2018-01-21 14:41:15 +0800
categories: 实践之路
tags: JavaScript
---

> 原文：[Deep-copying in JavaScript - DasSur.ma](https://dassur.ma/things/deep-copy/)

如何在 JavaScript 中拷贝一个对象？对于这个很简单的问题，但是答案却不简单。<!-- more -->

## 引用传值

先看下面的例子：

```js
function mutate(obj) {
  obj.a = true;
}

const obj = {a: false};
mutate(obj)
console.log(obj.a); // 输出 true
```

函数 mutate 改变了它的参数。在值传递的场景中，函数的形参只是实参的一个副本——a copy——当函数调用完成后，并不改变实参。但是在 JavaScript 这种引用传递的场景中，函数的形参和实参指向同一个对象，当参数内部改变形参的时候，函数外面的实参也被改变了。

因此在某些情况下，你需要保留原始对象，这时你需要把原始对象的一个拷贝传入到函数中，以防止函数改变原始对象。

## 浅拷贝：Object.assign()

一个简单的获取对象拷贝的方式是使用 Object.assign(target, sources...)。它接受任意数量的源对象，枚举它们的所有属性并分配给target。如果我们使用一个新的空对象target，那么我们就可以实现对象的复制。

```js
const obj ='' /* ... */;
const copy = Object.assign({}, obj); 
```

然而这只是一个浅副本。如果我们的对象包含其它对象作为自己的属性，它们将保持共享引用，这不是我们想要的：

```js
function mutateDeepObject(obj) {
  obj.a.thing = true;
}

const obj = {a: {thing: false}};
const copy = Object.assign({}, obj);
mutateDeepObject(copy)
console.log(obj.a.thing); // prints true 
```

> Object.assign 方法只会拷贝源对象自身的并且可枚举的属性到目标对象。该方法使用源对象的[[Get]]和目标对象的[[Set]]，所以它会调用相关 getter 和 setter。因此，它分配属性，而不仅仅是复制或定义新的属性。如果合并源包含getter，这可能使其不适合将新属性合并到原型中。为了将属性定义（包括其可枚举性）复制到原型，应使用[Object.getOwnPropertyDescriptor()](https://link.funteas.com?target=http%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%253A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FObject%2FgetOwnPropertyDescriptor)和[Object.defineProperty()](https://link.funteas.com?target=http%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%253A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FObject%2FdefineProperty) 。

所以现在怎么办？有几种方法可以创建一个对象的深拷贝。

注意：也许有人提到了[对象解构运算](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)，这也是浅拷贝。

## JSON.parse

创建对象副本的最古老方法之一是：

将该对象转换为其 JSON 字符串表示形式，然后将其解析回对象。这感觉有点压抑，但它确实有效：

```js
const obj = ''/* ... */;
const copy = JSON.parse(JSON.stringify(obj));
```

这里的缺点是你创建一个临时的，可能很大的字符串，只是为了把它重新放回解析器。另一个缺点是这种方法不能处理循环对象。而且循环对象经常发生。例如，当您构建树状数据结构，其中一个节点引用其父级，而父级又引用其子级。

```js
const x = {};
const y = {x};
x.y = y; // Cycle: x.y.x.y.x.y.x.y.x...
const copy = JSON.parse(JSON.stringify(x)); // throws!
```

另外，诸如 Maps, Sets, RegExps, Dates, ArrayBuffers 和其他内置类型在进行序列化时会丢失。

## Structured Clone 结构化克隆算法

[Structured cloning](https://html.spec.whatwg.org/multipage/structured-data.html#structuredserializeinternal) 是一种现有的算法，用于将值从一个地方转移到另一地方。例如，每当您调用postMessage将消息发送到另一个窗口或 [WebWorker](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) 时，都会使用它。关于结构化克隆的好处在于它处理循环对象并支持[大量的内置类型](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm#Supported_types)。问题是，在编写本文时，该算法并不能直接使用，只能作为其他 API 的一部分。

### MessageChannel

正如我所说的，只要你调用postMessage结构化克隆算法就可以使用。我们可以创建一个 [MessageChannel](https://developer.mozilla.org/en-US/docs/Web/API/MessageChannel/MessageChannel) 并发送消息。在接收端，消息包含我们原始数据对象的结构化克隆。

```JS
function structuralClone(obj) {
  return new Promise(resolve => {
    const {port1, port2} = new MessageChannel();
    port2.onmessage = ev => resolve(ev.data);
    port1.postMessage(obj);
  });
}

const obj = ''/* ... */;
const clone = await structuralClone(obj);
```

这种方法的缺点是它是**异步的**。虽然这并无大碍，但是有时候你需要使用同步的方式来深度拷贝一个对象。

### History API

如果你曾经使用`history.pushState()`写过 SPA，你就知道你可以提供一个状态对象来保存 URL。事实证明，这个状态对象使用结构化克隆 - 而且是同步的。我们必须小心使用，不要把程序逻辑使用的状态对象搞乱了，所以我们需要在完成克隆之后恢复原始状态。为了防止发生任何意外，请使用`history.replaceState()`而不是`history.pushState()`。

```js
function structuralClone(obj) {
  const oldState = history.state;
  history.replaceState(obj, document.title);
  const copy = history.state;
  history.replaceState(oldState, document.title);
  return copy;
}

const obj = ''/* ... */;
const clone = structuralClone(obj); 
```

然而，仅仅为了复制一个对象，而使用浏览器的引擎，感觉有点过分。另外，Safari 浏览器对replaceState调用的限制数量为 30 秒内 100 次。

### Notification API

第三种方法来利用结构化克隆：Notification API。

```js
function structuralClone(obj) {
  return new Notification('', {data: obj, silent: true}).data;
}

const obj = ''/* ... */;
const clone = structuralClone(obj);
```

短小，简洁。

但是，它需要浏览器内部的权限机制，所以我怀疑它是很慢的。由于某种原因，Safari 总是返回`undefined`。

## 结论

+ 如果您没有循环对象，并且不需要保留内置类型，则可以使用跨浏览器的`JSON.parse(JSON.stringify())`获得最快的克隆性能，这让我感到非常惊讶。
+ 如果你想要一个适当的结构化克隆，`MessageChannel`是你唯一可靠的跨浏览器的选择。

如果浏览器平台直接提供一个 structuredClone()函数，会不会更好？我当然这样认为，最新的 HTML 规范正在讨论这个 [Synchronous clone = global.structuredClone(value, transfer = []) API · Issue #793 · whatwg/html](https://github.com/whatwg/html/issues/793)。