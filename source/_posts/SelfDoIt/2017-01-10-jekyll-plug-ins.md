---
layout: post
title: "Jekyll 插件（持续更新···）"
date: 2017-01-10 10:25:15 +0800
categories: 实践之路
tags: Jekyll
---


　　博客的基础已经搭建完成，该优化的也优化了，现在本该可以安安静静的写东西了，但谁让我是一个折腾起来停不下来的小菜鸟，看着静悄悄的博客，总感觉缺少的太多的，开始从网上撸各种 jekyll 的插件让自己的页面变得丰富起来。

　　*评论功能* 和 *目录* 已经在模板中躺着了，再次感谢 [@Freud, Kang](https://github.com/luoyan35714) 的 [LessOrMore](https://github.com/luoyan35714/LessOrMore)！本文旨在记录一下本博客中使用的 jekyll 插件 ··· ···<!-- more -->

----------

## 1. 站内搜索

　　首先第一个想到的就是得有一个站内搜索的功能，终于被我发现了一个 JavaScript 库，可用于向 Jekyll 博客添加搜索功能。

　　这个 js 库的使用也是非常简单，步骤如下

+ 首先在自己的项目中引入 **[jekyll-search.js](https://github.com/DoubleRayWang/Simple-Jekyll-Search/tree/master/dest)** 

+ 在项目根目录下新建 search.json，该文件的数据参考 [这里](https://github.com/DoubleRayWang/Simple-Jekyll-Search/blob/master/example/search.json)，如果设置全文搜索可参考 [这里](https://github.com/DoubleRayWang/Simple-Jekyll-Search/blob/master/search.json)；

+ 在相关的 html 中加入如下代码，并自行编写 css；

```html
<!-- Html Elements for Search -->
<div id="search-container">
    <input type="text" id="search-input" placeholder="search...">
    <ul id="results-container"></ul>
</div>
```

+ 在项目的 js 文件中加入如下配置：

```javascript
SimpleJekyllSearch({
    searchInput: document.getElementById('search-input'),
    resultsContainer: document.getElementById('results-container'),
    json: '/search.json',
    searchResultTemplate: '<li><a href="{url}">{title}</a></li>',
    noResultsText: '',
    limit: 6,
    fuzzy: true,
    exclude: ['Welcome']
});
```

　　一个简单的搜索框就算完成了，当然要做到完美还差的多，如有任何问题可参看 [Simple-Jekyll-Search的具体说明](https://github.com/DoubleRayWang/Simple-Jekyll-Search/tree/master)。


## 2. 代码高亮

　　代码高亮可以说是程序员必备，无论是搭建模块，或者是产品的说明网站都是需要展示代码的，而代码的样式直接的影响了阅读效果，一个好的代码高亮插件将会有好的体验。

　　网上有很多的代码高亮插件的说明 [QiuJuer Tinker](http://blog.csdn.net/qiujuer/article/details/50419279) 列举了代码高亮的几种选择，而我选择了 Jekyll 可以默认编译的 Rouge 。

　　首先修改　Jekyll　项目的　**_config.yml** 文件，修改其中的 highlighter 为：

```yaml
markdown: kramdown
highlighter: rouge
```

　　代码高亮简单的理解其实就是根据 language 编译出特定 class ，然后再通过这些类获得特定的样式，So 那么问题来了，从哪儿得到这个 css 呢？感谢 **[Pygments 官网的 demo](http://pygments.org/demo/3666780/)** 已经帮我们设计好了一些样式，从 demo 中获取 css 文件的方法一般前端人员都肯定是轻车熟路，右键查看网页源代码， head 中有一个 link 是 pygments.css， 单击这个路径出现这个文件的源码，就可以 Ctrl + A - C - V 一气呵成了。

　　在自己的项目下引用这个 **pygments.css** 文件，然后重启 Jekyll 服务。

　　接下来就是怎么在 Markdown 里面书写了，基本格式就是这样：

    {% highlight ruby %}
    def show
    @widget = Widget(params[:id])
    respond_to do |format|
        format.html # show.html.erb
        format.json { render json: @widget }
    end
    end
    #本段代码的作用其实就是ruby编译markdown上的代码生成html
    {% endhighlight %}
    
    -------或者--------
    
    ```ruby
    def show
        @widget = Widget(params[:id])
        respond_to do |format|
            format.html # show.html.erb
            format.json { render json: @widget }
        end
        end
        #本段代码的作用其实就是ruby编译markdown上的代码生成html
    ```
    

　　编译后的结果就是这个样子滴：

    {% highlight ruby %}
    def show
    @widget = Widget(params[:id])
    respond_to do |format|
        format.html # show.html.erb
        format.json { render json: @widget }
    end
    end
    #本段代码的作用其实就是ruby编译markdown上的代码生成html
    {% endhighlight %}
    
　　highlight 后的 “ruby”（` 后的 “ruby” ）就是告诉编译器这段代码是什么 language，rouge 大概支持60多种语言，具体[查阅rouge-support](https://github.com/DoubleRayWang/rouge/blob/master/rouge%20support.md)。

## 3. paginate 分页

　　首先推荐参考 [白召策的博客](http://bamzc.top/2016/11/02/jekyll-paginate#1920) 比较详细的写了基本的分页功能以及自定义的分页插件。

　　在 Jekyll 3 中，需要在 gems 中安装 jekyll-paginate 插件，并添加到 _config.yml 中，然后在 _config.yml 里边开启分页功能，格式如下：

```yml
paginate:5
paginate_path: "page:num"
gems: [jekyll-paginate]
```

　　博客在 username..github.io 目录下的话，在其根目录的 index.html 下（Jekyll 的分页功能不支持 Jekyll site 中的 Markdown 或 Textile 文件。分页功能从名为 index.html 的 HTML 文件中被调用时，才能工作）添加如下代码：

```html
<!--遍历分页后的文章-->
{% for post in paginator.posts %}
  <h1><a href="{{ post.url }}">{{ post.title }}</a></h1>
  <p class="author">
    <span class="date">{{ post.date }}</span>
  </p>
  <div class="content">
    {{ post.content }}
  </div>
{% endfor %}

<!-- 分页链接 -->
<div class="pagination">
  {% if paginator.previous_page %}
    <a href="/page{{ paginator.previous_page }}" class="previous">Previous</a>
  {% else %}
    <span class="previous">Previous</span>
  {% endif %}
  <span class="page_number ">Page: {{ paginator.page }} of {{ paginator.total_pages }}</span>
  {% if paginator.next_page %}
    <a href="/page{{ paginator.next_page }}" class="next">Next</a>
  {% else %}
    <span class="next ">Next</span>
  {% endif %}
</div>

```

　　个人觉得逻辑更好的分页在 [bicky.me](http://www.bicky.me/blog/archive/my-pagination-on-homepage-base-on-jekyll-pagination-plugin/)，本博客就是参考的这个。

<hr>
