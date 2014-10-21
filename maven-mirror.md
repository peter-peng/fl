Title: Maven私服中的mirror
Date: 2014-02-07 16:26
Author: neoyin
Category: 技术流
Tags: maven, mirror, repository
Slug: maven-mirror

</p>
前一段时间在公司内部搭建了Maven私服，在内部开发过程中一定程度上提高了工作效率。但是最近开发过程中遇到了一个问题，现将这一问题的发生的原因和过程记录下来：

最近一个同事开发过程中需要用到rabbitmq的最新版本(3.2.3)。但是本地私服还没更新这一最新版本。于是在其项目pom.xml文件中添加了一个repository

    <repository>
     <id>sonatype-nexus-staging</id>
     <name>Nexus Release Repository</name>
     <url>http://oss.sonatype.org/service/local/staging/deploy/maven2/</url>
    </repository>

在其本机上运行后没问题，放至测试环境后项目编译失败,仔细查看了一下日志发现编译的时候所需的rabbitmq最新版本jar从maven私服找不到。
这个当然找不到呀。私服上还没有最新的版本呢。

<!--more-->

但是明明在pom.xml文件中指定了repository，怎么还会去私服中找呢？而且本地没有问题呀。
经过仔细排查发现在测试环境中的\~/.m2/settings.xml文件中有一段配置

     <mirror>
        <id>nexus</id>
        <mirrorOf>*</mirrorOf>
        <name>Nexus</name>
        <url>http://nexus.xy.l99.com:8081/nexus/content/groups/public/</url>
     </mirror>

此处的镜像配置中 `<mirrorOf>*</mirrorOf>`
将所有对于远程仓库的请求都会被转至私服了.而本地没有做过这样的配置。所以本地编译OK，测试环境却出现报错。

关于mirror配置如下： Maven还支持更高级的镜像配置：

1.  `<mirrorOf>*</mirrorOf>`匹配所有远程仓库。
2.  `<mirrorOf>external:*</mirrorOf>`匹配所有远程仓库，使用localhost的除外，使用file://协议的除外。也就是说，匹配所有不在本机上的远程仓库。
3.  `<mirrorOf>repo1,repo2</mirrorOf>`
    匹配仓库repo1和repo2，使用逗号分隔多个远程仓库。
4.  `<mirrorOf>*,!repo1</miiroOf>`
    匹配所有远程仓库，repo1除外，使用感叹号将仓库从匹配中排除。

[http://m.oschina.net/blog/100634](http://m.oschina.net/blog/100634)
