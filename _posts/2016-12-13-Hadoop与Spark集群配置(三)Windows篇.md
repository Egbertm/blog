---
layout: post
title:  "Hadoop与Spark集群配置(三)Windows篇"
date:   2016-12-13 23:06:05
categories: 大数据
tags:  大数据 hadoop
---
* content  
{:toc}  

在Linux服务器上搭建了一个分布式的集群环境，可是本地的系统用的依旧是Windows,怎么能够在windows下搭建hadoop并调试Hadoop程序，网上有两种解决办法，一种是通过在windows模拟一套Linux环境通过Cygwin这种方式有点复杂因此采用第二种方式WithOut CygWin And Vitual OS 最后实现在Eclipse 开发第一hadoop 程序  




需要准备以下环境软件：  
1. JDK6.0以上(测试用的是jdk1.8)
2. Hadoop2.x(测试用的是Hadoop2.7.2)  
3. Hadoop-eclipse-plugin2.7.2.jar(Eclipse的Hadoop插件可以在网上下到)
4. Eclipse4.6（测试Neon.1a Release4.6.1）

### 环境搭建  

一、配置环境变量  
一个jdk的环境变量，另一是hadoop的环境变量。  
Win---->计算机---->系统属性---->高级系统设置---->系统环境变量----系统变量--->新建。  
变量名：Java_Home  
变量值：本人的环境变量值为 D:\studysoft\java\jdk1.8.0_65  
变量名：CLASSPATH  
变量值：本人的环境变量值为 .;%Java_Home%\lib%Java_Home%\lib\rt.jar;%Java_Home%\lib\dt.jar;%Java_Home%\lib\tools.jar;   
找到变量名为PATH的环境变量在它的值中加入 %JAVA_HOME%\bin  
测试，进入CMD的命令行窗口，输入  
`java -version`
`java`  
`javac`  
  
