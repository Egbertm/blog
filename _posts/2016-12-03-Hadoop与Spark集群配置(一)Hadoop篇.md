---
layout: post
title:  "Hadoop与Spark集群配置(一)Hadoop篇"
date:   2016-12-03 23:06:05
categories: 大数据
tags:  大数据 hadoop
---
* content  
{:toc}  

实验室的设备已经来了一段时间了，还没有把大数据集群环境给搭建起来，接下就把一些大数据需要用到的环境先搭建起来。先搭建hadoop,hadoop是apache基金组织里的一个顶级项目。是一款支持数据密集型分布式应用程序并以Apache 2.0许可协议发布的开源软件框架。它支持在商品硬件构建的大型集群上运行的应用程序。Hadoop是根据谷歌公司发表的MapReduce和Google文件系统的论文自行实现而成。  




### 1 主服务器（master）  

 **1.创建用户**  
  
创建了用户admin用的命令是：  

    
    useradd admin  #创建用户admin命令
    passwd admin   #为用户添加密码（本服务器admin用户的密码是admin）
    
  
 创建用户hadoop：  
    
    useradd hadoop  #创建用户hadoop的命令  
    passwd hadoop   #为hadoop用户添加密码（本服务器的hadoop用户的密码是hadoop）
  
 **2.安装ssh服务**  
  
安装ssh软件

    
    rpm -qa|grep ssh #查看ssh软件是否安装好了，一般CentOS服务会为用户安装openssh 服务。
    yum list openssh #也是查看ssh软件是否安装。
  
如果没有安装可以用CentOS yum命令安装  
  
    yum install openssh #yum命令安装ssh
  
安装后需要开启ssh服务需要切换到root用户操作  
    
    service sshd start #开启SSH服务
    service sshd status #查看ssh服务是否开启状态
    
  
**3.安装python**  
  
查看原服务器是否安装了pyhton，如果是pyhton2.6则升级python到pyhton2.7  

    
    yum list python # 查看是否安装了python
    
  
由于本服务器已经安装了pyhton2.6需要对它升级。一般在Linux下升级软件可以用yum update ***对于python的升级方式我们采用手动升级的方式。  
  
1.下载前可以先创建一个临时的下载目录  
    
    cd ~ #进入root 目录
    mkdir temp #在root目录下创建了一temp目录
    
2.下载相应的python包，本服务器下载的是python-2.11  

    
    wget https://www.python.org/ftp/python/2.7.11/Python-2.7.11.tgz
 

3.解压Python压缩包  

    
    tar -zxvf Python-2.7.11.tgz #解压安装包
    ls -l #查看目录下的文件
    
4.安装python  

    
    cd Python-2.7.11
    ./configure --prefix=/usr/local #/usr/local 为安装的目录
    make
    make altinstall #检查python的版本并安装
    cd /usr/local/python2.7
    ls -l 查看安装目录下的文件
  
创建软连接，由于原来的系统已经自带了一个python2.6的版本，如果在终端输入命令  

    
    python --version #这个时候还是原来的版本
    mv /usr/bin/python /usr/bin/python2.6.6 #备份原来的pyhton版本
    ln -s /usr/local/bin/pyhton2.7 /usr/bin/pyhton #创建软连接
    python --version #应该显示的是pyhton2.7.11了
    
  
安装setuptools  
  
    cd /root/temp 进入temp临时下载目录
    wget https://pypi.python.org/packages/source/s/setuptools/setuptools-1.4.2.tar.gz
    tar -zxvf setuptools-1.4.2
    python2.7 setup.py install #安装setuptools
    easy_install --version #查看setuptools的版本
   
安装pip  
  
    
    curl https://bootstrap.pypa.io/get-pip.py|python2.7 #直接安装
    pip --version #查看pip的版本
    
  
5.修改yum(更新后的python版本导致原来的yum不能使用)  
    
    which yum #查找yum安装路径
    vim /usr/bin/yum #修改yum中的python，将第一行的#！/usr/bin/pyhton 改为 #!/usr/bin/pyhton/2.6 应该可以用了
    
