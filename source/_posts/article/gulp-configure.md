---
layout: post
title: "gulp 实用指南"
date: 2017-01-11 19:45:15 +0800
categories: 实践之路
tags: 
- gulp
- node
---


　　**本文旨在记录 gulp 配置及使用的过程，方便日后查阅。（本文参考 [一点\|gulp详细入门教程](http://www.ydcss.com/) ）**

　　gulp 是前端开发过程中对代码进行构建的工具，是自动化项目的构建利器；她不仅能对网站资源进行优化，而且在开发过程中很多重复的任务能够使用正确的工具自动完成；使用她，我们不仅可以很愉快的编写代码，而且大大提高我们的工作效率。<!-- more -->

　　gulp 是基于 Nodejs 的自动任务运行器， 她能自动化地完成javascript/coffee/sass/less/html/image/css等文件的的测试、检查、合并、压缩、格式化、浏览器自动刷新、部署文件生成，并监听文件在改动后重复指定的这些步骤。在实现上，它借鉴了Unix操作系统的管道（pipe）思想，前一级的输出，直接变成后一级的输入，使得在操作上非常简单。通过本文，我们将学习如何使用Gulp来改变开发流程，从而使开发更加快速高效。gulp 和 grunt 非常类似，但相比于 grunt 的频繁 IO 操作，gulp 的流操作，能更快地更便捷地完成构建工作。
  
　　gulp 参考地址：[gulp官方网址](http://gulpjs.com)、[gulp插件地址](http://gulpjs.com/plugins)、[gulp 官方API](https://github.com/gulpjs/gulp/blob/master/docs/API.md)、[gulp 中文API](http://www.ydcss.com/archives/424)、[gulp.js 中文网](http://www.gulpjs.com.cn/)... ...

<br>

## gulp 配置

　　首先当然是安装 [nodejs](https://nodejs.org/en/)，通过 [nodejs](https://nodejs.org/en/) 的 npm 全局安装和项目安装 gulp，其次在项目里安装所需要的 gulp 插件，然后新建 gulp 的配置文件 gulpfile.js 并写好配置信息（定义 gulp 任务），最后通过命令提示符运行 gulp 任务即可。

　　**安装nodejs -> 全局安装gulp -> 项目安装gulp以及gulp插件 -> 配置gulpfile.js -> 运行任务**

### 1. node.js 安装

　　打开[nodejs官网](https://nodejs.org/en/)，点击硕大的绿色 Download 按钮，它会根据系统信息选择对应版本（.msi文件）。然后像安装QQ一样安装它就可以了（安装路径随意）。

　　通过命令行　*node -v*　和　*npm -v*　分别检查 node 和 npm 是否全局安装成功（如下图出现版本号即为安装成功，npm是在安装nodejs时一同安装的）。

<img src="/styles/images/gulp/node.png" alt="nodejs安装成功" />

### 2. npm 使用

　　npm（node package manager）nodejs 的包管理器，用于 node 插件管理（包括安装、卸载、管理依赖等），使用npm安装插件：命令提示符执行 **npm install \<name\> -g \--save-dev** ,-g 为全局安装、\--save-dev 为保存到本地，信息会配置到 package.json 内。

　　因为 npm 的服务器在国外，受网络影响大，可能出现异常，所以我们选择[ 淘宝的 npmjs.org 镜像 ](http://npm.taobao.org/)，命令提示符执行 **npm install cnpm -g --registry=https://registry.npm.taobao.org**；　**注意**：安装完后最好查看其版本号 *cnpm -v* 或关闭命令提示符重新打开，安装完直接使用有可能会出现错误。

　　**注**：cnpm 跟 npm 用法完全一致，只是在执行命令时将 npm 改为 cnpm （以下操作将以 cnpm 代替 npm ）。

### 3. gulp 安装

　　在安装 gulp 之前需先在项目根目录下新建 package.json 文件或通过命令提示符执行 **cnpm init**（文件内不允许写注释，[为什么？](https://www.zhihu.com/question/23004511)）。

<br>

#### 3.1 全局安装 gulp

　　命令提示符执行 **cnpm install gulp -g** 全局安装 gulp，*gulp -v* 出现版本号即为正确安装（全局安装 gulp 目的是为了通过她执行 gulp 任务）。

<br>

#### 3.2 本地安装 gulp

　　在项目根目录下执行命令提示符 **cnpm install gulp \--save-dev** 本地安装 gulp（本地安装 gulp 目的是为了调用 gulp 插件的功能）。

### 4. gulp 插件安装

　　gulp的插件数量虽然没有grunt那么多，但也可以说是应有尽有了，下面列举一些常用的插件。

<br>

+ 浏览器自动刷新页面：命令提示符执行 **cnpm install gulp-livereload \--save-dev**；

+ 自动处理浏览器前缀：命令提示符执行 **cnpm install gulp-autoprefixer \--save-dev**；

+ 合并javascript文件：命令提示符执行 **cnpm install gulp-concat \--save-dev**；

+ 压缩css文件：命令提示符执行 **cnpm install gulp-clean-css \--save-dev**；

+ 压缩图片文件：命令提示符执行 **cnpm install gulp-imagemin \--save-dev**；

+ 压缩html：命令提示符执行 **cnpm install gulp-htmlmin \--save-dev**；

+ less(sass)文件编译成css：命令提示符执行 **cnpm install gulp-less(sass) \--save-dev**；

<br>

　　插件安装完成后打开　package.json　查看一下各个插件的版本号是否已被记录在 devDependencies 下，如被记录（如下）则说明插件安装成功。

```jsonnet
    "devDependencies": {
        "gulp": "^3.9.1",
        "gulp-autoprefixer": "^3.1.1",
        "gulp-clean-css": "^2.3.2",
        "gulp-concat": "^2.6.1",
        "gulp-htmlmin": "^3.0.0",
        "gulp-imagemin": "^3.1.1",
        "gulp-less": "^3.3.0",
        "gulp-livereload": "^3.8.1",
        "gulp-uglify": "^2.0.0"
    }
```
    
## gulp 运行

　　在 gulp 插件安装完成之后，还需要在项目根目录下新建一个 **gulpfile.js** 的配置文件（很重要）,参考配置内容如下：

<br>


```js
    var gulp = require('gulp'),
    //导入工具包 require('node_modules里对应模块')
        less = require('gulp-less'),//编译less
        clean = require('gulp-clean-css'),//压缩css
        imagemin = require('gulp-imagemin'),//图片压缩
        uglify = require('gulp-uglify'),//压缩js
        htmlmin = require('gulp-htmlmin'), //html压缩
        livereload = require('gulp-livereload'),  //浏览器自动刷新页面
        autoprefixer = require('gulp-autoprefixer'),  //根据设置浏览器版本自动处理浏览器前缀
        concat = require('gulp-concat');  //合并js文件
    //css压缩
    gulp.task('testCssmin', function () {
        gulp.src('src/index.css')
            .pipe(clean({
                advanced: true,  //类型：Boolean 默认：true [是否开启高级优化（合并选择器等）]
                compatibility: 'ie7',  //保留ie7及以下兼容写法 类型：String 默认：''or'*' [启用兼容模式； 'ie7'：IE7兼容模式，'ie8'：IE8兼容模式，'*'：IE9+兼容模式]
                keepBreaks: true,  //类型：Boolean 默认：false [是否保留换行]
                keepSpecialComments: '*'  //保留所有特殊前缀 当你用autoprefixer生成的浏览器前缀，如果不加这个参数，有可能将会删除你的部分前缀
            }))
        .   pipe(gulp.dest('src')); 
    });
    //less编译
    gulp.task('less', function() {
        gulp.src('Machinery/public/less/index.less')
            .pipe(less())
            .pipe(gulp.dest('Machinery/build/css'))
    });
    //js压缩
    gulp.task('jsmin', function () {
        gulp.src('Machinery/public/js/*.js')
            .pipe(uglify())
            .pipe(gulp.dest('Machinery/build/js'));
    });
    //js合并
    gulp.task('testConcat', function () {
        gulp.src('src/*.js')
            .pipe(concat('zepto.concat.js'))//合并后的文件名
            .pipe(gulp.dest('src'));
    });
    //图片压缩
    gulp.task('testImagemin', function () {
        gulp.src('last/construction/public/images/*.{png,jpg,gif,ico}')
            .pipe(imagemin())
            .pipe(gulp.dest('last/construction/build/images'));
    });
    //html压缩
    gulp.task('testHtmlmin', function () {
        var options = {
            removeComments: true,//清除HTML注释
            collapseWhitespace: true,//压缩HTML
            collapseBooleanAttributes: true,//省略布尔属性的值 <input checked="true"/> ==> <input />
            removeEmptyAttributes: true,//删除所有空格作属性值 <input id="" /> ==> <input />
            removeScriptTypeAttributes: true,//删除<script>的type="text/javascript"
            removeStyleLinkTypeAttributes: true,//删除<style>和<link>的type="text/css"
            minifyJS: true,//压缩页面JS
            minifyCSS: true//压缩页面CSS
        };
        gulp.src('Machinery/*.html')
            .pipe(htmlmin(options))
            .pipe(gulp.dest('Machinery/.min.html'));
    });
    //自动刷新页面
    gulp.task('less', function() {
        gulp.src('src/less/*.less')
            .pipe(less())
            .pipe(gulp.dest('src/css'))
            .pipe(livereload());
    });
    //自动处理浏览器前缀
    gulp.task('testAutoFx', function () {
        gulp.src('src/css/index.css')
            .pipe(autoprefixer({
                browsers: ['last 2 versions', 'Android >= 4.0','last 2 Explorer versions'],
                cascade: false, //是否美化属性值 默认：true 像这样：
                //-webkit-transform: rotate(45deg);
                //        transform: rotate(45deg);
                remove:true //是否去掉不必要的前缀 默认：true
            }))
            .pipe(gulp.dest('dist/css'));
    });
    gulp.task('default',['testLess', 'elseTask']); //定义默认任务 elseTask为其他任务，该示例没有定义elseTask任务
    //当执行gulp default或gulp将会调用default任务里的所有任务[‘testLess’,’elseTask’]。
     
   //gulp.task(name[, deps], fn) 定义任务  name：任务名称 deps：依赖任务名称 fn：回调函数
    //gulp.src(globs[, options]) 执行任务处理的文件  globs：处理的文件路径(字符串或者字符串数组) 
    //gulp.dest(path[, options]) 处理完后文件生成路径
```

　　以上为我现在本地的配置内容，最基础的使用足够，还有更深度的配置可参照 [一点\|gulp详细入门教程](http://www.ydcss.com/archives/category/%E6%9E%84%E5%BB%BA%E5%B7%A5%E5%85%B7)，gulp 相关的 API 介绍看 [这里](http://www.ydcss.com/archives/424) 。

### 1. 命令提示符运行 gulp 任务

　　**运行 gulp 时执行命令提示符 *gulp 任务名称*，例如：编译less：命令提示符执行 *gulp testLess*。**

<img src="/styles/images/gulp/gulp-cmd.png" alt="命令提示符运行 gulp 任务" />

### 2. webstorm 运行gulp任务

　　使用 webstorm 可视化运行 gulp 任务，首先将项目导入 webstorm ，右键 gulpfile.js  选择 ”Show Gulp Tasks” 打开 Gulp 窗口，若出现 ”No task found” ，选择右键 ”Reload tasks” ，双击运行即可。

<img src="/styles/images/gulp/gulp-w1.png" alt="webstorm运行gulp任务"/><img src="/styles/images/gulp/gulp-w2.png" alt="webstorm运行gulp任务" />

<br>

## 总结

+ 安装 nodejs；

+ 新建 package.json 文件；

+ 全局和本地安装 gulp；

+ 安装 gulp 插件；

+ 新建 gulpfile.js 文件；

+ 通过命令提示符或 webstorm 运行 gulp 任务。


<hr>
