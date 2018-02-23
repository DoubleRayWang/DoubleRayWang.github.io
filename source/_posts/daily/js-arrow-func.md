---
layout: post
title: "JavaScript 箭头函数语法小结"
date: 2018-02-23 09:41:15 +0800
categories: 知识点
tags: [JavaScript,ES6]
---

箭头函数的确与传统函数有不同之处，但仍存在共同的特点。

例如：

1. 对箭头函数进行typeof操作会返回“function”。
2. 箭头函数仍是Function的实例，故而instanceof的执行方式与传统函数一致。
3. call/apply/bind方法仍适用于箭头函数，但就算调用这些方法扩充当前作用域，this也依旧不会变化。
4. 箭头函数与传统函数最大的不同之处在，禁用new操作
<!-- more -->

### 没有参数时

```js
var demo = function () {}
```

相当于：

```js
var demo = () => {}
```

### 只有一个参数时

```js
var demo = function(a){   
    return a;
}
```

相当于：

```js
var demo = a => a
```

### 多个参数需要用到小括号，参数间逗号间隔

```js
var demo = function(a,b){
    return a+b;
}
```

相当于：

```js
var demo = (a,b) => a+b
```

### 函数体多条语句需要用到大括号

```js
var demo = function(a,b){
if(a>b){
    return a-b;
} else{
    return b-a;
  }
}
```

相当于：

```js
var demo = (a,b) =>{
if(a>b){
    return a-b;
} else{
    return b-a;
  }
}
```

### 返回对象时需要用小括号包起来，因为大括号被占用解释为代码块了

```js
var demo = (name,age) =>{
return ({
    name: name,
    age: age
   })
}
```

### 作为数组排序回调

```js
var arr = [1, 9 , 2, 4, 3, 8].sort((a, b) => {
 if (a - b > 0 ) {
  return 1
 } else {
  return -1
 }
})
```