至此pyhton环境也升级完了以上的过程在从服务器上(slave1)实现同样的操作  

  
### 2 hadoop分布式环境搭建

**环境搭建前的准备**  
  
**1.关闭iptable**  
    
    service iptables stop #关闭FireWall
    service iptables status #检查Firewall是否关闭
    chkconfig iptables off #开机自启动也关闭
  
**2.关闭selinux**  
    
    /usr/sbin/sestatus -v #查看selinux状态
    setenforce 0 #临时关闭SELinux
    vim /etc/selinux/config #SELINUX=enforcing 改为disabled(需要重启)
    
**3.静态ip配置（master节点）**  

**master节点**  
    
    locate ifcfg-eth0 #查找ifcfg-eth0 文件路径
    cd /etc/sysconfig/network-scripts #网络配置的目录
    ls -l ifcfg-eth* #查看有几个配置文件，一般一个文件对应一个网络端口
    vim ifcfg-eth1 #本服务器选择eth1作为配置静态ip的网络端口
    #在里面加入以下内容（有些内容非必须的）
    DEVICE=eth1
    BOOTPROTO=static
    #DHCP_HOSTNAME=node
    HWADDR=38:D5:47:02:5D:34
    NM_CONTROLLED=yes
    ONBOOT=yes
    TYPE=Ethernet
    UUID="2e0aba0e-2f42-4319-8d58-a9fedcc5b04f"
    IPADDR=192.168.1.100
    NETMASK=255.255.255.0
    BROADCAST=192.168.1.255
    ONBOOT=yes
    NETWORK=192.168.1.0
    GATEWAY=192.168.1.1
    DNS1=192.168.1.1
    DNS2=8.8.8.8
    IPV6INIT=yes
    IPV6_AUTOCONF=yes
    USERCTL=no


**slave1节点**  
  
    vim ifcfg-eth1 #本服务器选择eth1作为配置静态ip的网络端口
    #在里面加入以下内容
    DEVICE="eth1"
    BOOTPROTO="static"
    IPADDR="192.168.1.101"
    NETWORK="192.168.1.0"
    GATEWAY="192.168.1.1"
    DNS1="192.168.1.1"
    DNS2="8.8.8.8"
    BOADCAST="192.168.1.255"
    NETMASK="255.255.255.0"
    DHCP_HOSTNAME="node"
    HWADDR="38:D5:47:02:5D:E4"
    NM_CONTROLLED="yes"
    ONBOOT="yes"
    TYPE="Ethernet"
    UUID="95533e2c-7ad9-41d8-bcaf-e013181863dd"
  
检查是否在同一个网段下，分别在master和slave1下进行检测  
    
    ping 192.168.1.101 #master ping slave1 能ping同则在一个局域网内
    ping 192.168.1.100 #slave1 ping master
    ping www.baidu.com -c 3 #查看是否能够访问外网
  


    
**3.修改主机名**  
    
    vim /etc/sysconfig/network #修改hostname=master(主节点为master 从节点为slave*)
    
  
**4.修改对应得映射**  
  
    vim /etc/hosts
    #添加以下内容（master节点和slave1节点添加的内容一样）  
    192.168.1.100 master
    192.168.1.101 slave1
  
环境搭建前的一些准备工作已经完成。  
  
**搭建分布式Hadoop环境**  

**1.创建hadoop用户**
    
    useradd hadoop #创建用户
    passwd hadoop #设置Hadoop用户密码（本例为hadoop）
    
  
**2.修改hadoop权限**
    
    visudo #编辑权限文件
    #在root    ALL=(ALL)       ALL 下面添加以下内容
    hadoop  ALL=(ALL)       ALL
    #为的是能够在Hadoop用户下用执行具有管理员权限才能够执行的命令  
  
**3.无密码登录**
  
