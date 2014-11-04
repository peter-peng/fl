Title: Nexus搭建Maven私服(三) 版本控制
Date: 2012-03-22 21:23
Author: neoyin
Category: 技术流
Tags: maven,nexus
Slug: maven-nexus-revesion

### Ｍavne插件--关于版本控制


一个项目到一个阶段后，就需要发布一个正式的版本（release版本）。一次正式的发布需要这样一些工作：

在trunk中，更新pom版本从1.0-SNAPSHOT到1.0
对1.0打一个svn tag
针对tag进行mvn deploy，发布正式版本
更新trunk从1.0到1.1-SNAPSHOT
你可以手工一步步的做这些事情，无非就是一些svn操作，一些pom编辑，还有一些mvn操作。但是你应该明白，手工做这些事情，一来繁琐，而来容易出错。因此这里我介绍使用maven插件来自动化这一系列动作。

 
在POM中加入scm信息，这样Maven才能够替你完成svn操作，配置如下：

```
<scm>
<connection>scm:svn:http://svnIP/svn/myapp/trunk/</connection><developerConnection>scm:svn:https://svnIP/svn/myapp/trunk/</developerConnection>
</scm>
```

#### maven-release-plugin

我们需要配置maven-release-plugin，这个插件会帮助我们升级pom版本，提交，打tag，然后再升级版本，再提交，等等。基本配置如下
```
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-release-plugin</artifactId>
<version>2.0-beta-7</version>
<configuration>
<tagBase>https://svnIP/svn/myapp/tags/</tagBase>
</configuration>
</plugin>
```

tagBase需要填写svn中发布项目的目录

确保你的所有代码都提交了，如果你有未提交代码，release插件会报错，既然你要发布版本了，就表示代码是稳定的

执行mvn release:prepare

执行过程中，你会遇到这样的提示：

> What is the release version for "Unnamed - org.myorg:myapp:jar:1.0-SNAPSHOT"? (org.myorg:myapp) 1.0: :
——“你想将1.0-SNAPSHOT发布为什么版本？默认是1.0。”我要的就是1.0，直接回车。
> What is SCM release tag or label for "Unnamed - org.myorg:myapp:jar:1.0-SNAPSHOT"? (org.myorg:myapp) myapp-1.0: :
——“发布的tag标签名称是什么？默认为myapp-1.0。”我还是要默认值，直接回车。
> What is the new development version for "Unnamed - org.myorg:myapp:jar:1.0-SNAPSHOT"? (org.myorg:myapp) 1.1-SNAPSHOT: :
——“主干上新的版本是什么？默认为1.1-SNAPSHOT。”哈，release插件会自动帮我更新版本到1.1-SNAPSHOT，很好，直接回车。

结果:
我们多了一个tag：https://192.168.1.100:8443/svn/myapp/tags/myapp-1.0/，这就是需要发布的版本1.0。
再看看trunk中的POM，其版本自动升级成了1.1-SNAPSHOT
接着将其发布至到仓库中

执行`mvn release:perform`

maven-release-plugin会自动帮我们签出刚才打的tag，然后打包，分发到远程Maven仓库中，至此，整个版本的升级，打标签，发布等工作全部完成。我们可以在远程Maven仓库中看到正式发布的1.0版本。

这可是自动化的 ，正式的 版本发布！


- http://maven.apache.org/plugins/index.html
- http://juvenshun.iteye.com/