下载的Hadoop.2.7.2.tar.gz 需要管理员权限解压  
变量名：HADOOP_HOME  
变量值：本人的环境变量值为 D:\studysoft\hadoop\hadoop-2.7.2  
在变量名为PATH中加入 %HADOOP_HOME%\bin  
测试，进入CMD的命令窗口，输入 `hadoop version` 查看版本号。  
![hadoopversion](http://o886hn2n8.bkt.clouddn.com//hadoop/hadooforwin/hadoopversion.png)
  
二、配置hadoop  
1.编辑hadoop的安装目录下的文件(./etc/hadoop/core-site.xml）  

    
    <configuration>
    <property>
       <name>fs.defaultFS</name>
       <value>hdfs://localhost:50080</value>
    </property>
    </configuration>  
    
2.编辑./etc/hadoop/hdfs-site.xml  

    
    <configuration>

     <property>
       <name>dfs.replication</name>
       <value>1</value>
    </property>
    <property>
       <name>dfs.namenode.name.dir</name>
       <value>/D:/hadoop/hadoop-2.7.2/data/namenode</value>#name节点
    </property>
    <property>
       <name>dfs.datanode.data.dir</name>
     <value>/D:/hadoop/hadoop-2.7.2/data/datanode</value>#数据节点
    </property>
    </configuration>
     
  
3.编辑./etc/hadoop/mapred-site.xml  
    
    <configuration>
    <property>
       <name>mapreduce.framework.name</name>
       <value>yarn</value>
    </property>
    </configuration>
  
4.编辑./etc/hadoop/yarn-site.xml  
    
    <configuration>

    <!-- Site specific YARN configuration properties -->
    <!-- Site specific YARN configuration properties -->
    <property>
       <name>yarn.nodemanager.aux-services</name>
       <value>mapreduce_shuffle</value>
    </property>
    <property>
       <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
       <value>org.apache.hadoop.mapred.ShuffleHandler</value>
    </property>
    </configuration>
  
5.编辑文件./etc/hadoop/hadoop-env.cmd
    
    @rem set JAVA_HOME=%JAVA_HOME% #注释掉原来的javahome
    set JAVA_HOME=D:\studysoft\java\jdk1.8.0_65 #添加新的JAVA_HOME
    
  
三、替换文件  
  
下载hadoopwindowns 文件，下载[地址](https://github.com/iwwenbo/hadooponwindows/archive/master.zip)，将下载下来的bin目录替换hadoop安装下的bin目录即可。
  
四、运行环境  

1. 第一次运行需要格式化namenode  
` hdfs namenode -format     #打开cmd,运行`
2. 管理员运行./sbin/start-all.cmd  
![startallcmd](http://o886hn2n8.bkt.clouddn.com//hadoop/hadooforwin/startallcmd.png)
3. 运行jps 查看服务  
  ![JPS](http://o886hn2n8.bkt.clouddn.com//hadoop/hadooforwin/jps.png)
五、WEB浏览  

master节点浏览 资源管理GUI:http://localhost:8088/ 
  
![WebMaster](http://o886hn2n8.bkt.clouddn.com//hadoop/hadooforwin/WEB_MASTER.png)
节点浏览  节点管理GUI:http://localhost:50070/  
![dfs](http://o886hn2n8.bkt.clouddn.com//hadoop/hadooforwin/dfsnodeOverview.png)

六、基本的操作  
  
1. 创建目录  
`hadoop fs -mkdir -p /user/input #创建的地址在hdfs://localhost:50080/user/input`  
2. 上传文件  
`hadoop fs -put D:/hadoop/input/* /user/input  #D:/hadoop/input/*上传到/user/input`
3. 查看文件数  
`hadoop fs -ls /user/input`
4. 删除文件
`hadoop fs -rm /user/input/**.txt #删除文件`
5. 删除文件夹
`hadoop fs -rmr /user/output #删除文件夹`
6. WordCount例子运行  
`hadoop jar hadoop-mapreduce-examples-2.7.2.jar wordcount /user/input /user/output #运行程序`  
7. 查看结果  
`hadoop fs -cat /user/output/* #输出如下` 
        a       1
        c       2
        d       1
        v       1
        z       1
  
### Hadoop-eclipse-plugin2.7.2 安装  
  
1. 下载hadoop-eclipse-plugin-2.7.2.jar插件，下载的插件放入解压下的Eclipse的plugin文件夹下，打开Eclipse.exe.  
2. Eclipse的配置  
进入Eclipse，打开window，preference，找到 Hadoop Map/Reduce 在Hadoop installation directory 输入hadoop的安装目录，重启Eclipse.exe .
3. DFS Location配置  
依次打开Window，showView,others,MapReduce Tools，DFS Location.编辑Define Hadoop Location,  
在General选项卡中填入Location name:***(自定义)  
Map/Reduce Master 对应的host为localhost，Port 为8088 ；  
DFS Master 对应的Host为：localhost;Port 为 50080  
  
4. 可以在Project Explorer中看到DFS Location 并可以看到新建input的文件夹。
5. 新建一个MapReduce Project,新建以下类：  
`WordMapper.java #Mapper类  `  

    
    package yayin;
    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.LongWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Mapper;
    public class WordMapper extends Mapper<LongWritable, Text, Text, IntWritable> {
	private final IntWritable one = new IntWritable(1);
	private Text word=new Text();
	
	public void map(LongWritable ikey, Text ivalue, Context context)
			throws IOException, InterruptedException {
 
		StringTokenizer itr = new StringTokenizer(ivalue.toString());
		while(itr.hasMoreTokens()){
			word.set(itr.nextToken());
			context.write(word,one);
		}
		
	}
    }
    
  
`WordReduce.java #Reduce类`  
    
    package yayin;
    import java.io.IOException;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Reducer;

    public class WordReduce extends Reducer<Text, IntWritable, Text, IntWritable> {
	private IntWritable result=new IntWritable();
	public void reduce(Text _key, Iterable<IntWritable> values, Context context)
			throws IOException, InterruptedException {
		// process values
		int sum=0;
		for (IntWritable val : values) {
			sum+=val.get();
		}
		result.set(sum);
		context.write(_key,result);
	}
    }
  
`WordDriver.java #MapReduce的job 配置类  `
    
    package yayin;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    public class WordDriver {
	public static void main(String[] args) throws Exception {
		Configuration conf = new Configuration();
		Job job = Job.getInstance(conf, "JobName");
		job.setJarByClass(yayin.WordDriver.class);
		job.setMapperClass(yayin.WordMapper.class);
		job.setReducerClass(yayin.WordReduce.class);
		job.setCombinerClass(yayin.WordReduce.class);
	    job.setReducerClass(yayin.WordReduce.class);
	    job.setOutputKeyClass(Text.class);
		// TODO: specify output types
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(IntWritable.class);
		// TODO: specify input and output DIRECTORIES (not files)
		FileInputFormat.setInputPaths(job, new Path("hdfs://localhost:50080/user/input"));
		FileOutputFormat.setOutputPath(job, new Path("hdfs://localhost:50080/user/output"));
		if (!job.waitForCompletion(true))
			return;
	}
    }
    
命令压缩代码。  
`jar -cvf WordCount.jar ./yayin/* #进入源代码的bin目录其中yayin是包名。`  
运行jar包。  
`hadoop jar WordCount.jar yayin.WordCount /user/input /user/output #其中yayin.WordCount 是主类，后面的参数分别是输入文件地址，以及输出的文件地址  `
输出为：  
    
    a       1
    c       2
    d       1
    v       1
    z       1 
    
   