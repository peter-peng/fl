Title: 打造分布式文件系统--HDFS
Date: 2013-10-30 11:52
Author: neoyin
Category: 技术流
Tags: hadoop
Slug: hadoop-hdfs

经过
[打造分布式文件系统–Hadoop配置安装](http://www.floatinglife.cn/distributed-hadoop)
我们已简单的搭建了hadoop

但分布式文件系统之路才刚刚开始呢.这一旅程的第一步就是HDFS

关于其介绍网上太多,而且基本类似,就不在这里阐述了.大家自行搜索

[http://www.cnblogs.com/forfuture1978/archive/2010/03/14/1685351.html](http://www.cnblogs.com/forfuture1978/archive/2010/03/14/1685351.html)

Hadoop的命令行提供了一套完整命令接口，就像Linux命令一样方便使用。

如下:

    bin/hadoop dfs
    DEPRECATED: Use of this script to execute hdfs command is deprecated.
    Instead use the hdfs command for it.

    Usage: hadoop fs [generic options]
        [-appendToFile ...]
        [-cat [-ignoreCrc] ...]
        [-checksum ...]
        [-chgrp [-R] GROUP PATH...]
        [-chmod [-R] <MODE[,MODE]... | OCTALMODE> PATH...]
        [-chown [-R] [OWNER][:[GROUP]] PATH...]
        [-copyFromLocal [-f] [-p] ...]
        [-copyToLocal [-p] [-ignoreCrc] [-crc] ...]
        [-count [-q] ...]
        [-cp [-f] [-p] ...]
        [-createSnapshot []]
        [-deleteSnapshot]
        [-df [-h] [ ...]]
        [-du [-s] [-h] ...]
        [-expunge]
        [-get [-p] [-ignoreCrc] [-crc] ...]
        [-getmerge [-nl]]
        [-help [cmd ...]]
        [-ls [-d] [-h] [-R] [ ...]]
        [-mkdir [-p] ...]
        [-moveFromLocal ...]
        [-moveToLocal]
        [-mv ...]
        [-put [-f] [-p] ...]
        [-renameSnapshot]
        [-rm [-f] [-r|-R] [-skipTrash] ...]
        [-rmdir [--ignore-fail-on-non-empty]

    Generic options supported are
    -conf <configuration file> specify an application configuration file
    -D <property=value> use value for given property
    -fs <local|namenode:port> specify a namenode
    -jt <local|jobtracker:port> specify a job tracker
    -files <comma separated list of files> specify comma separated files to be copied to the map reduce cluster
    -libjars <comma separated list of jars> specify comma separated jar files to include in the classpath.
    -archives <comma separated list of archives> specify comma separated archives to be unarchived on the compute machines

在这里可查看
[http://hadoop.apache.org/docs/r2.2.0/hadoop-project-dist/hadoop-common/FileSystemShell.html\#expunge](http://hadoop.apache.org/docs/r2.2.0/hadoop-project-dist/hadoop-common/FileSystemShell.html#expunge)

<!--more-->

不做过多演示,直接进入java API 环节

由于之前hadoop 版本为2.2 所以我们需要引入hadoop-client相对应版本.

在maven 项目pom.xml文件中添加相关仓库

    <repositories>
    <repository>
    <id>cloudera-repo-releases</id>
    <url>https://repository.cloudera.com/artifactory/repo/</url>
    </repository>
    </repositories>

    项目依赖中添加
    <dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-client</artifactId>
    <version>2.2.0-cdh5.0.0-beta-1</version>
    </dependency>

代码示例:

            /**
         * 创建目录
         * @param dir
         * @throws IOException
         */
        public static void mkdirs(String dir) throws IOException{
            Path path = new Path(hdfs+dir);
            FileSystem fs = FileSystem.get(URI.create(hdfs),conf);
            if (!fs.exists(path)) {
                fs.mkdirs(path);
            }else {

            }
            fs.close();
        }
        //删除
        public static void rmDir(String dir) throws IOException{
            Path path = new Path(hdfs+dir);
            FileSystem fs = FileSystem.get(URI.create(hdfs),conf);
            fs.deleteOnExit(path);
            fs.close();
        }
        //显示
        public static void cat(String file) throws IOException{
            Path path = new Path(file);
            FileSystem fs = FileSystem.get(URI.create(hdfs), conf);
            FSDataInputStream fsdis = null;
            System.out.println("cat: " + file);
            try {
                fsdis = fs.open(path);
                IOUtils.copyBytes(fsdis, System.out, 4096, false);
            } finally {
                IOUtils.closeStream(fsdis);
                fs.close();
            }
        }

        //从本地copy
        public static void upload() throws IOException{
            String path ="/home/neoyin/Desktop/testtest";
            String fpath =hdfs+"ttt";
            InputStream in = new BufferedInputStream(new FileInputStream(path));
            FileSystem fs = FileSystem.get(URI.create(fpath),conf);
            OutputStream out =fs.create(new Path(fpath),new Progressable() {
                @Override
                public void progress() {
                    System.out.println("===");
                }
            });
            IOUtils.copyBytes(in, out, 4096, true);
        }

以上只是通过简单java API 去了解并简单应用hadoop .

执行上述程序可以在[http://$master\_ip:50070](http://localhost:50070/)
查看文件的创建等是否正确

---

参考:

- [http://www.cnblogs.com/forfuture1978/archive/2010/03/14/1685351.html](http://www.cnblogs.com/forfuture1978/archive/2010/03/14/1685351.html)
- [http://www.cnblogs.com/xia520pi/archive/2012/05/28/2520813.html](http://www.cnblogs.com/xia520pi/archive/2012/05/28/2520813.html)
- [http://blog.fens.me/hadoop-hdfs-api/](http://blog.fens.me/hadoop-hdfs-api/)