前面已经装了openssh服务（不多介绍）  

    
    su hadoop #进入Hadoop用户（有密码需要输入密码才能登录，root用户下直接可以登录）
    #每个新建的用户在/home路径下都有一个以用户名命名的文件夹目录。
    cd ~ #这个命令是进入当前用户的根目录（root用户为/root,hadoop 用户为/home/hadoop）
    pwd #显示当前的路径为/home/hadoop
    ls -al #查看当前目录下的所有文件包括隐藏文件
    #查看是否有.ssh文件夹。
    mkdir .ssh #没有创建.ssh文件夹
    cd .ssh #进入.ssh目录
    ssh-keygen -t rsa #一路enter（3下）
    #当前目录下生成id_rsa,id_rsa.pub文件
    cat ./id_rsa.pub >> ./authorized_keys
    #检测ssh登录
    ssh master #master 为当前的主机名也可以用ip来设置
    #如果不能登录一般是权限问题需要修改权限
    chmod 700 /home/hadoop/.ssh
    chmod 600 /home/hadoop/.ssh/authorized_keys
    ssh master #再一次测试

  
以上过程在master节点上操作；  
在分布式环境下需要master无密码登录到slave节点（逆过程非必须的），需要把authorized_keys文件拷贝到需要控制的从节点中。  
    
    sudo scp ./.ssh/authorized_keys hadoop@slave1:/home/hadoop/.ssh
    ssh slave1 #在master节点中测试是否可以无密码登录到slave1主机中，
    #如果不行，需要修改slave1中的主机权限
  
进入slave主机修改.ssh权限（这个过程很重要）。  
    
    chmod 700 /home/hadoop/.ssh
    chmod 600 /home/hadoop/.ssh/authorized_keys
  
至此可以实现在master主机下无密码登录slave1从节点  
  
**4.Hadoop下载及配置环境变量**
  
hadoop下载（本服务器使用的是hadoop2.7.2）

    
    su hadoop #登录hadoop用户
    sudo mkdir /download #创建常用下载目录
    sudo mkdir /softwareinstall #hadoop 的安装目录
    cd /download #进入下载目录
    sudo wget http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.2/hadoop-2.7.2.tar.gz  
    #下载完后最好用md5校验一下
    tar -zxvf hadoop-2.7.2.tar.gz -C /softwareinstall #解压到安装目录
    cd /softwareinstall
    mv hadoop-2.7.2 hadoop #重命名
  
配置jdk环境变量及hadoop环境变量  
查看是否安装jdk或者是openjdk1.6(jdk版本必须为1.6以上)
    
    cd ~ #进入/home/hadoop目录下
    vim ./bashrc
    #添加以下内容
    export JAVA_HOME=/usr/lib/jvm/java-1.7.0
    export HADOOP_HOME=/softwareinstall/hadoop
    export HADOOP_INSTALL=$HADOOP_HOME
    export HADOOP_MAPRED_HOME=$HADOOP_HOME
    export HADOOP_COMMON_HOME=$HADOOP_HOME
    export HADOOP_HDFS_HOME=$HADOOP_HOME
    export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
    
  
是环境变量的配置生效
    
    source ./bashrc
    java -version #查看java 版本
    hadoop version #查看hadoop版本
  
**5.配置hadoop启动参数**  
需进入hadoop安装路径即解压的Hadoop路径下，修改5个文件  
    
    su hadoop
    cd /softwareinstall/hadoop #进入hadoop的安装目录
    cd ./etc/hadoop #进入hadoop配置文件目录
  
修改slaves文件增加节点
    
    vim slaves 
    #添加节点，一行一个
    slave1 #本例添加的节点
  
修改core-site.xml 文件如下：  
    
    <configuration>
        <property>
          <name>fs.defaultFS</name>
          <value>hdfs://master:9000</value>
        </property>
        <property>
        <name>hadoop.tmp.dir</name>
        <value>file:/softwareinstall/hadoop/tmp</value>
        <description>Abase for other temporary directories.</description>
         </property>
    </configuration>  
  
