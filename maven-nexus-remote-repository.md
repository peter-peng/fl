Title: Nexus搭建Maven私服(二) 分发构件至远程仓库
Date: 2012-03-21 20:42
Author: neoyin
Category: 技术流
Tags: maven,nexus
Slug: maven-nexus-remote-repository

### 分发构件至远程仓库

上一篇文章中已将Maven私服搭建起来.

这篇文章主要涉及到将本地项目构件分发至远程仓库中.

mvn install 会将项目生成的构件安装到本地Maven仓库，mvn deploy 用来将项目生成的构件分发到远程Maven仓库。本地Maven仓库的构件只能供当前用户使用，在分发到远程Maven仓库之后，所有能访问该仓库的用户都能使用你的构件。
 

你需要在你的项目pom.xml中加入
```
<distributionManagement>
<repository>
<id>nexus-releases</id>
<name>Nexus Releases Repository</name>
<url>http://IP:8081/nexus/content/repositories/releases/</url>
</repository>
<snapshotRepository>
<id>nexus-snapshots</id>
<name>Nexus Snapshots Repository</name>
<url>http://IP:8081/nexus/content/repositories/snapshots/</url>
</snapshotRepository>
</distributionManagement>
```
 
Maven区别对待release版本的构件和snapshot版本的构件，snapshot为开发过程中的版本，实时，但不稳定，release版本则比较稳定。Maven会根据你项目的版本来判断将构件分发到哪个仓库。

由于用Maven分发构件到远程仓库需要认证

需要在~/.m2/settings.xml中加入验证信息
```
<server>
<id>nexus-releases</id>
<username>neoyin</username>
<password>*****</password>
</server>
<server>
<id>nexus-snapshots</id>
<username>neoyin</username>
<password>*****</password>
</server>
```

注意的是，settings.xml中server元素下id的值必须与POM中repository或snapshotRepository下id的值完全一致 

刚刚就因为少了一个字母排查错误时面抓狂.

 
附:发布release 版本时发生错误 Failed to transfer file ... Retrun code is 401

Nexus中Releases仓库默认的Deployment Policy是“Disable Redeploy”，所以无法部署的问题在这个地方，方法是将其修改为“Allow Redeploy”就可以了

 

进入项目目录 执行mvn deploy

不一会工夫.ＯＫ 在Nexus中搜索就可发现刚刚deploy的项目

其它开发人员只要在Maven项目中pom.xml加入你的dependency元素就可以用啦.