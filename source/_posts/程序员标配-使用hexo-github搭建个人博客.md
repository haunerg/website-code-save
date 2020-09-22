---
title: 程序员标配--使用hexo+github搭建个人博客
date: 2020-01-09 18:37:03
categories: 个人博客
tags:
 - hexo
 - github
preview: 200
toc: true
---
&emsp;&emsp;作为一名合格的程序员，拥有一个自己的个人网站，那想必是非常舒服了。我们可以在里边写写技术博客，发发牢骚，记录自己的生活。当然，我们可以在博客园，掘金的博客网站发表，但是那毕竟是人家的东西，我们应该试着搭建一个自己的博客。但是，做网站就要买服务器，买服务器就要花钱，这对于我们这帮屌丝程序员来说当然是不太友好，这时候，我们就想到一个搭建很熟悉的东西--github。
&emsp;&emsp;github作为世界上最大的同性交友基地，它除了能为广大男性同胞带来灵魂上的快感，还有一个巨大的作用，那就是可以充当一个小型的服务器。

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

# 安装hexo
hexo需要全局安装，命令如下：

```
npm install -g hexo
```

linux和mac用户需要添加权限 

```
sudo npm install -g hexo
```
安装完成之后，接下来我们下载博客模板

# 初始化模板
 首先我们新建一个文件夹用来放模板，文件夹名字随便起，题材不限，这里以```blog```为例,新建blog文件夹，切换到blog，用hexo给出的命令```hexo init```初始化模板

 ```
 mkdir blog 

 cd blog

 hexo init
 ```
 下载完成时候，blog文件夹内出现以下文件
 
 ![image](https://img-blog.csdnimg.cn/2020042915153328.png)
 下面介绍以下各个文件的作用
 - scaffolds: 模板文件夹，里边的```.md```文件都是各个模板，比如```page.md```是我们新建页面的模板， post是我们新建博客的模板，我们可以修改这些默认模板
 - source: 资源文件夹
 - themes: 主题文件夹
 - _config.yml: 网站的配置信息，大部分配置都可以在这里修改
 - package.json: 应用程序信息，也装着各种依赖

# 打包成博客
模板下载好之后，我们就用这个模板打包成博客并启动服务，首先先装一下依赖
```
npm i
```
装完之后打包模板
```
hexo g
```
打包完成之后你会发现在目录下多了一个```public```文件夹,这些文件就是最终生成的博客静态网站，最终都要提交到github发布。

接下来启动服务
```
hexo g
```
启动完之后控制台生成```http://localhost:4000```网址，打开之后就是这个丑样子：

![image](https://img-blog.csdnimg.cn/20200429162134668.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)

由于这个主题实在是太丑了，所以接下来我们要美化一下

# 更换主题
hexo有一个主题库[地址](https://hexo.io/themes/),这里的主题应有，当然如果你都不喜欢，可以即几做一个，方法自己网上找。 我现在用的主题是```freemind.386```,病毒风格，特殊口味。
以```freemind.386```为例，克隆项目到```theme```目录下
```
git clone git@github.com:blackshow/hexo-theme-freemind.386.git themes/freemind.385
```
更改项目目录下```_config.yml```配置文件中的```theme```属性的值为```freemind.386```。注意不是主题文件中的```_config.yml```
（再次注意，有的的主题是需要下载依赖的，具体看clone的主题目录有没有```package.json```,如果有，需要切换到主题目录执行```npm install```下载依赖包）;
然后重新打包，启动服务，
```
hexo g
hexo s
```
也可以使用组合命令
```
hexo s -g
```

到此为止，主题就更换完了

本人对```freemind.386```这个主题进行了二次封装，修改了一些样式，添加评论插件，欢迎star [github地址](https://github.com/qisi007/hexo-theme-freemind.386.second)

# 部署
接下来，我们把生成的静态网页部署到github。
#### (1)配置ssh
首先，需要配置```ssh```,在命令行输入
```
ssh-keygen -t rsa -C "此处填写你的邮件地址"
```
然后疯狂按3次回车就生成好了，我们把生成好的的````ssh```粘贴到github中，命令行输入
```
cat ~/.ssh/id_rsa.pub
```
复制```ssh```到github中，位置是 【点击github头像】-->【Settings】-->【SSH and GPG keys】--> 【New SSH key】

然后配置git用户信息，防止以后每次提交代码都要输入账号和密码
```
git config --global user.name "github用户名"
git config --global user.email "githab注册用的邮箱"
```

#### (2)创建博客仓库
点击github右上角的加号，选择【New responitroy】新建一个仓库，仓库名为 ```你的github名+github.io```

![image](https://img-blog.csdnimg.cn/2020042917304975.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)

进入仓库，选择【Settings】找到【GitHub Pages】这一项，其中【Source】选择```master branch```,
下边的主题随便选一个让后commit提交

![image](https://img-blog.csdnimg.cn/20200429174204811.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)

由于我绑定了域名，所以分配到了该域名的二级域名下，正常情况访问```你的用户名.github.io```应该能访问你的博客了

#### (3)关联仓库地址部署
上边所有配置完成之后，我们复制该远程仓库地址

![image](https://img-blog.csdnimg.cn/20200429174927885.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)


将改地址复制到本地博客目录的```_config.yml```的```deploy```中
![image](https://img-blog.csdnimg.cn/2020042917505687.png)

注意远程仓库地址不加```https://```,接下来安装一个插件
```
npm install hexo-deployer-git --save
```
安装成功后输入如下命令部署
```
hexo d
```
出现如下信息部署就成功了，等几分钟访问```你的用户名.github.io```就ojbk了

![image](https://img-blog.csdnimg.cn/20200429175657132.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)



# 关联域名
毕竟上边那种github的地址不容易被人记住，如果有一个自己的地址就再好不过了。其实买个普通的也就几十块钱，少```洗一次脚```能买好几个。我是在腾讯云买的，一年21块大洋。[购买传送门](https://dnspod.cloud.tencent.com/)

完成支付之后，进入控制台，绑定域名之前需要```邮箱验证```和```实名认证```，用不多长时间，几分钟搞定。然后点击进行解析域名，操作路径为【我的域名】-->【解析】

![image](https://img-blog.csdnimg.cn/20200429182104444.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)

解析需要github仓库服务的ip,获取方法为在命令行输入
```
ping 服务地址   // 例如 liuguisheng.github.io
```
获取后在腾讯云控制台配置如下

![image](https://img-blog.csdnimg.cn/20200429182546851.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)

具体配置参入里边都有介绍，这里我就不多说了，接下来进行github设置

在项目的【source】文件夹下新建```CNAME```文件（没有后缀）,里边填写你的域名。重新打包发布等几分钟就能访问你的域名了。 ×注意网上有的方法是直接在远程仓库的根目录下直接新建```CNAME```文件，不要那么干，因为你每次部署的时候都会把这个文件冲调，放在【source】文件的原因是hexo编译的时候会把里边的文件原封不动复制到【public】文件夹下。

到现在为止基本的部署的工作都做完了，接下来94要看看我们如何写博客了

# 发布博客

发布博客很简单，命令行输入
```
hexo new '博客名称'
```
完成后hexo会在【source】中的【_posts】文件生成一个```mardown```文件，在里边编辑博客就可以了

mardown文件头部部分可以添加的属性说明

    - title----------博客名称
    - date-----------发布日期
    - categories-----分类
    - tags-----------标签
    - preview--------列表页最多展示多少个文字
    - toc------------是否添加目录结构

如果你想修改默模板可以去【scaffolds】文件夹下修改```post.md```文件

# hexo 命令汇总
#### (1)基本命令

描述 | 命令 | 缩写
--- | --- | --- 
生成静态页面 | hexo generate | hexo g
开启服务 | hexo server | hexo s
部署到github | hexo deploy | hexo d
新建文章 | hexo new "articleName" | -
新建页面 | hexo new page "htmlName" | -
查看帮助 | hexo help | - 
查看hexo版本号 | hexo version

#### (2)组合命令
描述 | 命令 |
--- | --- |  
生成页面并预览 | hexo s -g | 
生成页面并上传到github | hexo d -g | 


# 添加标签页和分类页面

#### (1)标签页
命令行输入
```
hexo new page "tags"
```
这是hexo会在【soruce】文件夹下生成一个【tags】文件夹，修改该文件夹下的```index.md```,替换里边的内容为
```
---
title: tags
date: 2020-04-28 17:15:54
type: "tags" 
layout: "tags" 
---

```
然后修改【theme】文件夹下的```_config.yml```的menu,添加如下配置

![image](https://img-blog.csdnimg.cn/2020042918543177.png)

重新打包部署即可


#### (2)分类页面
分类页面和标签页类似，命令行输入
```
hexo new page "categories"
```
这是hexo会在【soruce】文件夹下生成一个【tags】文件夹，修改该文件夹下的```index.md```,替换里边的内容为
```
---
title: categories
date: 2020-04-28 16:46:27
type: "categories"
layout: "categories"
comments: false
---


```
然后修改【theme】文件夹下的```_config.yml```的menu,添加如下配置

![image](https://img-blog.csdnimg.cn/20200429185756736.png)

重新打包部署即可

# 添加评论插件
网上的评论插件很多，但是最终我选择了```valine```, valine有一个自己的评论管理后台```leancloud```,每次读者在博客的评论都会流入leancloud后台。
但是，```freemind.386```这个主题没有集成valine,后来从网上找了方法，改造了一下主题，如果其他主题没有集成valine,也可以用下边的方法。

具体方法是在主题目录的【layout】-->【_partial】-->【post】文件夹下新建```valine.ejs```文件，里边填写如下代码
```
<div id="comment"></div>
<script src='//unpkg.com/valine/dist/Valine.min.js'></script>
<script>
new Valine({
    el: '#comment' ,
    notify:true, 
    verify:true, 
    appId: '<%=theme.valine.appId%>',
    appKey: '<%=theme.valine.appKey%>',
    placeholder: '<%=theme.valine.appKey%>',
    path:window.location.pathname, 
    avatar:'mm' 
});
</script>
```

然后在主题目录的【layout】-->【_partial】的```article.ejs```文件中找到

```
	<%- partial('post/comment', {page: item}) %>
```
这行代码下面填写

```
<% if (theme.valine && theme.valine.appId && theme.valine.appKey){ %>
		<section id="comments" class="comments">
			<style>
			.comments{padding:10px;background:#fff}
			@media screen and (max-width:800px){.comments{margin:auto;padding:10px;background:#000}}
			</style>
			<%- partial('post/valine', {
			key: post.slug,
			title: post.title,
			url: config.url+url_for(post.path)
			}) %>
		</section>
	<% } %>
```
我把修改好的主题上传到我的github中，可以自行clone到【themes】文件夹下， [传送门](https://github.com/qisi007/hexo-theme-freemind.386.second)。


上述工作完成后我们看看怎么添加valine评论插件。 

首先去leancloud官网注册账号[传送门](https://leancloud.cn/)。注册完成之后进个人管理页面```添加应用```,输入应用名称，下边选开发板

![image](https://img-blog.csdnimg.cn/20200430114031590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)

点击设置按钮

![image](https://img-blog.csdnimg.cn/20200430114551854.png)


点击左边【存储】-->【创建class】,新建两个类用来存储评论，分别是```Comment```和```Counter```

然后点击左边【设置】-->【安全中心】把没用的服务都关掉

![image](https://img-blog.csdnimg.cn/20200430114909833.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)

然后点击【应用Keys】记录下```AppID```和```AppKey```,这两个是需要配置到项目中的

最后在主题文件夹的```_config.yml```配置文件中添加如下代码，把```AppID```和```AppKey```添加到对应位置

```
valine:
  enable: true # 是否开启
  appId:    # 上一步获取的 App ID
  appKey:   # 上一步获取的 App Key
  notify: true # 新留言是否需要通知 https://github.com/xCss/Valine/wiki
  verify: true # 是否需要验证，验证比较反人类建议false关闭
  placeholder: 请在此输入您的留言 # 默认留言框内的文字
  avatar: mm # 默认头像
  guest_info: nick,mail # 默认留言框的头部需要访问者输入的信息
  pageSize: 10 # pagination size #默认单页的留言条数
```

最后执行```hexo d -g```打包部署，访问域名应该就可以了

![image](https://img-blog.csdnimg.cn/20200430115425744.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVMaXVndWlzaGVuZw==,size_16,color_FFFFFF,t_70)



好了，到现在可以拿着自己的网站出去装逼了，其实还有很多功能，只不过我现在没有研究，比如```相册```，```音乐播放器```等，等我以后有时间弄出来之后再分享给大家。

我把我搭建的博客代码仓库地址也上传到了我的github中[传送门](https://github.com/qisi007/website-code-save)；

[我的个人主页地址](https://www.liuguisheng.vip/)

最后感谢下边博客的博主提供的帮助，小心心送上❤❤；

感谢:

[三分钟在GitHub上搭建个人博客](https://zhuanlan.zhihu.com/p/28321740)

[使用hexo+github搭建免费个人博客详细教程](https://www.cnblogs.com/liuxianan/p/build-blog-website-by-hexo-github.html)



