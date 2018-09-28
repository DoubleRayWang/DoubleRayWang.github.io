---
layout: post
title: "自用正则手册"
date: 2018-06-06 13:13:05 +0800
categories: 干货分享
tags: [JavaScript]
---

正则表达式regex是用于匹配字符串中字符组合的模式，由参数pattern + 标志flags构成。

> 参考学习 [MDN：正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)

> 参考学习 [阮一峰：ES6正则的扩展](http://es6.ruanyifeng.com/#docs/regex)

> 参考学习 [菜鸟教程：正则表达式](http://www.runoob.com/regexp/regexp-syntax.html)

<!-- more -->

## 参数

### 普通字符

指所有字母、数字、符号等

### 非打印字符

指换行、回车、空白等不会实际显示出来的字符

| 字符 | 说明 |
| :--- | :--- |
| \cx | 匹配由x指明的控制字符。例如， \cM 匹配一个 Control-M 或回车符。x 的值必须为 A-Z 或 a-z 之一。否则，将 c 视为一个原义的 'c' 字符。 |
| \f | 匹配一个换页符。等价于 \x0c 和 \cL。 |
| \n | 匹配一个换行符。等价于 \x0a 和 \cJ。 |
| \r | 匹配一个回车符。等价于 \x0d 和 \cM。 |
| \s | 匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]。注意 Unicode 正则表达式会匹配全角空格符。 |
| \S | 匹配任何非空白字符。等价于 [^ \f\n\r\t\v]。 |
| \t | 匹配一个制表符。等价于 \x09 和 \cI。 |
| \v | 匹配一个垂直制表符。等价于 \x0b 和 \cK。 |

### 限定符

指定匹配前面的子表达式必须要出现多少次才能满足

| 字符 | 说明 |
| :--- | :--- |
| * | 	零次或多次。例如，zo* 能匹配 "z" 以及 "zoo"。* 等价于{0,}。 |
| + | 	一次或多次。例如，'zo+' 能匹配 "zo" 以及 "zoo"，但不能匹配 "z"。+ 等价于 {1,}。 |
| ? | 	零次或一次。例如，"do(es)?" 可以匹配 "do" 或 "does" 。? 等价于 {0,1}。 |
| {n} | 	n >= 0。匹配确定的 n 次。例如，'o{2}' 不能匹配 "Bob" 中的 'o'，但是能匹配 "food" 中的两个 o。 |
| {n,} | 	n >= 0。至少匹配 n 次。例如，'o{2,}' 不能匹配 "Bob" 中的 'o'，但能匹配 "foooood" 中的所有 o。'o{1,}' 等价于 'o+'。'o{0,}' 则等价于 'o*'。 |
| {n,m} | 	m >= n>= 0。例如，"o{1,3}" 将匹配 "fooooood" 中的前三个 o。'o{0,1}' 等价于 'o?'。逗号和两个数之间不能有空格。 |

### 定位符

描述字符串定边界

| 字符| 	说明 |
| :--- | :--- |
| ^	| 匹配输入字符串的开始位置。如果设置了 RegExp 对象的 Multiline 属性，^ 也匹配 '\n' 或 '\r' 之后的位置。 |
| $	| 匹配输入字符串的结束位置。如果设置了RegExp 对象的 Multiline 属性，$ 也匹配 '\n' 或 '\r' 之前的位置。 |
| \b | 	匹配一个单词边界，也就是指单词和空格间的位置。例如， 'er\b' 可以匹配"never" 中的 'er'，但不能匹配 "verb" 中的 'er'。 |
| \B | 	匹配非单词边界。'er\B' 能匹配 "verb" 中的 'er'，但不能匹配 "never" 中的 'er'。 |

### 圆括号

组，应用于限制多选结构的范围/分组/捕获文本/环视/特殊模式处理
<table><thead><tr><th>字符</th><th align="left">说明</th></tr></thead><tbody><tr><td>(abc)</td><td align="left">匹配 'abc' 并且记住匹配项，括号被称为捕获括号。模式/(foo) (bar) \1 \2/中的 '(foo)' 和 '(bar)' 匹配并记住字符串 "foo bar foo bar" 中前两个单词。模式中的 \1 和 \2 匹配字符串的后两个单词。注意 \1、\2、\n 是用在正则表达式的匹配环节。在正则表达式的替换环节，则要使用像 $1、$2、$n 这样的语法，例如，'bar foo'.replace( /(...) (...)/, '$2 $1' )。</td></tr><tr><td>(?:abc)</td><td align="left">匹配 'abc' 这样一组，但不记录，不保存到$变量中，否则可以通过$x取第几个括号所匹配到的项，比如：(aaa)(bbb)(ccc)(?:ddd)(eee)，可以用$1获取(aaa)匹配到的内容，而$3则获取到了(ccc)匹配到的内容，而$4则获取的是由(eee)匹配到的内容，因为前一对括号没有保存变量</td></tr><tr><td>a(?=bbb)</td><td align="left">正向肯定查找，表示a后面必须紧跟3个连续的b。例如，"Windows(?=95|98|NT|2000)"能匹配"Windows2000"中的"Windows"，但不能匹配"Windows3.1"中的"Windows"。预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始。</td></tr><tr><td>a(?!bbb)</td><td align="left">正向否定查找，表示a后面不能跟3个连续的b。例如"Windows(?!95|98|NT|2000)"能匹配"Windows3.1"中的"Windows"，但不能匹配"Windows2000"中的"Windows"。</td></tr><tr><td>(?&lt;=bbb)a</td><td align="left">反向肯定查找，表示a前面必须紧跟3个连续的b。例如，"(?&lt;=95|98|NT|2000)Windows"能匹配"2000Windows"中的"Windows"，但不能匹配"3.1Windows"中的"Windows"。</td></tr><tr><td>(?&lt;!bbb)a</td><td align="left">反向否定查找，表示a前面不能跟3个连续的b。例如"(?&lt;!95|98|NT|2000)Windows"能匹配"3.1Windows"中的"Windows"，但不能匹配"2000Windows"中的"Windows"。</td></tr></tbody></table>

### 中括号

单个匹配，字符集/排除字符集/命名字符集

<table><thead><tr><th>字符</th><th align="left">说明</th></tr></thead><tbody><tr><td>[xyz]</td><td align="left">字符集合。匹配所包含的任意一个字符。例如， '[abc]' 可以匹配 "plain" 中的 'a'。</td></tr><tr><td>[^xyz]</td><td align="left">负值字符集合。匹配未包含的任意字符。例如， '[^abc]' 可以匹配 "plain" 中的'p'、'l'、'i'、'n'。</td></tr><tr><td>[a-z]</td><td align="left">字符范围。匹配指定范围内的任意字符。例如，'[a-z]' 可以匹配 'a' 到 'z' 范围内的任意小写字母字符。</td></tr><tr><td>[^a-z]</td><td align="left">负值字符范围。匹配任何不在指定范围内的任意字符。例如，'[^a-z]' 可以匹配任何不在 'a' 到 'z' 范围内的任意字符。</td></tr></tbody></table>

### 其他特殊字符

<table><thead><tr><th>字符</th><th align="left">说明</th></tr></thead><tbody><tr><td>\</td><td align="left">将下一个字符标记为一个特殊字符、或一个原义字符、或一个 向后引用、或一个八进制转义符。例如，'n' 匹配字符 "n"。'\n' 匹配一个换行符，而 "\(" 则匹配 "("。</td></tr><tr><td>?</td><td align="left">当该字符紧跟在任何一个其他限制符 (*, +, ?, {n}, {n,}, {n,m}) 后面时，匹配模式是非贪婪的。非贪婪模式尽可能少的匹配所搜索的字符串，而默认的贪婪模式则尽可能多的匹配所搜索的字符串。例如，对于字符串 "oooo"，'o+?' 将匹配单个 "o"，而 'o+' 将匹配所有 'o'。</td></tr><tr><td>.</td><td align="left">匹配除换行符（\n、\r）之外的任何单个字符。要匹配包括 '\n' 在内的任何字符，请使用像"(.|\n)"的模式。</td></tr><tr><td>x|y</td><td align="left">匹配 x 或 y。例如，'z|food' 能匹配 "z" 或 "food"。'(z|f)ood' 则匹配 "zood" 或 "food"。</td></tr><tr><td>\d</td><td align="left">匹配一个数字字符。等价于 [0-9]。</td></tr><tr><td>\D</td><td align="left">匹配一个非数字字符。等价于 [^0-9]。</td></tr><tr><td>\w</td><td align="left">匹配字母、数字、下划线。等价于'[A-Za-z0-9_]'。</td></tr><tr><td>\W</td><td align="left">匹配非字母、数字、下划线。等价于 '[^A-Za-z0-9_]'。</td></tr><tr><td>\xn</td><td align="left">匹配 n，其中 n 为十六进制转义值。十六进制转义值必须为确定的两个数字长。例如，'\x41' 匹配 "A"。'\x041' 则等价于 '\x04' &amp; "1"。正则表达式中可以使用 ASCII 编码。</td></tr><tr><td>\num</td><td align="left">匹配 num，其中 num 是一个正整数。对所获取的匹配的引用。例如，'(.)\1' 匹配两个连续的相同字符</td></tr><tr><td>\n</td><td align="left">标识一个八进制转义值或一个向后引用。如果 \n 之前至少 n 个获取的子表达式，则 n 为向后引用。否则，如果 n 为八进制数字 (0-7)，则 n 为一个八进制转义值。</td></tr><tr><td>\nm</td><td align="left">标识一个八进制转义值或一个向后引用。如果 \nm 之前至少有 nm 个获得子表达式，则 nm 为向后引用。如果 \nm 之前至少有 n 个获取，则 n 为一个后跟文字 m 的向后引用。如果前面的条件都不满足，若 n 和 m 均为八进制数字 (0-7)，则 \nm 将匹配八进制转义值 nm。</td></tr><tr><td>\nml</td><td align="left">如果 n 为八进制数字 (0-3)，且 m 和 l 均为八进制数字 (0-7)，则匹配八进制转义值 nml。</td></tr><tr><td>\un</td><td align="left">匹配 n，其中 n 是一个用四个十六进制数字表示的 Unicode 字符。例如， \u00A9 匹配版权符号 (?)。</td></tr></tbody></table>

## 标志

有6个标志，可单独或一起使用

<table><thead><tr><th align="left">flags</th><th align="left">说明</th></tr></thead><tbody><tr><td align="left">g</td><td align="left">全局搜索</td></tr><tr><td align="left">i</td><td align="left">不区分大小写搜索</td></tr><tr><td align="left">m</td><td align="left">多行搜索</td></tr><tr><td align="left">u</td><td align="left">正确处理四个字节的 UTF-16 编码</td></tr><tr><td align="left">y</td><td align="left">粘连搜索</td></tr><tr><td align="left">s</td><td align="left">dotAll模式，即点（dot）代表一切字符</td></tr></tbody></table>

## 创建

有以下两种方式构建一个正则表达式：

### 字面量：由包含在斜杠之间的模式组成

+ 格式： pattern/flags

+ 优点：加载时编译，性能好

```js
const regex = /ab+c/;

const regex = /hello/gi;
```

### 构造函数

+ 格式：new RegExp(pattern [, flags])

+ 优点：运行时编译，可动态修改

```js
let regex = new RegExp("ab+c");

let regex = new RegExp(/hello/, "gi");

let regex = new RegExp(/hello/gi);

let regex = new RegExp("hello", "gi");
```

## 使用

```js
let regex = /nn/;
let str = 'runnobnnnbnn';
```

<table><thead><tr><th align="left">方法</th><th align="left">说明</th><th align="left">示例</th><th align="left">返回值</th></tr></thead><tbody><tr><td align="left">test</td><td align="left">测试是否匹配，返回true或false</td><td align="left">regex.test(str)</td><td align="left">true</td></tr><tr><td align="left">exec</td><td align="left">查找匹配的内容，有则返回一个数组，未匹配则返回null</td><td align="left">regex.exec(str)</td><td align="left">["nn", index: 2, input: "runnobnnnbnn", groups: undefined]</td></tr><tr><td align="left">match</td><td align="left">查找匹配的内容，有则返回一个数组，未匹配则返回null</td><td align="left">str.match(regex)</td><td align="left">["nn", index: 2, input: "runnobnnnbnn", groups: undefined]</td></tr><tr><td align="left">search</td><td align="left">查找匹配的内容，有则返回位置索引，未匹配则返回-1</td><td align="left">str.match(regex)</td><td align="left">2</td></tr><tr><td align="left">replace</td><td align="left">查找匹配的内容，并且使用替换字符str1串替换掉匹配到的子字符串</td><td align="left">str.replace(regex, str1)</td><td align="left">ruccobnnnbnn</td></tr><tr><td align="left">split</td><td align="left">使用正则表达式或者一个固定字符串分隔一个字符串，并将分隔后的子字符串存储到数组中</td><td align="left">str.split(regex)</td><td align="left">["ru", "ob", "nb", ""]</td></tr></tbody></table>

- match：非全局匹配时，跟exec很相似；全局匹配时，就大不同了，上面的例子，在全局匹配时的返回值如下：

```js
regex.exec(str) // ["nn", index: 6, input: "runnobnnnbnn", groups: undefined]

str.match(regex) // ["nn", "nn", "nn"]
```