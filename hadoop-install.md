Title: 打造分布式文件系统--Hadoop配置安装
Date: 2013-10-23 14:46
Author: neoyin
Category: 技术流
Tags: hadoop
Slug: hadoop-install

</p>
(Hadoop分布式文件系统 Hadoop Distributed File System)
其相关知识就不在这里介绍了,可以直接去[http://hadoop.apache.org/](http://hadoop.apache.org/),也可以直接google
直接进入安装(Ubuntu)环节: **1.环境需求** jdk 在此不做安装说明 ssh
为建立免登录所用 <!--more-->

    ssh-keygen -t rsa -P ""  #创建密钥
    cd ~/.ssh
    cat id_rsa.pub >> authorized_keys #加入授权
    ssh localhost

    需要在master slave 中都加入

**2.下载安装** 下载地址:
[http://hadoop.apache.org/releases.html\#Download](http://hadoop.apache.org/releases.html#Download)
这里下载的版本:
[hadoop-2.2.0.tar.gz](http://www.eu.apache.org/dist/hadoop/common/hadoop-2.2.0/hadoop-2.2.0.tar.gz)
Release Notes : [Hadoop 2.2.0 Release
Notes](http://hadoop.apache.org/docs/r2.2.0/hadoop-project-dist/hadoop-common/releasenotes.html)
解压到/usr/local 目录下

    sudo ln -s hadoop-2.2.0/ hadoop   #建立链接
    添加环境路径
    export HADOOP_INSTALL=/usr/local/hapood
    export PATH=$HADOOP_INSTALL/bin:$HADOOP_INSTALL/sbin:$PATH
    export HADOOP_MAPRED_HOME=$HADOOP_INSTALL
    export HADOOP_COMMON_HOME=$HADOOP_INSTALL
    export HADOOP_HDFS_HOME=$HADOOP_INSTALL
    export YARN_HOME=$HADOOP_INSTALL  
    cd /usr/local/hadoop
    bin/hadoop version     #查看hadoop信息
    输入如下安装成功:
    Hadoop 2.2.0
    Subversion https://svn.apache.org/repos/asf/hadoop/common -r 1529768
    Compiled by hortonmu on 2013-10-07T06:28Z
    Compiled with protoc 2.5.0
    From source with checksum 79e53ce7994d1628b240f09af91e1af4
    This command was run using /usr/local/hadoop-2.2.0/share/hadoop/common/hadoop-common-2.2.0.jar

**3.配置** 这一部分描述在节点 上安装配置Hadoop， 这里的安装是指： 安装一个
HDFS （包含一个命名节点 namenode和一个数据节点 datanode） 以及 一个
Map/Reduce 机群（带有 jobtracker 和一个单独的 tasktracker） .
配置过程同样适用于大规模的机群的安装。
此版本配置文件目录为$HADOOP\_INSTALL/etc/hadoop

    hadoop-env.sh：包含Hadoop设置的一些环境变量。你可以使用他们影响Hadoop Deamons的行为，例如：日志文件的存储地点、 最大数量的堆栈内存的使用等等. 需修改的一个环境变量是JAVA_HOME
    slaves ：这个文件每行列出一个HOST的信息，在这些HOST上 Hadoop slave daemons (datanodes 和 tasktrackers)运行其上. 默认是一行： localhost
    core-site.xml： 此文件包含具体每个节点对所有 Hadoop daemons 和 Map/Reduce jobs的设置。此文件默认为空。
    mapred-site.xml ：此文件包含具体每个节点对 Hadoop Map/Reduce daemons 和 jobs的设置信息。默认为空。

    各相关配置如下:
    各master slave /etc/hosts添加
    #ip              $hostname
    192.168.50.110  neoyin-Inspiron-580
    192.168.50.124  lifeix-DX4840
    192.168.50.123  abc-DX4840

    core-site.xml
    <configuration>
    <property>
    <name>fs.defaultFS</name>
    <value>hdfs://masterIP:9000</value>
    </property>
    </configuration>

    hdfs-site.xml
    <configuration>
    <property>
    <name>dfs.name.dir</name>
    <value>file:/data/hdfs/name</value>
    </property>
    <property>
    <name>dfs.data.dir</name>
    <value>file:/data/hdfs/data</value>
    </property>
    <property>
    <name>dfs.permissions</name>
    <value>false</value>
    </property>
    <property>
    <name>dfs.replication</name>
    <value>1</value>
    </property>
    </configuration>

    mapred-site.xml
    <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
    </property>
    <property>
    <name>mapred.system.dir</name>
    <value>file:/data/hdfs/mapred/mapred/system</value>
    <final>true</final>
    </property>
    <property>
    <name>mapred.local.dir</name>
    <value>file:/data/hdfs/mapred/mapred/local</value>
    <final>true</final>
    </property>

    yarn-site.xml
    <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
    </property>
    <property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
    </property>

    master
    #masterip
    slave
    #slave_1_ip
    #slave_2_ip
    ...

建立运行Hadoop第一
步是格式化Haoop文件系统，它是实现在本地机群的文件系统之上
，格式化文件系统是你安装Haddoop第一时间应该做的事情。不要格式化一个正在运行中的Hadoop文件系统，这会导致你所有的数据被清除。

**格 式化之前，**确保 `dfs.name.dir` 目录存在。 如果你选择默认的值，那么
`mkdir -p $dfs.name.dir` 命令即可实现 . 格式化 (其实是初始化
`dfs.name.dir` 变量指向的路径), 运行如下命令：

    bin/hadoop namenode -format
    INFO namenode.NameNode: SHUTDOWN_MSG: 
    /************************************************************
    SHUTDOWN_MSG: Shutting down NameNode at xxx-xxx/127.0.1.1
    ************************************************************/
    启动
    sbin/start-all.sh
    check 启动是否成功
    jps命令查看如下:
    14640 ResourceManager
    15138 Jps
    13967 NameNode
    14497 SecondaryNameNode

访问如下页面http://localhost:8088/ http://localhost:50070/
可查看相关信息 Notes:

1.  Master和Slave上的几个conf配置文件不需要全部同步，如果确定都是通过Master去启动和关闭，那么Slave机器上的配置不需要去维护。但如果希望在任意一台机器都可以启动和关闭Hadoop，那么就需要全部保持一致了。
2.  Master和Slave机器上的`/etc/hosts`中必须把集群中机器都配置上去，就算在配置文件中使用的是IP也需如此,不然一直会出现
    datanode denied communication with namenode 相关错误. 参考资料:

---
参考：

- [http://hadoop.apache.org/docs/r2.2.0/](http://hadoop.apache.org/docs/r2.2.0/)
- [http://dongxicheng.org/mapreduce-nextgen/hadoop-2-2-0/](http://dongxicheng.org/mapreduce-nextgen/hadoop-2-2-0/)
- [http://www.yongbok.net/blog/how-to-install-hadoop-2-2-0-pseudo-distributed-mode/](http://www.yongbok.net/blog/how-to-install-hadoop-2-2-0-pseudo-distributed-mode/)
- [http://www.cnblogs.com/xia520pi/category/346943.html](http://www.cnblogs.com/xia520pi/category/346943.html)
