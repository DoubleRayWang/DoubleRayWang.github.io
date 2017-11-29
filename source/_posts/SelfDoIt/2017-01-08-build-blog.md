---
layout: post
title: "我的博客搭建历程"
date: 2017-01-08 13:15:15 +0800
categories: 实践之路
tags: [Jekyll,Github,Markdown]
---


　　最近赋闲在家，想着自己也搭建一个自己的技术博客，记录分享一些技术点，说干就干，开始着手搭建感觉有点无从下手，是先写个页面？还是先找个例子学习一下？千般辗转，发现 github pages 的学习成本最低，相比其他搭建方式而言，不需要太多的服务器基础，天然基于 git 版本控制，便于对代码进行迁移，而且前一阵一直捣鼓 ruby 的一些东西，所以最终选择了 **github + git + Jekyll** 的搭建方案（ windows7 64bit 环境），那么开始折腾。

> 现用 hexo+next 搭建，极大的简化了搭建流程！

<!-- more -->

　　网上已经有很多关于这方面的教程，只是有一些教程已经过时了，比如大家可以参考 [猿blog](http://www.54tianzhisheng.cn/Blog/html/blog_github.html)、[Github建站系列教程](http://www.pchou.info/ssgithubPage/2013-01-03-build-github-blog-page-01.html)、[简书](http://www.jianshu.com/p/3f355c7872d5) 、[Pwnny's Blog](http://pwnny.cn/original/2016/06/26/MakeBlog.html)、[BIGBALLON的博客 ](https://bigballon.github.io/posts/jekyll-github.html)等的教程，这些融合在一起完全可以搭建一个简单的 github 博客，本文主要记录一下自己造的坑。

　　Github Pages 本身就可以作为一个 git 项目，其他人可以 fork 或者参与到你的博客建设中，这本身可以独立也可以互相协作，换而言之，这也是你的 contributions。

准备工作
-------

<br>

1. 注册一个 Github 账号
2. 安装并简单学习 git 版本控制
3. 学习 Markdown 语法
4. 安装 ruby、gem、jekyll 等
5. 学习一下 jekyll 的基本配置和简单的工作机制流程
6. 购买一个个人域名（ B 格提升专用）

----------------------


1.Github
------

　　对于Github的注册及使用可以参考[ 这里](http://m.blog.csdn.net/article/details?id=51336332 "Github的注册及使用")，我就不细表了，主要作一下补充及更新。

+ 在 setting 页面下的 github pages 里没有了之前的 Launch automatic page generator (启动自动页面生成器) 消失了，当然 github pages 完全可以直接放在 master 下;

+ 同git建立连接时需要的SSH在如下图的位置（当然刚建库的时候也会看到，但当时真的没有注意）

![SSH位置](/styles/images/build blog/SSH.png)


2.git 版本控制
----------

+ git 在 windows 下的安装路劲是任意的，这个不用纠结；

+ git的基本配置与命令行使用[ Git - Book ](https://git-scm.com/book/zh/v2)的讲解的非常清楚，也可以参考我自己总结的[常用 git 命令行](/2017/01/09/git-skills/)的总结；

+ 当命令行使用熟练后推荐通过 **webstorm** 进行 git 的操作，使用过程一目了然而且还非常的方便。推荐教程 [ 简书](http://www.jianshu.com/p/4e2cb06cf3e4)、[戴眼镜的小女孩](http://www.cnblogs.com/zhaoxiu/p/5772466.html)。


3.Markdown 语法
-----------

　　Markdown的语法非常之简单，[Markdown 语法说明 ](http://www.appinn.com/markdown/index.html)进行了详细的讲解；[这里 ](/2017/01/09/markdown-skills/) 是本人总结的写 Markdown 文本的一些小技巧。

4.域名配置
-------

　　这个装X方案起初我内心是拒绝的，但后来转念一想个人博客还是有个人的域名比较好，这个过程还是遇到点的坑，网上关于 CNAME 这个东西给的解释很明确，但是通过我亲自尝试，最终的配置结果如下图

<br>

#### DNS配置

<img src="/styles/images/build blog/DNS.png" alt="DNS配置" />

<br>

#### CNAME文件

![CNAME文件](/styles/images/build blog/CNAME.png)

　　首先说明这个方案是成功的，这里没有设置 IP 的解析是因为亲测 IP 确实不太稳定，如果各位看官有更好的方案，欢迎留言交流讨论！

　　按照我的这个配置不成功的可以看看 [流风雨情的博客](http://blog.csdn.net/qq_29232943/article/details/52786603)。

### 5.Jekyll 配置


　　关于 jekyll 的安装与配置在 [Github建站系列教程](http://www.pchou.info/ssgithubPage/2013-01-03-build-github-blog-page-01.html) 讲解的很详细，参照着做就行了，另外推荐 [jekyll 介绍与使用](http://jekyll.com.cn/) 和 [Jekyll 语法小节](http://www.tuicool.com/articles/qm2Qfi) ，可加深对 Jekyll 的理解，并在构建博客的过程中更加得心应手。

　　jekyll也有不太丰富的插件提供，满足基本需求还是够的，不过本人推荐使用一些纯 js 的类插件来达到功能需求（因为github对.rb插件不怎么友好）。

　　关于 RubyGems 被墙的问题，淘宝的 gems 镜像已经不再维护了，可以用 [RubyGems 镜像- Ruby China](https://gems.ruby-china.org/) ，基于国内 CDN + 国外服务器的方式，能确保几乎无延迟的同步。

<br>

### 6.过程总结

最后对我自己的搭建过程做一下总结，基本的流程如下：

1. 先找了一个模板，fork 到自己的 github；

2. 将项目名称改为 github 的用户名（ *usename.github.io* ），这样就可以直接通过 *https://usename.github.io/* 来访问 master 的页面（即博客内容）；

3. 安装 git，在项目（ blog ）目录右键 Git Bash Here，进行 git 的基本设置（设置用户名，邮箱，生成 SSH Key 与 github 连接，设置远程仓库连接，init 本地仓库等）；

4. 通过 *git pull origin master* 将 github 的内容pull到本地目录；

5. 先通过 *git add -A* 将目录中的文件 tracked，然后进行 change；

   1）如果没有自己的域名，目录中不能出现 CNAME 文件；
   
   2）改 _config.yml 时 markdown: kramdown ， 如果项目名称为（usename.github.io）则 baseurl = " " ；
   
6. Change 完了之后通过 *git status* 查看当前的状态，检查一下；

7. 通过 *git commit -m 'first commit'*  将更改的东西添加到暂存区（再次提交去掉-m即可）；

8. 最后在通过 *git push origin master* 将更改过的文件信息 push 到 github 上；

　　在这之前可以在本地通过 jekyll 先查看检查一下，配置好 jekyll 之后在 git 的当前目录下输入 *jekyll build* ，然后 *jekyll serve* ，那么现在就可以通过 localhost：4000 来访问到更改后的本地页面了。

　　**OK 到了这里，就可以踏踏实实的来专注自己的博文了。**

### 结语

　　本文大部分的技术点都在相关的推荐文档里，因为我可能和正在看本文的您一样也是第一次搭建 github 的博客，所以大部分知识都是从前辈总结的经验和官方的文档中得来的，只是自己通过实践来发现更适合自己的方法方式。

<hr>