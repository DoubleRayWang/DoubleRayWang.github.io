---
layout: post
title: "webpack 入门指南"
date: 2017-02-12 13:15:15 +0800
categories: 前沿技术
tags: [webpack,ES6,JavaScript]
---

> **本文转自博客园的 [一小时包教会 —— webpack 入门指南](http://www.cnblogs.com/chris-oil/p/5681225.html)。**

> **有些知识点已过时，请参考结合 [webpack 3 入门指南](/2017/12/19/article-webpack-3-10-0/)**

　　webpack 是近期最火的一款模块加载器兼打包工具，它能把各种资源，例如 JS（含JSX）、coffee、样式（含less/sass）、图片等都作为模块来使用和处理。

　　你可以不打算将其用在你的项目上，但没有理由不去掌握它，因为以近期 Github 上各大主流的（React相关）项目来说，它们仓库上所展示的示例已经是基于 webpack 来开发的，比如 React-Boostrap 和 Redux。<!-- more -->

　　我们可以直接使用 require(XXX) 的形式来引入各模块，即使它们可能需要经过编译（比如JSX和sass），但我们无须在上面花费太多心思，因为 webpack 有着各种健全的加载器（loader）在默默处理这些事情，这块我们后续会提到。
s
　　想对其进行更详细了解的可以点 [webpack的官网](http://webpack.github.io/) 及 [文档地址](http://webpack.github.io/docs/ ) 进去瞧一瞧。

> webpack 的优势主要可以归类为如下几个：

1. webpack 是以 commonJS 的形式来书写脚本滴，但对 AMD/CMD 的支持也很全面，方便旧项目进行代码迁移。

2. 能被模块化的不仅仅是 JS 了。

3. 开发便捷，能替代部分 grunt/gulp 的工作，比如打包、压缩混淆、图片转base64等。

4. 扩展性强，插件机制完善，特别是支持 React 热插拔（见 react-hot-loader ）的功能让人眼前一亮。

　　我们谈谈第一点。以 AMD/CMD 模式来说，鉴于模块是异步加载的，所以我们常规需要使用 define 函数来帮我们搞回调：

```js
define(['package/lib'], function(lib){
    function foo(){
        lib.log('hello world!');
    } 
    return {
        foo: foo
    };
});
```

　　另外为了可以兼容 commonJS 的写法，我们也可以将 define 这么写：

```js
define(function (require, exports, module){
    var someModule = require("someModule");
    var anotherModule = require("anotherModule");    
 
    someModule.doTehAwesome();
    anotherModule.doMoarAwesome();
 
    exports.asplode = function (){
        someModule.doTehAwesome();
        anotherModule.doMoarAwesome();
    };
});
```

　　然而对 webpack 来说，我们可以直接在上面书写 commonJS 形式的语法，无须任何 define （毕竟最终模块都打包在一起，webpack 也会最终自动加上自己的加载器）：

```js
var someModule = require("someModule");
    var anotherModule = require("anotherModule");    
 
    someModule.doTehAwesome();
    anotherModule.doMoarAwesome();
 
    exports.asplode = function (){
        someModule.doTehAwesome();
        anotherModule.doMoarAwesome();
 };
```

　　这样撸码自然更简单，跟回调神马的说 byebye～～不过即使你保留了之前 define 的写法也是可以滴，毕竟 webpack 的兼容性相当出色，方便你旧项目的模块直接迁移过来。

## 1. 安装和配置

### 1.1 安装

　　我们常规直接使用 npm 的形式来安装：

    $ npm install webpack -g

　　当然如果常规项目还是把依赖写入 package.json 包去更人性化：

    $ npm init
    $ npm install webpack --save-dev

### 1.2 配置

　　每个项目下都必须配置有一个 webpack.config.js ，它的作用如同常规的 gulpfile.js/Gruntfile.js ，就是一个配置项，告诉 webpack 它需要做什么。

　　我们看看下方的示例：

```js
var webpack = require('webpack');
var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('common.js');
 
module.exports = {
    //插件项
    plugins: [commonsPlugin],
    //页面入口文件配置
    entry: {
        index : './src/js/page/index.js'
    },
    //入口文件输出配置
    output: {
        path: 'dist/js/page',
        filename: '[name].js'
    },
    module: {
        //加载器配置
        loaders: [
            { test: /\.css$/, loader: 'style-loader!css-loader' },
            { test: /\.js$/, loader: 'jsx-loader?harmony' },
            { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
            { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
        ]
    },
    //其它解决方案配置
    resolve: {
        root: 'E:/github/flux-example/src', //绝对路径
        extensions: ['', '.js', '.json', '.scss'],
        alias: {
            AppStore : 'js/stores/AppStores.js',
            ActionType : 'js/actions/ActionType.js',
            AppAction : 'js/actions/AppAction.js'
        }
    }
};
```

-  plugins 是插件项，这里我们使用了一个 CommonsChunkPlugin 的插件，它用于提取多个入口文件的公共脚本部分，然后生成一个 common.js 来方便多页面之间进行复用。

- entry 是页面入口文件配置，output 是对应输出项配置（即入口文件最终要生成什么名字的文件、存放到哪里），其语法大致为：

```js
{
    entry: {
        page1: "./page1",
        //支持数组形式，将加载数组中的所有模块，但以最后一个模块作为输出
        page2: ["./entry1", "./entry2"]
    },
    output: {
        path: "dist/js/page",
        filename: "[name].bundle.js"
    }
}
```

　　该段代码最终会生成一个 page1.bundle.js 和 page2.bundle.js，并存放到 ./dist/js/page 文件夹下。

- module.loaders 是最关键的一块配置。它告知 webpack 每一种文件都需要使用什么加载器来处理：

```js
module: {
        //加载器配置
        loaders: [
            //.css 文件使用 style-loader 和 css-loader 来处理
            { test: /\.css$/, loader: 'style-loader!css-loader' },
            //.js 文件使用 jsx-loader 来编译处理
            { test: /\.js$/, loader: 'jsx-loader?harmony' },
            //.scss 文件使用 style-loader、css-loader 和 sass-loader 来编译处理
            { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
            //图片文件使用 url-loader 来处理，小于8kb的直接转为base64
            { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
        ]
    }
```

　　如上，"-loader"其实是可以省略不写的，多个loader之间用“!”连接起来。

　　注意所有的加载器都需要通过 npm 来加载，并建议查阅它们对应的 readme 来看看如何使用。

　　拿最后一个 [url-loader](https://github.com/webpack-contrib/url-loader) 来说，它会将样式中引用到的图片转为模块来处理，使用该加载器需要先进行安装：

> npm install url-loader -save-dev

　　配置信息的参数“?limit=8192”表示将所有小于8kb的图片都转为base64形式（其实应该说超过8kb的才使用 url-loader 来映射到文件，否则转为data url形式）。

- 最后是 resolve 配置，这块很好理解，直接写注释了：

```js
resolve: {
        //查找module的话从这里开始查找
        root: 'E:/github/flux-example/src', //绝对路径
        //自动扩展文件后缀名，意味着我们require模块可以省略不写后缀名
        extensions: ['', '.js', '.json', '.scss'],
        //模块别名定义，方便后续直接引用别名，无须多写长长的地址
        alias: {
            AppStore : 'js/stores/AppStores.js',//后续直接 require('AppStore') 即可
            ActionType : 'js/actions/ActionType.js',
            AppAction : 'js/actions/AppAction.js'
        }
    }
```

　　**关于 webpack.config.js 更详尽的配置可以参考[这里](http://webpack.github.io/docs/configuration.html)。**

## 2. 运行 webpack

　　webpack 的执行也很简单，直接执行：

> webpack --display-error-details

　　即可，后面的参数“--display-error-details”是推荐加上的，方便出错时能查阅更详尽的信息（比如 webpack 寻找模块的过程），从而更好定位到问题。

　　其他主要的参数有：

```
$ webpack --config XXX.js   //使用另一份配置文件（比如webpack.config2.js）来打包
 
$ webpack --watch   //监听变动并自动打包
 
$ webpack -p    //压缩混淆脚本，这个非常非常重要！
 
$ webpack -d    //生成map映射文件，告知哪些模块被最终打包到哪里了
```

　　其中的 -p 是很重要的参数，曾经一个未压缩的 700kb 的文件，压缩后直接降到 180kb（主要是样式这块一句就独占一行脚本，导致未压缩脚本变得很大）。

## 3. 模块引入

　　上面唠嗑了那么多配置和执行方法，下面开始说说寻常页面和脚本怎么使用呗。

### 3.1 HTML

　　直接在页面引入 webpack 最终生成的页面脚本即可，不用再写什么 data-main 或 seajs.use 了：

```html
<!DOCTYPE html>
<html>
<head lang="en">
  <meta charset="UTF-8">
  <title>demo</title>
</head>
<body>
  <script src="dist/js/page/common.js"></script>
  <script src="dist/js/page/index.js"></script>
</body>
</html>
```

　　可以看到我们连样式都不用引入，毕竟脚本执行时会动态生成<style>并标签打到head里。

### 3.2 JS

　　各脚本模块可以直接使用 commonJS 来书写，并可以直接引入未经编译的模块，比如 JSX、sass、coffee等（只要你在 webpack.config.js 里配置好了对应的加载器）。

　　我们再看看编译前的页面入口文件（index.js）：

```js
require('../../css/reset.scss'); //加载初始化样式
require('../../css/allComponent.scss'); //加载组件样式
var React = require('react');
var AppWrap = require('../component/AppWrap'); //加载组件
var createRedux = require('redux').createRedux;
var Provider = require('redux/react').Provider;
var stores = require('AppStore');
 
var redux = createRedux(stores);
 
var App = React.createClass({
    render: function() {
        return (
            <Provider redux={redux}>
                {function() { return <AppWrap />; }}
            </Provider>
        );
    }
});
 
React.render(
    <App />, document.body
);
```

　　一切就是这么简单么么哒~~ 后续各种有的没的，webpack 都会帮你进行处理。

## 4. 其他

　　至此我们已经基本上手了 webpack 的使用，下面是补充一些有用的技巧。

### 4.1 shimming

　　在 AMD/CMD 中，我们需要对不符合规范的模块（比如一些直接返回全局变量的插件）进行 shim 处理，这时候我们需要使用 [exports-loader](https://github.com/webpack-contrib/exports-loader) 来帮忙：

    { test: require.resolve("./src/js/tool/swipe.js"),  loader: "exports?swipe"}
    
　　之后在脚本中需要引用该模块的时候，这么简单地来使用就可以了：

```js
require('./tool/swipe.js');
swipe(); 
```

### 4.2 自定义公共模块提取

　　在文章开始我们使用了 CommonsChunkPlugin 插件来提取多个页面之间的公共模块，并将该模块打包为 common.js 。

　　但有时候我们希望能更加个性化一些，我们可以这样配置：

```js
var CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");
module.exports = {
    entry: {
        p1: "./page1",
        p2: "./page2",
        p3: "./page3",
        ap1: "./admin/page1",
        ap2: "./admin/page2"
    },
    output: {
        filename: "[name].js"
    },
    plugins: [
        new CommonsChunkPlugin("admin-commons.js", ["ap1", "ap2"]),
        new CommonsChunkPlugin("commons.js", ["p1", "p2", "admin-commons.js"])
    ]
};
// <script>s required:
// page1.html: commons.js, p1.js
// page2.html: commons.js, p2.js
// page3.html: p3.js
// admin-page1.html: commons.js, admin-commons.js, ap1.js
// admin-page2.html: commons.js, admin-commons.js, ap2.js
```

### 4.3 独立打包样式文件

　　有时候可能希望项目的样式能不要被打包到脚本中，而是独立出来作为.css，然后在页面中以<link>标签引入。这时候我们需要 [extract-text-webpack-plugin](https://github.com/webpack/extract-text-webpack-plugin) 来帮忙：

```js
var webpack = require('webpack');
    var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('common.js');
    var ExtractTextPlugin = require("extract-text-webpack-plugin");
 
    module.exports = {
        plugins: [commonsPlugin, new ExtractTextPlugin("[name].css")],
        entry: {}
        }
        //...省略其它配置
```

　　最终 webpack 执行后会乖乖地把样式文件提取出来：

![webpack样式文件](/styles/images/webpack/webpack_list.png)

### 4.4 使用CDN/远程文件

　　有时候我们希望某些模块走CDN并以<script>的形式挂载到页面上来加载，但又希望能在 webpack 的模块中使用上。

　　这时候我们可以在配置文件里使用 externals 属性来帮忙：

```js
{
    externals: {
        // require("jquery") 是引用自外部模块的
        // 对应全局变量 jQuery
        "jquery": "jQuery"
    }
}
```

　　需要留意的是，得**确保 CDN 文件必须在 webpack 打包文件引入之前先引入**。

　　我们倒也可以使用 script.js 在脚本中来加载我们的模块：

```js
var $script = require("scriptjs");
$script("//ajax.googleapis.com/ajax/libs/jquery/2.0.0/jquery.min.js", function() {
  $('body').html('It works!')
});
```

### 4.5 与 grunt/gulp 配合

　　以 gulp 为示例，我们可以这样混搭：

```js
gulp.task("webpack", function(callback) {
    // run webpack
    webpack({
        // configuration
    }, function(err, stats) {
        if(err) throw new gutil.PluginError("webpack", err);
        gutil.log("[webpack]", stats.toString({
            // output options
        }));
        callback();
    });
});
```

　　当然我们只需要把配置写到 webpack({ ... }) 中去即可，无须再写 webpack.config.js 了。更多参照信息请参阅：[grunt配置](http://webpack.github.io/docs/usage-with-grunt.html) / [gulp配置](http://webpack.github.io/docs/usage-with-gulp.html) 。

### 4.6 React 相关

1 推荐使用 npm install react 的形式来安装并引用 React 模块，而不是直接使用编译后的 react.js，这样最终编译出来的 React 部分的脚本会减少 10-20 kb左右的大小。

2 [react-hot-loader](https://github.com/gaearon/react-hot-loader) 是一款非常好用的 React 热插拔的加载插件，通过它可以实现修改-运行同步的效果，配合 [webpack-dev-server](http://webpack.github.io/docs/webpack-dev-server.html) 使用更佳！

基于 webpack 的入门指引就到这里，希望本文能对你有所帮助，你也可以参考这篇文章来入门： [webpack-howto](https://github.com/petehunt/webpack-howto)。

<hr>