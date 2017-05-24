---
layout: post
title:  "sougopinyin安装"
date: 2017-05-23 14:15:43
categories: 工具
tags:  工具
---
* content
    {:toc}  


对于Linux系统一款好的输入法软件是少不了的，很多在Windows下的用户转移到Linux下都
为一款好的输入法软件而苦恼，对于在Windows下用惯了sougo的朋友在Linux下也能找到
sougopinyin输入法，在配置上没有Windows那么简单罢了，需要安装一些依赖。有以下几个
步骤。




###  添加国内的镜像源。

```vim

deb http://mirrors.aliyun.com/ubuntu/ quantal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-backports main restricted universe multiverse

```
添加了镜像源之后，需要更新镜像`sudo apt-get update`,Ubuntu 系统包含的输入法系统
有多种，如ibus,fcitx等，sougo是基于fcitx的，而系统是默认的是ibus所以在安装sougo
输入法的前需要先安装fcitx。

### 安装fcitx步骤。

* 添加源`sudo add-apt-repository ppa:fcitx-team/nightly`
* 更新源`sudo apt-get update`
* 安装fcitx`sudo apt-get install fcitx`
* 安装fcitx的配置文件`sudo apt-get install fcitx-config-gtk` 
* 安装fcitx的table-all软件包：`sudo apt-get install fcitx-table-all`
* 安装im-switch切换工具：`sudo apt-get install im-switch`
至此打开Ubuntu系统的软件搜索栏，检查是否安装了fcitx 和fcitx-config,如下图：

    ![sougo](http://oqaij4yei.bkt.clouddn.com/2017_05_24_sougo_first.png)]


### 安装sougo软件

* 官网下载软件：[sougo官网](http://pinyin.sogou.com/linux/?r=pinyin)
* 开始安装，1）`sudo apt-get install -f` ;2）`sudo dpkg -i *.deb # *为下载
的版本软件名`
* 设置语言支持，选择fcitx，如下图：
![sougo_second](http://oqaij4yei.bkt.clouddn.com/2017_05_24_sougo_second.png)
* 需要注意的是刚安装的系统，如果是全英文版的需要在语言支持里添加中文的支持
，同样是在上图的那个界面，选择（Install/Remove Language）添加中文支持。
* 最后重启即可安装完成。

在安装过程中可能会遇到一下依赖问题，解决的办法是缺少依赖，通过`sudo apt-get
   install (dependeces)`安装。最后测试是有个依赖没有解决至今没有找到可以解决的办
   法。如找不到中英文切换，是因为没有添加英文输入法，需要手动添加即可。
   



