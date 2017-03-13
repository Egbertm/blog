---
layout: post
title:  "机器学习之scikit-learn（二)"
date:   2017-3-11 23:06:05
categories: 算法
tags:  算法 bayes
---
* content  
{:toc}  

大数据与机器学习的概念越来越火了，现在越来越多的学者和企业都开始涉足这个行业，很多企业都想通过大数据以及机器学习的方式提高自身企业的利润。当年奈飞公司为了找到一种合适的算法精准的推荐影片各用户，在全球发起了一场数据挖掘竞赛，最后一个团队以一种奇异矩阵分解的方式解决的这一问题，而获得了巨额的奖金。




在国内阿里有个大数据的天池竞赛，面向的也是数据分析以及机器学习方面的学者，希望通过这种实践环节找到一些数据挖掘方面的人才。大数据技术离不开一些机器学习的算法，机器学习算法的实现语言目前比较火有R和Python，这两种是提供给数据科学家的最常用的两种工具。每一个工具都有其优缺点，诸如在python下有个很出名的机器学习模块Scikit-Learn，这个模块已经实现了很多机器学习的算法，同时提供了丰富的文档介绍，数据科学可以很方便的使用这些软件分析数据。下面介绍机器学习中的一些比较常用的挖掘过程  

### 1 数据加载

首先，数据要被加载到内存中，才能对其操作。Scikit-Learn库在它的实现用使用了NumPy数组，所以我们将用NumPy来加载*.csv文件或者是.txt文件。比如我们从UCI Machine Learning Repository下载其中一个数据集。  
``` python

    import numpy as np
    import urllib
    # url with dataset
    url = "http://archive.ics.uci.edu/ml/machine-learning-databases/pima-indians-diabetes/pima-indians-diabetes.data"
    # download the file
    raw_data = urllib.urlopen(url)
    # load the CSV file as a numpy matrix
    dataset = np.loadtxt(raw_data, delimiter=",")
    # separate the data from the target attributes
    X = dataset[:,0:7]
    y = dataset[:,8]
``` 

我们将在下面所有的例子里使用这个数据组，换言之，其中X是一个条件属性构成的决策系统，y我们通常称之为决策系统。

### 2 数据标准化

我们都知道大多数的梯度方法对于数据的缩放很敏感。因此，在运行算法之前，我们应该进行标准化，或所谓的规格化。标准化包括替换所有特征的名义值，让它们每一个的值在0和1之间。而对于规格化，它包括数据的预处理，使得每个特征的值有0和1的离差。Scikit-Learn库已经为其提供了相应的函数。数据归一化一般通过以下公式即可：  

$$x_i=2\frac{x_i-min\left(x_i\right)}{max\left(x_i\right)-min\left(x_i\right)}-1$$

``` python 
    
    from sklearn import preprocessing
    # normalize the data attributes
    normalized_X = preprocessing.normalize(X)
    # standardize the data attributes
    standardized_X = preprocessing.scale(X)
``` 

### 3 特征的选取 

特征提取最后的提取的特征是用于表达整个知识系统，如图片的特征用于表达整个图片，文本的特征代表整个文本等。有时候更多的是靠直觉和专业的知识，但对于特征的选取，已经有很多的算法可供直接使用。如树算法就可以计算特征的信息量。基于互信息提取特征，基于TD-FID提取特征等等，特征提取的好坏直接影响到后面的一切工作。  

``` python 
    
    from sklearn import metrics
    from sklearn.ensemble import ExtraTreesClassifier
    model = ExtraTreesClassifier()
    model.fit(X, y)
    # display the relative importance of each attribute
    print(model.feature_importances_)  
```

### 4 算法的开发

Scikit-Learn库已经实现了很多的基本机器学习的算法。下面介绍一下比较常用的机器学习算法。  

#### 4.1 逻辑回归  

大多数情况下被用来解决分类问题（二元分类），但多类的分类（所谓的一对多方法）也适用。这个算法的优点是对于每一个输出的对象都有一个对应类别的概率。  

``` python 
    
    from sklearn import metrics
    from sklearn.linear_model import LogisticRegression
    model = LogisticRegression()
    model.fit(X, y)
    print(model)
    # make predictions
    expected = y
    predicted = model.predict(X)
    # summarize the fit of the model
    print(metrics.classification_report(expected, predicted))
    print(metrics.confusion_matrix(expected, predicted))
```
  
#### 4.2 朴素贝叶斯　

它也是最有名的机器学习的算法之一，它的主要任务是恢复训练样本的数据分布密度。这个方法通常在多类的分类问题上表现的很好。  

