---
layout: post
title:  "## Hadoop yarn及相关参数介绍"
date:   2017-3-19 23:06:05
categories: 大数据
tags:  大数据 Hadoop
---
* content  
{:toc}  

网上的关于hadoop0.20.0及以前的框架主要存在以下的一些问题：
1. JobTracker 是 Map-reduce 的集中处理点，存在单点故障。  
2. JobTracker 完成了太多的任务，造成了过多的资源消耗，当 map-reduce job 非常多的时候，会造成很大的内存开销，潜在来说，也增加了 JobTracker fail 的风险，这也是业界普遍总结出老 Hadoop 的 Map-Reduce 只能支持 4000 节点主机的上限。  




3. 在 TaskTracker 端，以 map/reduce task 的数目作为资源的表示过于简单，没有考虑到 cpu/ 内存的占用情况，如果两个大内存消耗的 task 被调度到了一块，很容易出现 OOM。  
4. 在 TaskTracker 端，把资源强制划分为 map task slot 和 reduce task slot, 如果当系统中只有 map task 或者只有 reduce task 的时候，会造成资源的浪费，也就是前面提过的集群资源利用的问题。  
5. 源代码层面分析的时候，会发现代码非常的难读，常常因为一个 class 做了太多的事情，代码量达 3000 多行，造成 class 的任务不清晰，增加 bug 修复和版本维护的难度。  
6. 从操作的角度来看，现在的 Hadoop MapReduce 框架在有任何重要的或者不重要的变化 ( 例如 bug 修复，性能提升和特性化 ) 时，都会强制进行系统级别的升级更新。更糟的是，它不管用户的喜好，强制让分布式集群系统的每一个用户端同时更新。这些更新会让用户为了验证他们之前的应用程序是不是适用新的 Hadoop 版本而浪费大量时间。  
为从根本上解决旧 MapReduce 框架的性能瓶颈，促进 Hadoop 框架的更长远发展，从 0.23.0 版本开始，Hadoop 的 MapReduce 框架完全重构，发生了根本的变化。新的 Hadoop MapReduce 框架命名为 MapReduceV2 或者叫 Yarn，其架构图如下：
![https://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop-yarn/images/image002.jpg](https://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop-yarn/images/image002.jpg)  
### Hadoop yarn介绍  

**yarn** 主要是将MR1中的资源管理和作业调度分开，分别用ResourceManger和ApplicationMaster进程来实现。
1. ResourceManager 负责整个集群的资源调度。
2. ApplicationMaster：负责应用相关的事务，比如任务的调度，任务的监控等。  
   
yarn作业的运行有以下几个步骤：  

1 作业的提交  
client 向整个集群提交MapReduce作业。此过程有新的Job Id由资源管理器产生。作业的核实输出，计算输入的split的大小。将作业的jar,配置文件，split的信息拷贝到HDFS中。最后调用资源管理器submitApplication（）来提交作业。    
2 作业的初始化  
当作业管理器收到submitApplication（）请求是就将作业请求发送给调度器scheduler,调度器分配container,然后资源管理器在该container内启动应用管理器进程，由节点管理器监控。
 MapReduce作业的应用管理器是一个主类为MRAppMaster的Java应用. 其通过创造一些bookkeeping对象来监控作业的进度, 得到任务的进度和完成报告(第6步). 然后其通过分布式文件系统得到由客户端计算好的输入split(第7步). 然后为每个输入split创建一个map任务, 根据mapreduce.job.reduces创建reduce任务对象.  

3.任务分配  

如果作业很小, 应用管理器会选择在其自己的JVM中运行任务。如果不是小作业, 那么应用管理器向资源管理器请求container来运行所有的map和reduce任务. 这些请求是通过心跳来传输的, 包括每个map任务的数据位置, 比如存放输入split的主机名和机架(rack). 调度器利用这些信息来调度任务, 尽量将任务分配给存储数据的节点, 或者分配给和存放输入split的节点相同机架的节点.  

4.任务运行  

当一个任务由资源管理器的调度器分配给一个container后, 应用管理器通过联系节点管理器来启动container. 任务由一个主类为YarnChild的Java应用执行. 在运行任务之前首先本地化任务需要的资源, 比如作业配置, JAR文件, 以及分布式缓存的所有文件. 最后, 运行map或reduce任务.YarnChild运行在一个专用的JVM中, 但是YARN不支持JVM重用.  

5 进度和状态更新  

YARN中的任务将其进度和状态(包括counter)返回给应用管理器, 客户端每秒(通过mapreduce.client.progressmonitor.pollinterval设置)向应用管理器请求进度更新, 展示给用户。  

6 作业完成  

除了向应用管理器请求作业进度外, 客户端每5分钟都会通过调用waitForCompletion()来检查作业是否完成. 时间间隔可以通过mapreduce.client.completion.pollinterval来设置. 作业完成之后, 应用管理器和container会清理工作状态, OutputCommiter的作业清理方法也会被调用. 作业的信息会被作业历史服务器存储以备之后用户核查.  

如下图所示：  
![http://images2015.cnblogs.com/blog/615800/201605/615800-20160514160217187-40278054.png](http://images2015.cnblogs.com/blog/615800/201605/615800-20160514160217187-40278054.png)  

### Hadoop 参数配置  
Hadoop中yarn作为资源管理器，在底层控制调配资源。yarn中资源的最小单位是container。一个container可以类似的认为是一运行的JVM。

**yarn** 中的配置参数主要有：  
yarn-node-manager.resource.memory-mb=12288*0.8主要是自己的物理内存为12GB,不能全部用完需要留一些给操作系统使用。

yarn.scheduler.minimum-allocation-mb:每个container允许分配的最小单元的内存。当应用程序向Resourcemanager申请资源的时候（即申请container时），RM它分配给container是按照一个最小单位进行分配的。 例如， 我们设置分配的最小单位为4GB， 则RM分配出来的container的内存一定是4G的倍数。  假设现在有一个程序向RM申请 5.1G的内存， 则RM会分配给它一个8GB的container去执行。  
在实际执行map reduce的job中， 一个container实际上是执行一个map 或者reduce task的jvm的进程。 那么这个jvm在执行中会不断的请求内存，假设它的物理内存或虚拟内存占用超出了container的内存设定， 则node manager 会主动的把这个进程kill 掉。  这里需要澄清一点，JVM使用的内存实际上分为虚拟内存和物理内存。JVM中所有存在内存中的对象都是虚拟内存， 但在实际运行中只有一部分是实际加载在物理内存中的。 因此在设置mapreduce的task的 jvm opts 参数时， 应将heap size 设置的比container允许的最大虚拟内存小。 这样jvm 不会因为申请过多的内存而被node manager 强制关闭。 当然设置最大heap size 如果在执行中被超过， jvm就会报 OutOfMemoryException。同时还有一个参数yarn.scheduler.maximum-allocation-mb，设定了RM可以分配的最大的container是多大。   假设应用程序向RM申请的资源超过了这个值， RM会直接拒绝这个请求。  

**mapreduce2**(mapred-site.xml中)参数配置：  

mapreduce2 是hadoop 上实际执行计算的引擎。 因此我们在前面所说的“应用程序”向yarn请求资源， 这里应用程序就是指mapreduce2 这个引擎。 mapreduce2 在逻辑上有几个概念： 一个job， 一个由app master 以及若干个 map task 和reduce task组成。  app master 负责向 Yarn的RM申请资源， 并将map 和reduce task 送到分配到的container去执行。mapreduce中可以设定 map task的默认使用的虚拟内存。  这个值是app master 在向 yarn申请用于map task的container时会使用的内存值如：  

mapreudce.map.memory.mb=8192  

mapreudce.reduce.memory.mb=8192  

这个值是全局的， 这里可以看到mapreduce并不是很智能的， 他不知道某个job的map task需要使用多大的内存。 这个值应该根据集群大多数任务的特征来设定。  但是在我们执行一些需要很大内存的job的时候，可以在提交job时手工的overwrite这些参数。

另外，mapreduce2中还可以设置每个map、reduce task的子jvm进程的heapsize。  这个值应该比上面的task的虚拟内存值小（因为jvm除了heap还有别的对象需要占用内存）， 如果jvm进程在执行中heap上的对象占用内存超过这个值， 则会抛出OutOfMemory Exception，如下：

mapreduce.map.Java.opts=-Xmx7129m

mapreduce.reduce.java.opts=-Xmx7129m

### 符常用参数  
注意，配置这些参数前，应充分理解这几个参数的含义，以防止误配给集群带来的隐患。另外，这些参数均需要在yarn-site.xml中配置。  

　　1.    ResourceManager相关配置参数  

　　（1） yarn.resourcemanager.address  

　　参数解释：ResourceManager 对客户端暴露的地址。客户端通过该地址向RM提交应用程序，杀死应用程序等。  

　　默认值：${yarn.resourcemanager.hostname}:8032  

　　（2） yarn.resourcemanager.scheduler.address  

　　参数解释：ResourceManager 对ApplicationMaster暴露的访问地址。ApplicationMaster通过该地址向RM申请资源、释放资源等。  

　　默认值：${yarn.resourcemanager.hostname}:8030  

　　（3） yarn.resourcemanager.resource-tracker.address  

　　参数解释：ResourceManager 对NodeManager暴露的地址.。NodeManager通过该地址向RM汇报心跳，领取任务等。  

　　默认值：${yarn.resourcemanager.hostname}:8031  

　　（4） yarn.resourcemanager.admin.address  

　　参数解释：ResourceManager 对管理员暴露的访问地址。管理员通过该地址向RM发送管理命令等。  

　　默认值：${yarn.resourcemanager.hostname}:8033  

　　（5） yarn.resourcemanager.webapp.address  

　　参数解释：ResourceManager对外web ui地址。用户可通过该地址在浏览器中查看集群各类信息。  

　　默认值：${yarn.resourcemanager.hostname}:8088  

　　（6） yarn.resourcemanager.scheduler.class  

　　参数解释：启用的资源调度器主类。目前可用的有FIFO、Capacity Scheduler和Fair Scheduler。  

　　默认值：

　　org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler  

　　（7） yarn.resourcemanager.resource-tracker.client.thread-count  

　　参数解释：处理来自NodeManager的RPC请求的Handler数目。  

　　默认值：50  

　　（8） yarn.resourcemanager.scheduler.client.thread-count  

　　参数解释：处理来自ApplicationMaster的RPC请求的Handler数目。  

　　默认值：50  

　　（9） yarn.scheduler.minimum-allocation-mb/ yarn.scheduler.maximum-allocation-mb  

　　参数解释：单个可申请的最小/最大内存资源量。比如设置为1024和3072，则运行MapRedce作业时，每个Task最少可申请1024MB内存，最多可申请3072MB内存。  

　　默认值：1024/8192  

　　（10） yarn.scheduler.minimum-allocation-vcores / yarn.scheduler.maximum-allocation-vcores  

　　参数解释：单个可申请的最小/最大虚拟CPU个数。比如设置为1和4，则运行MapRedce作业时，每个Task最少可申请1个虚拟CPU，最多可申请4个虚拟CPU。什么是虚拟CPU，可阅读我的这篇文章：“YARN 资源调度器剖析”。  

　　默认值：1/32  

　　（11） yarn.resourcemanager.nodes.include-path /yarn.resourcemanager.nodes.exclude-path  

　　参数解释：NodeManager黑白名单。如果发现若干个NodeManager存在问题，比如故障率很高，任务运行失败率高，则可以将之加入黑名单中。注意，这两个配置参数可以动态生效。（调用一个refresh命令即可）  

　　默认值：“”  

　　（12） yarn.resourcemanager.nodemanagers.heartbeat-interval-ms

　　参数解释：NodeManager心跳间隔  

　　默认值：1000（毫秒）  

　　2. NodeManager相关配置参数  

　　（1） yarn.nodemanager.resource.memory-mb  

　　参数解释：NodeManager总的可用物理内存。注意，该参数是不可修改的，一旦设置，整个运行过程中不 可动态修改。另外，该参数的默认值是8192MB，即使你的机器内存不够8192MB，YARN也会按照这些内存来使用（傻不傻？），因此，这个值通过一 定要配置。不过，Apache已经正在尝试将该参数做成可动态修改的。  

　　默认值：8192  

　　（2） yarn.nodemanager.vmem-pmem-ratio  

　　参数解释：每使用1MB物理内存，最多可用的虚拟内存数。  

　　默认值：2.1  

　　（3） yarn.nodemanager.resource.cpu-vcores  

　　参数解释：NodeManager总的可用虚拟CPU个数。  

　　默认值：8  

　　（4） yarn.nodemanager.local-dirs  

　　参数解释：中间结果存放位置，类似于1.0中的mapred.local.dir。注意，这个参数通常会配置多个目录，已分摊磁盘IO负载。  

　　默认值：${hadoop.tmp.dir}/nm-local-dir  

　　（5） yarn.nodemanager.log-dirs  

　　参数解释：日志存放地址（可配置多个目录）。  

　　默认值：${yarn.log.dir}/userlogs  

　　（6） yarn.nodemanager.log.retain-seconds  

　　参数解释：NodeManager上日志最多存放时间（不启用日志聚集功能时有效） 。  

　　默认值：10800（3小时）  

　　（7） yarn.nodemanager.aux-services  

　　参数解释：NodeManager上运行的附属服务。需配置成mapreduce_shuffle，才可运行MapReduce程序  

　　默认值：“”  

### 参考文献

[http://www.thebigdata.cn/Hadoop/9572.html](http://www.thebigdata.cn/Hadoop/9572.html "http://www.thebigdata.cn/Hadoop/9572.html")
[http://www.cnblogs.com/codeOfLife/p/5492740.html](http://www.cnblogs.com/codeOfLife/p/5492740.html "http://www.cnblogs.com/codeOfLife/p/5492740.html")  

[http://blog.csdn.net/tiimfei/article/details/46818257](http://blog.csdn.net/tiimfei/article/details/46818257 "http://blog.csdn.net/tiimfei/article/details/46818257")