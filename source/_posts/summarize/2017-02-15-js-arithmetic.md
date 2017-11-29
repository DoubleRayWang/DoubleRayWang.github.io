---
layout: post
title: "JavaScript 常用算法总结"
date: 2017-02-15 14:41:15 +0800
categories: 学习笔记
tags: JavaScript
---

　　算法通俗的讲：就是解决问题的方法和步骤。在计算机发明之前便已经存在。只不过在计算机发明后，其应用变得更为广泛。通过简单的算法，利用电脑的计算速度，可以让问题变得简单。在前端运算中，算法也是非常重要的。记不清是谁说过“现在的前端已经不是选择器和超链接就能应付的了“，说得一点都没错。在移动端和大数据飞速发展的今天，性能已经是所有工程师所必须重视的部分。作为用户交互的先锋队，前端工程师的技术含量也愈发增加，随着用户体验的不断发展，页面交互的自由度及性能使我们必须深入设计的部分。

　　作为前端工程师，我们的工作已经不仅仅是完成UI的静态设计稿，还需要设计页面的呈现顺序（惰性加载），功能模块化，设计交互（页面滑动，3D效果等），设计网页游戏等，这时候就需要我们对算法和设计模式的研究。<!-- more -->

## 1. 99乘法表

```js
for(var i = 1; i <= 9; i++) {
	for(var n = 1; n <= i; n++) {
		document.write(n+'*'+i+'='+i*n+'\t');
	}
	document.write('<br/>')
}//9*9乘法表
```

## 2. 斐波那契数列

```js
function fib(n) {
    if (n>2) {
        return fib(n - 2) + fib(n - 1);
    } else {
        return 1
    }
}
alert(fib(6));//递归
//生成斐波那契数列
function getFibonacci(n) {
    var fibarr = [];
    var i = 0;
    while (i < n) {
        if (i <= 1) {
            fibarr.push(i);
        } else {
            fibarr.push(fibarr[i - 1] + fibarr[i - 2])
        }
        i++;
    }
    return fibarr;
}
alert(getFibonacci(6))
```

## 3. 回文字符串判断

```js
function palindrome(str) {
    // 删除字符串中不必要的字符
    var re = /[W_]/g;
    // 将字符串变成小写字符
    var lowRegStr = str.toLowerCase().replace(re, '');
    // 如果字符串lowRegStr的length长度为0时，字符串即是palindrome
    if (lowRegStr.length === 0) {
        return true;
    }
    // 如果字符串的第一个和最后一个字符不相同，那么字符串就不是palindrome
    if (lowRegStr[0] !== lowRegStr[lowRegStr.length - 1]) {
        return false;
    } else {
        return palindrome(lowRegStr.slice(1, lowRegStr.length - 1));
    }
}
alert(palindrome('asddfa'))
```

## 4. 获取任意长度的随机字符串

```js
var getRandomString = function(n){
    // 定义随机字符串的字符库
    var str = 'qwertyuiopasdfghjklzxcvbnm1234567890';
    // 定义一个临时变量tmp存储生成的随机字符串
    var tmp = '';
    //获取str的长度
    var len = str.length;
    // 生成一个长度为n的随机字符串
    for(var i = 0; i < n; i++){
        tmp += str.charAt(Math.floor(Math.random() * len));
    }
    return tmp;
};
alert(getRandomString(10))
```

## 5. 阶乘算法

```js
function factorialize(num) {
    var result = num;
    if (num < 0) {
        return -1;
    } else if (num === 0 || num === 1) {
        return 1;
    } else {
        while (num > 1) {
            num--;
            result *= num;
        }
    }
    return result;
}
alert(factorialize(5));
```

## 6. 数组排序

### 6.1 快速排序

- 在数据集之中，选择一个元素作为"基准"（pivot）。
- 所有小于"基准"的元素，都移到"基准"的左边；所有大于"基准"的元素，都移到"基准"的右边。
- 对"基准"左边和右边的两个子集，不断重复第一步和第二步，直到所有子集只剩下一个元素为止。

> 快排的时间复杂度是O(nlogn)，属于不稳定的排序!

```js
function quickSort(arr){
    //如果数组<=1,则直接返回
    if(arr.length<=1){return arr;}
    var pivotIndex=Math.floor(arr.length/2);
    //找基准，并把基准从原数组删除
    var pivot=arr.splice(pivotIndex,1)[0];
    //定义左右数组
    var left=[];
    var right=[];

    //比基准小的放在left，比基准大的放在right
    for(var i=0;i<arr.length;i++){
        if(arr[i]<=pivot){
            left.push(arr[i]);
        }
        else{
            right.push(arr[i]);
        }
    }
    //递归
    return quickSort(left).concat([pivot],quickSort(right));
}
var arr=[3,1,5,2,6];
alert(quickSort(arr))
```

### 6.2. 冒泡排序

　　冒泡排序分两种，一种小泡泡往上吐，一种大泡泡往上吐。也就是从小到大和从大到小。

> 具体步骤:

　　冒泡排序就是从最开始的位置或结尾的位置反方向对比，如果比它大/小,就交换然后继续走，第一遍走完,最后一个位置是最大值或者最小值。

