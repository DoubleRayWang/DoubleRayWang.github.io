---
layout: post
title: "JavaScript 跳转页面"
date: 2018-05-06 12:41:15 +0800
categories: 知识点
tags: [JavaScript,ES6]
---

## 调试

```js
const a=6,b=9,c=0;
console.log({a,b,c});
//输出 {a:5,b:9,c:0}
```

## 结构Async/Await

```js
const [grade,fee] = await Promise.all([
    fetch('/grade'),
    fetch('/fee')
])
```

## 合并数据

```js
const arr_1 = ['a','b','c']
const arr_2 = [1,2,3]
const result = [...arr_1,...arr_2]
```

## 深拷贝对象和数组

```js
const object_1 = {
    a:5,
    b:6,
    c:7
},
arr_1 = [1,2,3]

const obj = {...object_1}
const arr = [...arr_1]

console.log(obj===object_1)  //false
console.log(arr===arr_1)  //false
```