``` python
    from sklearn import metrics
    from sklearn.naive_bayes import GaussianNB
    model = GaussianNB()
    model.fit(X, y)
    print(model)
    # make predictions
    expected = y
    predicted = model.predict(X)
    # summarize the fit of the model
    print(metrics.classification_report(expected, predicted))
    print(metrics.confusion_matrix(expected, predicted))
``` 

#### 4.3 k-最近邻  

kNN（k-最近邻）方法通常用于一个更复杂分类算法的一部分。例如，我们可以用它的估计值做为一个对象的特征。有时候，一个简单的kNN算法在良好选择的特征上会有很出色的表现。当参数（主要是metrics）被设置得当，这个算法在回归问题中通常表现出最好的质量。  

``` python 
    
    from sklearn import metrics
    from sklearn.neighbors import KNeighborsClassifier
    # fit a k-nearest neighbor model to the data
    model = KNeighborsClassifier()
    model.fit(X, y)
    print(model)
    # make predictions
    expected = y
    predicted = model.predict(X)
    # summarize the fit of the model
    print(metrics.classification_report(expected, predicted))
    print(metrics.confusion_matrix(expected, predicted))　　
``` 

####　4.4 决策树　　

分类和回归树（CART）经常被用于这么一类问题，在这类问题中对象有可分类的特征且被用于回归和分类问题。决策树很适用于多类分类。  

``` python 
    
    from sklearn import metrics
    from sklearn.tree import DecisionTreeClassifier
    # fit a CART model to the data
    model = DecisionTreeClassifier()
    model.fit(X, y)
    print(model)
    # make predictions
    expected = y
    predicted = model.predict(X)
    # summarize the fit of the model
    print(metrics.classification_report(expected, predicted))
    print(metrics.confusion_matrix(expected, predicted))
``` 

#### 4.5 支持向量机

SVM（支持向量机）是最流行的机器学习算法之一，它主要用于分类问题。同样也用于逻辑回归，SVM在一对多方法的帮助下可以实现多类分类。  
``` python 
    
    from sklearn import metrics
    from sklearn.svm import SVC
    # fit a SVM model to the data
    model = SVC()
    model.fit(X, y)
    print(model)
    # make predictions
    expected = y
    predicted = model.predict(X)
    # summarize the fit of the model
    print(metrics.classification_report(expected, predicted))
    print(metrics.confusion_matrix(expected, predicted))  
``` 

除了分类和回归问题，Scikit-Learn还有海量的更复杂的算法，包括了聚类， 以及建立混合算法的实现技术，如Bagging和Boosting等。

#### 5 如何优化算法的参数

在编写高效的算法的过程中最难的步骤之一就是正确参数的选择。一般来说如果有经验的话会容易些，但无论如何，我们都得寻找。幸运的是Scikit-Learn提供了很多函数来帮助解决这个问题。  

作为一个例子，我们来看一下规则化参数的选择，在其中不少数值被相继搜索了：  
``` python 
    
    import numpy as np
    from sklearn.linear_model import Ridge
    from sklearn.grid_search import GridSearchCV
    # prepare a range of alpha values to test
    alphas = np.array([1,0.1,0.01,0.001,0.0001,0])
    # create and fit a ridge regression model, testing each alpha
    model = Ridge()
    grid = GridSearchCV(estimator=model, param_grid=dict(alpha=alphas))
    grid.fit(X, y)
    print(grid)
    # summarize the results of the grid search
    print(grid.best_score_)
    print(grid.best_estimator_.alpha)
``` 

有时候随机地从既定的范围内选取一个参数更为高效，估计在这个参数下算法的质量，然后选出最好的。

``` python 
    
    import numpy as np
    from scipy.stats import uniform as sp_rand
    from sklearn.linear_model import Ridge
    from sklearn.grid_search import RandomizedSearchCV
    # prepare a uniform distribution to sample for the alpha parameter
    param_grid = {'alpha': sp_rand()}
    # create and fit a ridge regression model, testing random alpha values
    model = Ridge()
    rsearch = RandomizedSearchCV(estimator=model, param_distributions=param_grid, n_iter=100)
    rsearch.fit(X, y)
    print(rsearch)
    # summarize the results of the random parameter search
    print(rsearch.best_score_)
    print(rsearch.best_estimator_.alpha)  
```  
  
数据挖掘包括的过程大致如以上的几个过程，数据预处理（数据的清洗，噪声数据处理，数据归一化，数据离散化等），数据特征提取，数据处理（分类聚类分析，回归分析等），数据的可视化，（结果的图显示如matplotlib绘图等等）。

### 参考文献  
[http://python.jobbole.com/86910/](http://python.jobbole.com/86910/ "http://python.jobbole.com/86910/")  

[http://scikit-learn.org/stable/documentation.html](http://scikit-learn.org/stable/documentation.html "http://scikit-learn.org/stable/documentation.html")  