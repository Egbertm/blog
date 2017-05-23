---
layout: post
title:  "pyspider 使用小总结"
date:   2016-09-21 23:06:05
categories: python
tags: pyhton pyspider
---
* content  
{:toc}  

### pyspider一个优秀的爬虫框架  
pyspider是天朝人写的一个基于python的爬虫框架，官方文档[http://docs.pyspider.org](http://docs.pyspider.org "官方文档")中有对pyspider的介绍。现在笔者只针对自己使用过程中遇到的一些问题做探讨。   




#### pyspider的安装过程及环境配置等一系列问题
  
***问题一***   python版本选择低（不适合，建议python2.7.6及以上）；因为python安装pyspider中需要安装pycurl模块，这个模块只有在pyhton2.7.6以上能安装。  
***问题二***   安装pySpider的时候，需要有C++ complier的支持，对与WIN用户而言需要安装Visual studio(python2.7 推荐安装vs2008);否则在安装的过程中会出现**error: Unable to find vcvarsall.bat**。  
***问题三***   安装过程中的python平台的选择，笔者电脑是64位的，因此最开始是安装python27的64位版本，后来在安装pyspider的时候，到真正运行程序时总是不能成功**python.exe 奔溃**，后来查看官方文档，推荐安装的是python27 32位的版本因此换过版本后安装没有问题。  
***问题四***   在安装lxml,和pycurl模块的时候，碰上安装不成功，（如32位和64位冲突DLL错误等）可以先下载和python平台匹配的相关版本自己手动安装。  
***问题五***   安装好后需要测试可以在github上找找小例子测试一下。例子[https://github.com/Germey/TaobaoMM](https://github.com/Germey/TaobaoMM "小例子")测试的时候会出现SSL 559 证书验证问题，这个问题有一种解决方案四在crawl()函数中加上validate_cert=False即可，倘若存在验证的key不存在的问题，就需要手动更新一下代码了，下载github中的最新代码，将代码复杂到site-package中覆盖原来的带pyspider即可。  
***问题六*** 加载phantomjs,下载后把bin目录添加到环境变量中，pyspider all 会启动所有的组件，就可以在crawl中通过fetch-type='js'解析脚本了。  

### pyspider的最佳实践
  
框架的组合，python2.7.9（32位推荐）+pyspider（0.3.9）+phantomjs+selenium。

### pyspider的应用实例
  
例子代码python2.7.9（32位）+phantomjs+pyspider(0.3.9)测试成功。
    
    #!/usr/bin/env python
    # -*- encoding: utf-8 -*-
    # Created on 2016-03-25 00:59:45
    # Project: taobaomm

    from pyspider.libs.base_handler import *

    PAGE_START = 3
    PAGE_END = 4
    DIR_PATH = 'C:/var2/py/mm'


    class Handler(BaseHandler):
        headers= {
                          "Accept":"text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8",
    "Accept-Encoding":"gzip, deflate, sdch",
    "Accept-Language":"zh-CN,zh;q=0.8",
    "Cache-Control":"max-age=0",
    "Connection":"keep-alive",
    "User-Agent":"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2272.101 Safari/537.36"
        }

    crawl_config = {
        "headers" : headers,
        "timeout" : 100
    }
    
    def __init__(self):
        self.base_url = 'https://mm.taobao.com/json/request_top_list.htm?page='
        self.page_num = PAGE_START
        self.total_num = PAGE_END
        self.deal = Deal()

    def on_start(self):
        while self.page_num <= self.total_num:
            url = self.base_url + str(self.page_num)
            self.crawl(url, callback=self.index_page,validate_cert=False)
            self.page_num += 1

    def index_page(self, response):
        for each in response.doc('.lady-name').items():
            self.crawl(each.attr.href, callback=self.detail_page,validate_cert=False, fetch_type='js')

    def detail_page(self, response):
        domain = response.doc('.mm-p-domain-info li > span').text()
        if domain:
            page_url = 'https:' + domain
            self.crawl(page_url, callback=self.domain_page,validate_cert=False)

    def domain_page(self, response):
        name = response.doc('.mm-p-model-info-left-top dd > a').text()
        dir_path = self.deal.mkDir(name)
        brief = response.doc('.mm-aixiu-content').text()
        if dir_path:
            imgs = response.doc('.mm-aixiu-content img').items()
            count = 1
            self.deal.saveBrief(brief, dir_path, name)
            for img in imgs:
                url = img.attr.src
                if url:
                    extension = self.deal.getExtension(url)
                    file_name = name + str(count) + '.' + extension
                    count += 1
                    self.crawl(img.attr.src, callback=self.save_img,validate_cert=False,
                               save={'dir_path': dir_path, 'file_name': file_name})

    def save_img(self, response):
        content = response.content
        dir_path = response.save['dir_path']
        file_name = response.save['file_name']
        file_path = dir_path + '/' + file_name
        self.deal.saveImg(content, file_path)


    import os

    class Deal:
        def __init__(self):
            self.path = DIR_PATH
            if not self.path.endswith('/'):
            self.path = self.path + '/'
            if not os.path.exists(self.path):
            os.makedirs(self.path)

        def mkDir(self, path):
            path = path.strip()
            dir_path = self.path + path
            exists = os.path.exists(dir_path)
            if not exists:
                 os.makedirs(dir_path)
                 return dir_path
            else:
                return dir_path

        def saveImg(self, content, path):
            f = open(path, 'wb')
            f.write(content)
            f.close()

        def saveBrief(self, content, dir_path, name):
            file_name = dir_path + "/" + name + ".txt"
            f = open(file_name, "w+")
            f.write(content.encode('utf-8'))

        def getExtension(self, url):
            extension = url.split('.')[-1]
            return extension
    
 代码解释：
Handle类继承了BaseHandle，在这个类中首先通过初始化构造函数，设置一些属性，并创建一个本地的文件夹存放文件，任务启动后通过on_start独立的启动一个任务，通过回调方法调用index_page,在该方法中处理得到的doc文档内容，解析得到一系列的Items,遍历各个Item得到相应的url,传入回调函数domain_page,在这个函数中，我们可以得到人物的URL,再通过回调函数今入到该页面，接下来就是解析了。得到人物的姓名新建文件夹，得到人物的简历介绍，如果文件夹存在的话，写入简历文件。并得到所有的image的URL保存对应的照片。代码结构图如下图所示![代码结构](http://o886hn2n8.bkt.clouddn.com/pyspider/pyspiderjiegou.png)

抓取的照片文件夹如下图所示
![mulu](http://o886hn2n8.bkt.clouddn.com/pyspider/taobaommmulu.png)
![image](http://o886hn2n8.bkt.clouddn.com/pyspider/taobaomm.png)

### pyspider你值得拥有
  
pyspider 的基本入门就是这样的，以后需要用这个框架爬取一些网站上的其它资源的时候，可以自定义handel类，封装自己的方法，当然本例子是采用的是文件存取的方式，还可以借助数据库，mysql，Mangodb等等pyspider对这些都有很好的支持。
