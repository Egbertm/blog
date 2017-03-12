---
layout: post
title:  "机器学习之scikit-learn(一)"
date:   2017-3-05 23:06:05
categories: 大数据
tags:  大数据 机器学习
---
* content  
{:toc}  


机器学习，数据挖掘，深度学习这些名词不仅在学术界，现在在商业上也是经常的出现，并不是一个很时髦的概念，毕竟已经出现了很久了。  
**数据挖掘**是一个跨学科的计算机科学分支。数据挖掘是从数据中提取出隐含的过去的未知的有价值的潜在信息。数据挖掘与KDD知识发现系统的关系是KDD是从数据中辨别有效的，新颖的、潜在有用的、最终可以理解的模式的过程；  




**机器学习**：机器学习是一门人工智能的科学，该领域的主要研究对象是人工智能，特别是如何在经验学习中改善具体算法的性能，是用数据和以往的经验，以此优化计算机的程序的性能标准。  

**深度学习**：是机器学习的一个分支，它试图使用包含负载结构或由多重非线性变换构成的多个处理层对数据进行高层的抽象的算法。  


### 1 python环境的安装  

1.安装python2.7,现如今pyhton的版本已经有3.6了，可以通过python的官网[http://www.python.org](http://www.python.org "python 官网")下载对应的版本，考虑到版本3和版本2之间的差异，选择了版本2中的一个子版本python2.7.9 安装，python2.7.9的下载地址[https://www.python.org/downloads/](https://www.python.org/downloads/)，下载对应的windows版本根据系统的32位还是64位进行选择。Window x86-64-MSI installer 是64位的版本，Windows x86 MSI installer 是32位版本。下载来后，直接安装即可。  
setuptools:setuptools 是一组由PEAK(Python Enterprise Application Kit)开发的 Python 的 distutils 工具的增强工具，可以让程序员更方便的创建和发布 Python的egg 包，特别是那些对其它包具有依赖性的状况。 由 setuptools 创建和发布的包看起来和基于 distutils 发布的包没什么不同。最终用户不需要事先安装 setuptools 甚至根本不需要知道 setuptools 的存在，而程序员也不需要附上完整的 setuptools，只需要包含一个大小约 8K 的ez_setup.py脚本作为启动模块，就可以在最终用户没有安装适当版本的 setuptools 时让这些包自动下载和安装 setuptools。  
easy_install: 常使用python的人员，当需要安装第三方python包时，可能会用到easy_install命令。easy_install是由PEAK(Python Enterprise Application Kit)开发的setuptools包里带的一个命令，它用来自动地从http://pypi.python.org/simple/来安装egg包，相当于perl中的cpan或PPM、RedHat中的yum命令，但是系统都没有预装easy_install命令。如果之前也是使用.exe安装程序安装的setuptools，则安装前要在“添加/删除程序”中卸载旧的版本。安装完毕后，在Python的Scripts子目录下就会出现easy_install.exe程序。 确保将这个目录（例如 d:\Python26\Scripts）加入 PATH 环境变量。或者直接通过下载一个ez_setup.py 的脚本安装，通过执行`python ez_setup.py`,还可以通过源码安装，可以下载setuptools**.zip,解压进入安装包，通过命令`python setup.py install`安装即可。可以直接使用`easy_install *.egg` 文件直接安装软件，需要的依赖包会自动的下载，卸载软件的化直接使用`easy_install -m xx`卸载。  
3 安装pip (python软件的另一种管理方式)
直接通过setuptools命令安装pip `easy_install install pip`安装好后对于一些文件后缀为.whl的文件可以直接通过`pip install *.whl`如下面安装的numpy和matplotlib 就是通过pip安装的。  

### 2 安装numpy

NumPy是一个定义了数值数组和矩阵类型和它们的基本运算的语言扩展。在很多科学计算中都会用到这个模块，exe的下载地址如下[https://sourceforge.net/projects/numpy/files/](https://sourceforge.net/projects/numpy/files/ "https://sourceforge.net/projects/numpy/files/")，pypi的下载地址如下[https://pypi.python.org/pypi/numpy](https://pypi.python.org/pypi/numpy "https://pypi.python.org/pypi/numpy")选择一种，如选择下载`numpy-1.12.1rc1-cp34-none-win_amd64.whl (md5, pgp)`,可以直接通过pip安装，`pip install numpy-1.12.1rc1-cp34-none-win_amd64.whl`即可。  

### 3 安装matplotlib
matplotlib是python最著名的绘图库，它提供了一整套和matlab相似的命令API，十分适合交互式地行制图。而且也可以方便地将它作为绘图控件，嵌入GUI应用程序中。  
在Linux下比较著名的数据图工具还有gnuplot，这个是免费的，Python有一个包可以调用gnuplot，但是语法比较不习惯，而且画图质量不高。  
而 Matplotlib则比较强：Matlab的语法、python语言、latex的画图质量（还可以使用内嵌的latex引擎绘制的数学公式）  
它的文档相当完备，并且Gallery页面中有上百幅缩略图，打开之后都有源程序。因此如果你需要绘制某种类型的图，只需要在这个页面中浏览/复制/粘贴一下，基本上都能搞定，安装下载地址在[https://pypi.python.org/pypi/matplotlib](https://pypi.python.org/pypi/matplotlib "https://pypi.python.org/pypi/matplotlib")下载`matplotlib-2.0.0-cp27-cp27m-win_amd64.whl (md5)`，直接通过pip方式安装`pip install matplotlib-2.0.0-cp27-cp27m-win_amd64.whl`即可完成模块的安装。  

### 4 安装scipy  

SciPy包含的模块有最优化、线性代数、积分、插值、特殊函数、快速傅里叶变换、信号处理和图像处理、常微分方程求解和其他科学与工程中常用的计算。与其功能相类似的软件还有MATLAB、GNU Octave和Scilab。由于scipy对win 64位支持的不是很好比较难找到资源，现在提供一个下载的链接[https://osf.io/64ywn/](https://osf.io/64ywn/ "https://osf.io/64ywn/")可以下载。下载后通过pip 安装。  

### 5 安装scikit-learn  
scikit-learn是Python的一个开源机器学习模块，它建立在NumPy，SciPy和matplotlib模块之上能够为用户提供各种机器学习算法接口，可以让用户简单、高效地进行数据挖掘和数据分析  
下载地址[https://pypi.python.org/pypi/scikit-learn/0.18.1](https://pypi.python.org/pypi/scikit-learn/0.18.1 "https://pypi.python.org/pypi/scikit-learn/0.18.1")下载scikit_learn-0.18.1-cp27-cp27m-win_amd64.whl通过pip 安装。  

### 5 一个简单的例子  

scikit-learn 使用KNN分类器分类实验。代码如下：
    
    import matplotlib.pyplot as plt
    import numpy as np
    from sklearn.datasets import load_iris
    from sklearn.model_selection import train_test_split
    from sklearn.neighbors import KNeighborsClassifier
    iris = load_iris()
    X_train, X_test, y_train, y_test = train_test_split(iris['data'], iris['target'], random_state=0)
    knn = KNeighborsClassifier(n_neighbors=1)
    knn.fit(X_train, y_train)
    X_new = np.array([[5, 2.9, 1, 0.2]])
    prediction = knn.predict(X_new)
    y_pred = knn.predict(X_test)
    print y_pred == y_test
    print (np.mean(y_pred == y_test))
    print (iris['target_names'][prediction])

    
还有一些比较好的例子请参考：  
1 [http://www.hareric.com/2016/05/22/scikit-learn%E7%9A%84%E5%AE%89%E8%A3%85%E5%92%8C%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/](http://www.hareric.com/2016/05/22/scikit-learn%E7%9A%84%E5%AE%89%E8%A3%85%E5%92%8C%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/)  

2 [http://blog.csdn.net/dream_angel_z/article/details/46972669](http://blog.csdn.net/dream_angel_z/article/details/46972669 "http://blog.csdn.net/dream_angel_z/article/details/46972669")  

3 [http://www.cnblogs.com/taceywong/p/4568806.html](http://www.cnblogs.com/taceywong/p/4568806.html "http://www.cnblogs.com/taceywong/p/4568806.html")  

### 参考文献

[https://zh.wikipedia.org/wiki/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0](https://zh.wikipedia.org/wiki/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0)  
[https://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E6%8C%96%E6%8E%98](https://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E6%8C%96%E6%8E%98)  
[http://www.cnblogs.com/wei-li/archive/2012/05/23/2506940.html](http://www.cnblogs.com/wei-li/archive/2012/05/23/2506940.html "http://www.cnblogs.com/wei-li/archive/2012/05/23/2506940.html")  

 