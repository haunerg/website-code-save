---
title: 程序员标配--使用hexo+github搭建个人博客
date: 2020-01-09 18:37:03
categories: 个人博客
tags:
 - hexo
 - github
preview: 200
cover: statics/welcome-cover.jpg
---

&emsp;&emsp;但是，github作为世界上最大的同性交友基地，它除了能为广大男性同胞带来灵魂上的快感，还有一个巨大的作用，那就是可以充当一个小型的服务器。

&emsp;&emsp;**使用gitlab搭建个人博客的好处非常多，比如：**
1. 访问速度快，仓库里只需放打包好的静态页面
2. 构建快，代码push到远程仓库后，分分钟构建完
3. 免费（当然这是最大的好处）
4. 易于管理，这得益于github超强的代码管理能力，比如分支，版本回退等
5. 可以绑定自己的域名


&emsp;&emsp;常见的博客生成工具有两种，一个是[hexo](https://hexo.io/zh-cn/),另一个是[jekyll](http://jekyllcn.com/)。两者其实大同小异，但是jekyll语法和配置相对于复杂，它需要ruby环境。而hexo就比较简单易上手，只需要node环境就ok了。下面我们进入正题，详细介绍如何使用hexo+github搭建个人博客。
## 准备工作
1. 首先你得有一个github账号吧，注册过程这里不做过多介绍了。[注册账号点这里](https://github.com/join?source=header-home)
2. 配置node环境,根据系统下载好node安装包，一路下一步安装就ok了。[node下载](https://nodejs.org/zh-cn/download/)
3. shell工具，这个可以自行选择，比如cmd，powerShell,git等，建议使用[git](https://git-scm.com/)
4. 如果你想要自己的域名，可以花钱去申请一个，一般点的域名也不是太贵，一年几十块钱，其实这点钱对于程序员也不算什么，少买件格子衫就有了。

&emsp;&emsp;好了，基本的准备工作就这些了,下面真的要进入正题了。

## 安装hexo
hexo需要全局安装，命令如下：

`
npm install -g hexo
`

linux和mac用户需要添加权限 

`
sudo npm install -g hexo
`