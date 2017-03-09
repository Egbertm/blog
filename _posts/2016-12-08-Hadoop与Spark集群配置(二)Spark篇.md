---
layout: post
title:  "Hadoop与Spark集群配置(二)Spark篇"
date:   2016-12-03 23:06:05
categories: 大数据
tags:  大数据 spark
---
* content  
{:toc}  

这篇准备介绍如何在hadoop分布式集群中怎么集成spark环境，有文献称spark的运行效率是Hadoop 100倍，这个数字有点吓人啊，不过spark是基于内存的计算框架跟Hadoop不具有可比性，应该说spark同mapreduce 计算框架相比。
### 采用yarn部署安装spark  
  
下载spark（对应于hadoop的版本2.7）  
  
    su hadoop #登录hadoop用户
    cd /download #进入download目录
    wget http://mirror.bit.edu.cn/apache/spark/spark-2.0.1/spark-2.0.1-bin-hadoop2.7.tgz
    tar -zxvf spark-2.0.1-bin-hadoop2.7.tgz -C /softwareinstall #解压到指定目录
    mv spark-2.0.1-bin-hadoop2.7 spark #重命名spark
  
配置spark启动参数  
  
    su hadoop #登录hadoop用户
    cd /softwareinstall/spark
    cd ./conf #进入配置目录
    cp spark-env.sh.template spark-env.sh #复制模板文件
    vim spark-env.sh
    #添加以下东西
    export SPARK_DIST_CLASSPATH=$(/softwareinstall/hadoop/bin/hadoop classpath)
    cp slaves.template slaves #复制模板文件
    vim slaves
    #把slaves文件中的localhost注释掉
    slave1 #加入节点
    cd /softwareinstall #进入安装目录
    tar .zxvf spark.tar.gz ./spark #压缩文件
    scp spark.tar.gz hadoop@slave1:/softwareinstall #分发到从节点
    #进入从节点用hadoop用户登录解压缩安装目录里的文件
    
spark启动
  
    #启动spark
    cd /softwareinstall/spark/sbin #进入启动目录
    ./start-all.sh #./不能少否者会执行hadoop下的start-all.sh 启动hadoop而不是spark
    start-all.sh 启动Hadoop下的所有组件包括（hdfs,yarn等）
    #master节点下jps命令可以看到如下进程。
    [hadoop@master conf]$ jps
    12182 Jps
    11080 NameNode
    10606 Master
    11522 ResourceManager
    11299 SecondaryNameNode
    #slave1节点下jps命令可以有如下进程
    [hadoop@slave1 ~]$ jps
    6875 Worker
    8164 Jps
    7491 DataNode
    7626 NodeManager
  
进程图如下：  
![spark jps](http://o886hn2n8.bkt.clouddn.com//hadoop/spark%20jps.png)
  
可以通过在本地浏览器中访问节点：  
hadoop claster 域名是：master:8088  
  
spark claster 域名是：master:8080,图如下：  
![hadoop cluster](http://o886hn2n8.bkt.clouddn.com//hadoop/spark%20claster.png)  
  
![spark cluster](http://o886hn2n8.bkt.clouddn.com//hadoop/spark%20claster.png)  
  
spark例子测试  
    
    #本地模式线程运行
    [hadoop@master spark]$ ./bin/run-example  SparkPi 2>&1|grep "Pi is roughly"
    #结果如下
    Pi is roughly 3.135435677178386
    #Spark on YARN 集群上 yarn-cluster 模式运行
    ./bin/spark-submit --class org.apache.spark.examples.SparkPi --master yarn-cluster \
    examples/jars/spark-examples*.jar 10  
  
结果图如下  
![application result](http://o886hn2n8.bkt.clouddn.com//hadoop/application%20result.png)  
  
![application result log](http://o886hn2n8.bkt.clouddn.com//hadoop/application%20result%20log.png)  

### 参考文献
【1】[http://wuchong.me/blog/2015/04/04/spark-on-yarn-cluster-deploy/](http://wuchong.me/blog/2015/04/04/spark-on-yarn-cluster-deploy/ "http://wuchong.me/blog/2015/04/04/spark-on-yarn-cluster-deploy/")  
【2】[http://www.powerxing.com/spark-quick-start-guide/](http://www.powerxing.com/spark-quick-start-guide/ "http://www.powerxing.com/spark-quick-start-guide/")
【3】[http://blog.csdn.net/cenwenchu79/article/details/2847529](http://blog.csdn.net/cenwenchu79/article/details/2847529 "http://blog.csdn.net/cenwenchu79/article/details/2847529")