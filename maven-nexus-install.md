Title: Nexus搭建Maven私服(一) Maven安装
Date: 2012-03-21 10:47
Author: neoyin
Category: 技术流
Tags: maven,nexus
Slug: maven-nexus-install


### Maven安装

- 下载地址：http://maven.apache.org/download.html
- 下载(此版本为3.0.4)：wget http://mirror.bjtu.edu.cn/apache/maven/binaries/apache-maven-3.0.4-bin.tar.gz
- 解压安装：`tar -xvzf apache-maven-3.0.4-bin.tar.gz`
-maven的环境配置: vim /etc/profile 中 添加
```
 export M2_HOME=../apache-maven-3.0.4
 export PATH=$PATH:$M2_HOME/bin

使环境生效 source /etc/profile
测试maven 安装成功 mvn -v
```

<!-- more -->
### Nexus安装 

- Nexus 网站 http://www.sonatype.org/nexus/
- 下载 http://nexus.sonatype.org/downloads/

Nexus提供了两种安装方式，一种是内嵌Jetty的bundle，只要你有JRE就能直接运行。第二种方式是WAR，你只须简单的将其发布到web容器中即可使用.

- 解压 `tar xvzf nexus-2.0-bundle.tar.gz`

进入 cd ...bin/jsw/linux-x86-64/中 运行 ./nexus start 即可。

http://localhost:8081/nexus 进入. 默认用户名密码为admin/admin123.
 
nexus默认是关闭远程索引下载功能的，主要是担心会造成对服务器的巨大负担，需要我们手工开启。 
 
开启的方式： 
>点击Administration菜单下面的Repositories，将这三个仓库Apache Snapshots，Codehaus Snapshots，Maven Central的Download Remote Indexes修改为true。然后在这三个仓库上分别右键，选择Re-index，这样Nexus就会去下载远程的索引文件。

要让maven使用nexus作为私服，需要做一些设置，使用和原来设置artifactory相似的方法。修改~/.m2/settings.xml. 
 
#### 增加nexus的profile：
```
<profiles>

<profile>
<id>nexus</id>
<repositories>
<repository>
<id>nexus</id>
<name>Nexus</name>
<url>http://ip:8081/nexus/content/groups/public/</url>
<releases><enabled>true</enabled></releases>
<snapshots><enabled>true</enabled></snapshots>
</repository>
</repositories>
<pluginRepositories>
<pluginRepository>
<id>nexus</id>
<name>Nexus</name>
<url>http://ip:8081/nexus/content/groups/public/</url>
<releases><enabled>true</enabled></releases>
<snapshots><enabled>true</enabled> </snapshots>
</pluginRepository>
</pluginRepositories>

</profile>

</profiles>
<activeProfiles>
<activeProfile>nexus</activeProfile>
</activeProfiles>
```

#### 镜像:

如果你的地理位置附近有一个速度更快的central镜像，或者你想覆盖central仓库配置，或者你想为所有POM使用唯一的一个远程仓库（这个远程仓库代理的所有必要的其它仓库），你可以使用settings.xml中的mirror配置。
```
<mirror>
<id>nexus</id>
<mirrorOf>*</mirrorOf>
<name>Nexus</name>
<url>http://IP:8081/nexus/content/groups/public/</url>
</mirror>

 
<profile>
<id>nexus</id>
<repositories>
<repository>
<id>central</id>
<name>Nexus</name>
<url>http://central</url>
<releases><enabled>true</enabled></releases>
<snapshots><enabled>true</enabled></snapshots>
</repository>
</repositories>
<pluginRepositories>
<pluginRepository>
<id>central</id>
<name>Nexus</name>
<url>http://central</url>
<releases><enabled>true</enabled></releases>
<snapshots><enabled>true</enabled> </snapshots>
</pluginRepository>
</pluginRepositories>

</profile>
```
 
此配置用Ｍaven私服覆盖central仓库配置 这样Ｍaven对任何仓库的构件下载请求都会转到私服中.

#### 需要开机启动maven私服服务
```
/etc/rc.d/rc.local 中加入 nexus/bin/jsw/linux-x86-64/nexus start
```
 
- 相关地址: http://maven.apache.org/pom.html#Quick_Overview

 