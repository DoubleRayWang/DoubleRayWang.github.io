---
layout: post
title: "CSS 奇巧淫技"
date: 2017-03-26 12:19:15 +0800
categories: 面试总结
tags: CSS
---


　　能使用html/css解决的问题就不要使用JS，为什么呢？ 两个字，因为简单。简单就意味着更快的开发速度，更小的维护成本，同时往往具有更好的体验(但是也不要太局限于这个思路，好的方法是适用才好，而不是为了追求性能而性能)，JS 几乎可以做任何事情，但是有时候会显得十分笨拙，在js/html/css三者间灵活地切换，往往会极大地简化开发，没有谁是最好的语言，只有适不适合。只要用得好，不管黑猫白猫，都是好猫。下面总结几个实例。<!-- more -->

## 1. 自定义radio/checkbox的样式

　　我们知道，使用原生的radio/checkbox是不可以改变它的样式的，得自己用div/span去画，然后再去监听点击事件。但是这样需要自己去写逻辑控制，例如radio只能选一个的功能，另一个是没有办法使用change事件。就是没有用原生的方便。

　　但是实际上可以用一点CSS3的技巧实现自定义的目的，如下，就是用原生实现的radio：

![原生实现的radio](/styles/images/css/radio_checkbox.gif)
  
　　这个主要是借助了CSS3提供的一个伪类:checkd，只要radio/checkbox是选中状态，这个伪类就会生效，因此可以利用选中和非选中的这两种状态，去切换不同的样式。如下把一个checkbox和一个用来自定义样式的span写在一个label里面，checkbox始终隐藏：

````html
<style>
input[type=checkbox]{
    display: none;
}
/*未选中的checkbox的样式*/
.checkbox{
 
}
</style>
<label>
    <input type="checkbox">
    <span class="checkbox"></span>
</label>
````

　　写在label里面是为了能够点击span的时候改变checkbox的状态，然后再改一下选中态的样式即可：

````css
input[type=checkbox]:checked + .checkbox{
 ...
}
````

　　关键在于这一步，添加一个打勾的背景图也好，使用图标字体也好。
  :checked兼容性还是比较好的，只要你不用兼容IE8就可以使用，或者说只要你可以用nth-of-type，就可以用:checked
  
## 2. 多列等高

　　多列等高的问题是这样的，排成一行的几列由于内容长短不一致，导致容器的高度不一致：

![2](/styles/images/css/column_height1.png)

　　你可以用js算一下，以最高的一列的高度去设置所有列的高度，然而这个会造成页面闪动，刚开始打开页面的时候高度不一致，然后发现突然又对齐了。这个解决办法主要有两种：

- 第一种是利用**CSS 伸缩盒布局**，如下：

````html
<style>
	.wrapper {
		display: flex;
		flex-direction: row
	}
			
	.wrapper>div {
		width: 150px;
		margin: 0 10px;
		text-align: center;
		background-color: #ececec;
		border: 1px solid #ccc;
	}
</style>
<div class="wrapper">
    <div>column 1</div>
    <div>column 2</div>
    <div>column 3</div>
    <div>column 4</div>
</div>
````

　　效果如下：

![2](/styles/images/css/column_height2.png)

　　效果不错，只是兼容性你懂的，其他的关于flexbox的内容可以移步[Flex布局新旧混合写法详解](/2017/01/15/css-flex/)。

- 第二种办法是**借助table的自适应**特性:

　　每个div都是一个td，td肯定是等高的，html结构不变，CSS改一下： 

````css
.wrapper{
    display: table;
    border-spacing: 20px;  /* td间的间距*/
}
 
.wrapper > div {
    display: table-cell;
    width: 1000px;         /*设置很大的宽度，table自动平分宽度 */
    border-radius: 5px;    
}
````

## 3. 需要根据个数显示不同样式

　　例如说可能有1~3个item显示在同一行，而item的个数不一定，如果1个，那这个item占宽100%，2个时每一个50%，3个时每一个33%，这个你也可以用js计算一下，但是用CSS3就可以解决这个问题：

````html
<style>
    li{ 
        width: 100%;
    }   
    li:first-child:nth-last-child(2),
    li:first-child:nth-last-child(2) ~ li{ 
        width: 50%;
    }   
    
    li:first-child:nth-last-child(3),
    li:first-child:nth-last-child(3) ~ li{ 
        width: 33%; 
    }   
</style>
<ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
</ul>
````

　　第5行的意思就是选择li的第一个元素，并且它是倒数第二个元素，第6行的意思是选择前面有是第一个且是倒数第二个li的所有li，第一行是选择了第一个，第二行选择除第一个外的其它所有元素。有三个元素的类似。

## 4. 巧用CSS3伪类

　　CSS3的伪类提供了状态切换，除了第3点提到的checked之外，还有其它几个很好用的，例如:focus、:invalid等;

- 例如下面的效果：focus的时候把左边的放大镜颜色加深：

![2](/styles/images/css/css3_focus.gif)

　　借用:focus实现：

````html
<style>
    /*正常状态为浅灰色*/
    .icon-search{
        color: #ccc;
    }
    /*input focus时为深灰色 */
    input[type=search]:focus + .icon-search{
        color: #111;
    }
</style>
 
<input type="search">
<span class="icon-search"></span>
````

- 再如，如果用户输入不合法，则下一步是半透明不可点的状态：

![2](/styles/images/css/css3_invalid.gif)

　　实现这一步可以用html5的input和css3的:invalid

````html
<style>
    input[type=email]:invalid + .next-step{
        opacity: 0.5;
    }
</style>
<input type="email">
<span class="next-step">Next</span>
````

　　通过input的type和pattern属性约束合法性，然后触发:invalid。

## 5. 自动监听回车事件（HTML）

　　这个的场景是希望按回车的时候能够触发请求，或者按回车实现跳转，或者是按下回车就送一条聊天消息。

　　通常的做法是监听下keypress事件，然后检查一下keycode是不是回车，如果是则发请求。

　　但是其实有个特别简单的办法，也是不需要一行JS，那就是把表单写在一个form里面，按回车会自动触发submit事件。读者可以自己试试。这个就启示我们要用语义的html组织，而不是全部都用div。如果用相应的html标签，浏览器会自动做一些优化，特别是表单提交的input。

<hr>