修改hdfs-site.xml 文件如下：  
    
    <configuration>
      <property>
       <name>dfs.namenode.secondary.http-address</name>
       <value>master:50090</value>
       </property>
      <property>
      <name>dfs.replication</name>
      <value>1</value>
      </property>
      <property>
      <name>dfs.namenode.name.dir</name>
      <value>file:/softwareinstall/hadoop/tmp/dfs/name</value>
      </property>
     <property>
     <name>dfs.datanode.data.dir</name>
     <value>file:/softwareinstall/hadoop/tmp/dfs/data</value>
     </property>
     </configuration>  
  
复制文件mapred-site.xml.template 并重命名为mapred-site.xml 增加以下内容：  
  
    <configuration>
        <property>
          <name>mapreduce.framework.name</name>
          <value>yarn</value>
        </property>
        <property>
          <name>mapreduce.jobhistory.address</name>
          <value>master:10020</value>
        </property>
        <property>
          <name>mapreduce.jobhistory.webapp.address</name>
          <value>master:19888</value>
          </property>
    </configuration>
  
修改yarn-site.xml 文件如下：  
  
    <configuration>
        <property>
         <name>yarn.resourcemanager.hostname</name>
         <value>master</value>
        </property>
        <property>
          <name>yarn.nodemanager.aux-services</name>
          <value>mapreduce_shuffle</value>
        </property>
    </configuration>  
  
至此hadoop的环境配置结束可以进行相应的测试。  
  
**6.复制hadoop到从节点**  
    
    cd /softwareinstall #进入hadoop安装的目录
    tar -zcvf hadoop.tar.gz hadoop #打包并压缩
    scp hadoop.tar.gz hadoop@slave1:/softwareinstall #远程复制到从节点上
    #之后的操作就是进入slave节点解压缩Hadoop文件，并配置好从节点的环境变量，  
    #对于hadoop的配置可以不用，解压缩的hadoop已经在master节点配置完了。
  

**7.测试hadoop环境**  

第一次启动需fomatenode
    
    hdfs namenode -formate
    
  
启动节点  
    
    start-dfs.sh
    start-yarn.sh
    mr-jobhistory-daemon.sh start historyserver
  
测试是否启动成功
    
    jps
    #出现以下内容为启动成功
    [hadoop@master ~]$ jps
    14944 SecondaryNameNode
    15171 ResourceManager
    14342 NameNode
    15477 JobHistoryServer
    15544 Jps
    #从节点输出以下信息
    [hadoop@slave1 softwareinstall]$ jps
    14736 DataNode
    15254 Jps
    14979 NodeManager


    
运行实例  
  
    hadoop jar /softwareinstall/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep input output 'dfs[a-z.]+'
    
查看结果  
    
    [hadoop@master ~]$ hdfs dfs -cat output/*
    #输出如下内容  
    16/12/18 17:45:59 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your
     platform... using builtin-java classes where applicable
    1       dfsadmin
    1       dfs.replication
    1       dfs.namenode.secondary.http
    1       dfs.namenode.name.dir
    1       dfs.datanode.data.dir

  
关闭节点  
  
    stop-yarn.sh
    stop-dfs.sh
    mr-jobhistory-daemon.sh stop historyserver
  
配置结束。  

### 参考文献  
【1】[http://www.cnblogs.com/freeweb/p/5157802.html](http://www.cnblogs.com/freeweb/p/5157802.html "http://www.cnblogs.com/freeweb/p/5157802.html")  
【2】[http://www.powerxing.com/spark-quick-start-guide/](http://www.powerxing.com/spark-quick-start-guide/ "http://www.powerxing.com/spark-quick-start-guide/")  
【3】[http://blog.csdn.net/cenwenchu79/article/details/2847529](http://blog.csdn.net/cenwenchu79/article/details/2847529 "http://blog.csdn.net/cenwenchu79/article/details/2847529")  