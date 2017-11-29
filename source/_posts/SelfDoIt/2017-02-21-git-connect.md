---
layout: post
title: "Git 中 git push 到远程仓库不被授权的问题"
date: 2017-02-21 12:19:15 +0800
categories: 实践之路
tags: Git
---


　　使用 Git 过程中，今天突然遇到这样的问题，在 git push 到 github 远程仓库时失败了，提示你说这次 push 不被授权，昨天还是好好的，难道是 github 又被墙了？后开始测试发现除了 SSH 所有的连接都是正常的。Webstorm 里直接 push 会提示：

```
Push failed: Failed with error: fatal: Could not read from remote repository.
```

<!-- more -->
## 原因

　　没有匹配正确 ssh 公钥密钥对。要么是你没有生成对应的公钥密钥对并将公钥部署在 github 上的对应项目，要么就是生成了密钥对并将公钥部署在了 github 上之后，你的本地目录没有将私钥配置上或者是**之前有配置，但是过期了**。

## 解决方法

- 首先，生成一个公钥密钥对：**ssh-keygen -t rsa -C "youemail@example.com" -f ~/.ssh/id_rsa**  此命令可创建 SSH 公钥密钥对，"youemail@example.com"为你自己的邮箱地址，"id_rsa"为公钥密钥名，可修改。 创建后的公钥密钥对存放在本地目录C:\Users\[UserName]\.ssh\文件夹中，可以查看

- 将公钥部署上github，将（1）中生成的id_rsa.pub文件用文本编辑器打开，复制其中内容。在对应的github项目的“setting”中的“SSH and GPG keys”选择“new SSH key“，将公钥内容粘贴上去，新建一个SSH key。

- 在 git bash 命令行在添加私钥。查看私钥列表的命令为：**ssh-add -l**，如果输出以上命令出现Could not open a connection to your authentication agent.，则再输入**ssh-agent bash**，即可进入SSH bash。添加私钥的命令：**ssh-add ~/.ssh/id_rsa**，此时再查看私钥列表，若已经成功添加，则可以正常地使用 git push 进行对远程仓库的更新了。

> 其他 Git 常用命令行请移步 [常用 git 命令行总结](/2017/01/09/git-skills/)。

<hr>