```js
function bubbleSort(arr){
    for(var i=1;i<arr.length;i++){
        for(var j=0;j<arr.length-i;j++){
            var temp;
            if(arr[j]>arr[j+1]){
                temp=arr[j];
                arr[j]=arr[j+1];
                arr[j+1]=temp;
            }
        }
    }
    return arr;
}
var arr=[2,3,1,4,11,58,45];
alert(bubbleSort(arr));

//冒泡排序逻辑

var array=[1,20,4,18,7,3];
var temp=0;
document.writeln("排序前:"+array+"</br>");
for(var i=0;i<array.length-1;i++)
{
    for(var j=i+1;j<array.length;j++)
    {
        if(array[i]>array[j])
        {
            temp=array[i];
            array[i]=array[j];
            array[j]=temp;
        }
    }
    var m=i+1;
    document.write("第"+m+"次排序:"+array+"</br>");
}
document.writeln("排序后:"+array+"</br>");

document.write(arr);
```

### 6.3 插入排序

```js
//插入排序
Array.prototype.insertSort=function(){
   var len = this.length,
        i, j, tmp;
    for(i=1; i<len; i++){
        tmp = this[i];
        j = i - 1;
	//初始时数组索引值为1的与为0的比较，如果索引值为1的小于则将其值换为索引值为0的
        while(j>=0 && tmp < this[j]){
            this[j+1] = this[j];
            j--;
        }
	//随着j--循环的次，则找到tmp的前面有序区应该的位置。
        this[j+1] = tmp;
    }
    return this;
}
```

> 关于更多的排序知识，可移步 [这里](https://zhuanlan.zhihu.com/p/25379917)。

## 7. 求一个数组的最大差值

```js
var getMaxProfit = function(arr){
    // 定义两个变量，分别存贮最大值和最小值
    var maxNum = arr[0];
    var minNum = arr[0];
    for(var i = 0; i < arr.length; i++){
        if(arr[i] > maxNum){
            maxNum = arr[i];
        }
        if(arr[i] < minNum){
            minNum = arr[i];
        }
    }
    return maxNum - minNum;
};
var arr=[1,2,3,4,5,6];
alert(getMaxProfit(arr))
```

## 8. 数组的偶数位删除

```js
var arr=[];
for(var i=1;i<=100;i++){
    arr.push(i);
}
do{
    for(var a=arr.length;a>0;a--){
        if(a%2==1){
            arr.splice(a,1);
        }
    }
}while (arr.length>2);
alert(arr);
```

## 9. 数组去重

### 9.1 利用 key 去重

```js
function unique(arr){
    var hashTable = {};
    var data = [];
    for(var i = 0, l = arr.length; i < l; i++) {
        if(!hashTable[arr[i]]) {
            hashTable[arr[i]] = true;
            data.push(arr[i]);
        }
    }
    return data;
}
var arr=[2,3,4,5,2,3,6,1,6];
alert(unique(arr))
```

### 9.2 逻辑去重

```js
var arr=[21,32,45,32,21,56,45];
function fn(arr) {
    var temp=[];
    for(var i=0;i<arr.length;i++){
        if(temp.indexOf(arr[i])==-1){
            temp.push(arr[i]);
        }
    }
    return temp;
}
var a=fn(arr);//接收返回值
document.write(a);
```

### 9.3 传统 key 方法

```js
var arr=[2,3,1,4,2,1,4,12,12,3,5,6,5];
function toObject(arr){
    var obj={};
    for(var i=0;i<arr.length;i++){
        obj[arr[i]]=true;
    }
    return obj;
}
function toArr(obj){
    var arr=[];
    for(var attr in obj){
        arr.push(attr);
    }
    return arr;
}
function newArr(Arr){
    return toArr(toObject(Arr))
}
alert(newArr(arr));
```

## 10. 水仙花数及四叶玫瑰数

```js
for(var i = 100; i <= 999; i++) {
    var g, s, b;
    g = i % 10;
    s = parseInt(i / 10) % 10;
    b = parseInt(i / 100);
    var sum1 = g * g * g + s * s * s + b * b * b;
    if(i == sum1) {
        document.write(i + '<br/>');
    };
} //水仙花数

for(var i = 1000; i <= 9999; i++) {
    var g, s, b, q;
    g = i % 10;
    s = parseInt(i / 10) % 10;
    b = parseInt(i / 100) % 10;
    q = parseInt(i / 1000);
    var sum2 = g * g * g * g + s * s * s * s + b * b * b * b + q * q * q * q;
    if(i == sum2) {
        document.write(i + '<br/>');
    };
} //四叶玫瑰数
```

## 11. 交换两个值为数字的两个变量的值

```js
var swap = function(a,b){
    if(a === b){
        return [a,b];
    }
    b = b - a; // 此处的 b - a中的b和a的值是最初的值
    a = a + b; // a = a + b -a; 实现了将b的值赋给a
    b = a - b; // b = a - (b - a) = 2a - b 相当于 2b = 2a;实现了将a的值赋给b
    return [a,b];
};
alert(swap(3,6))
```

<hr>