---
layout: post
title: "github上搭建个人blog"
date:   2016-03-28 23:06:05
categories: WEB
tags: github
---
* content  
{:toc}  

## github上搭建个人blog  

 &emsp;&emsp;现如今很多人会选择平台来搭建自己的blog，有在CSDN上直接用他的平台。一个是因为他的流量的分享出去的概率大一点。当然也存在一部分人自己用第三方的开源项目搭建自己的blog，像非常流行的wordpress这个框架，甚至很多初创的企业用它来搭建自己的官方网站，还有就是可以用它来搭建自己的内容发布系统，并且它提供很多的插件，主题什么之类的也特别的多。这样一来显得比较臃肿。一向喜欢简单清新风格的我还是选择了另一套方案。通过jekyll+github搭建自己的blog平台。没什么好坏之分适合自己的就是最好的。曾经自己也开发了一套blog系统，奈何国内空间各种要备案。再加上自己对于自己的美感能力，还是有一点自知自明的，那个系统就是个资源管理的系统。至于主页那个难看我就不吐槽了。




## 搭建前环境配置  

### 搭建本地的环境（并不是必须的）  

 &emsp;&emsp;搭建本地的环境这个不是必须的，当然了要在本地看到blog的效果，那这个过程就是必须的了。
#### 软件下载  

* ruby (找一个ruby的安装文件，因为jekyll的本地安装是必须要这个支持的)  
* gem ruby的软件管理下载器，相当于Linux下的RPM 和 yum
* Devkit 安装（官网有下 记得配置环境）
* jekyll安装 `gem install jekyll`
* jekyll new blog_demo(在当前的目录下，就会新建一个blog_demo文件)并已经有些初始化文件一般_posts就是文档的放置目录。_site就是jekyll生成的静态文本了。其他的文件关注官网
* jekyll server （打开服务器，本地可以访问了）
* git 跟github通信的话，本地最后装上git
* Pygments 安装代码高亮（通过python）
#### win遇到的问题    

* 在win下难免遇到问题，
>. 1.证书验证，下载证书，把证书配置到环境变量  
>. 2.最开始的是没安装Devkit,但是jekyll能正常安装。但是安装一些主题的blog在本地运行的话就需要一些插件了，像分页的插件。这个时候没有Devkit 可能会报错，最好安装上.  

## 开发  

 &emsp;&emsp;本阶段主要是通过下载一些主题先在本地调试好，在通过git的上传到github中，这个过程需要了解一些github的常用命令     

`git remote add origin https://youname.github.io/yourrepo.git`     

`git add .`    

`git commit `   

`git push origin` 前提是你的github上有了gh-pages这个分支。   

`git checkout --orphn gh-pages` 就能切换到本地的gh-pages，没有就会创建这个分支。  
    
 
### 遇到的问题  

>. 1.注意_config.yml文件，里面的一些全局配置，baseurl是什么。  
>. 2.若需要绑定自己的域名的话修改CNAME文件，注意文件名大写，里面的内容就是你的域名，这个时候还要关注一下配置文件中的baseurl。接下来就是可以开始写文章了。  

## 总结  

 &emsp;&emsp;搭建的过程对于一个没开经验的陌生的人来说确实比较痛苦，各种奇葩的问题。这个时候就应该相信搜索引擎的力量，google一下里面会有你要的答案，推荐在overstack里面寻找答案。
