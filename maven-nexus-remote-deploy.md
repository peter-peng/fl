Title: Nexus搭建Maven私服(五) Maven远程部署web工程
Date: 2012-03-21 20:42
Author: neoyin
Category: 技术流
Tags: maven,nexus
Slug: maven-nexus-remote-deploy

### Maven远程部署web工程

在Maven项目中添加  tomcat-maven-plugin 插件
pom.xml 配置如下:
```
<plugin>
<groupId>org.codehaus.mojo</groupId>
<artifactId>tomcat-maven-plugin</artifactId>
<version>1.2-SNAPSHOT</version>
<configuration>
<url>http://IP:port/manager/html</url>
<server>tomcat</server>
<warFile>${project.build.directory}/${project.build.finalName}.${project.packaging}</warFile>
</configuration>
</plugin>
```

#### tomcat配置如下:
```
conf/tomcat-user.xml
<tomcat-users>
<role rolename="manager"/>
<user username="tomcat" password="****" roles="manager"/>
</tomcat-users>
```

#### maven 配置如下
```
~/.m2/setting.xml
<server>
<id>tomcat</id>
<username>tomcat</username>
<password>****</password>
</server>
```

maven 命令: mvn tomcat:deploy  

注: 打war包如果想去除掉version 可在
```
<build></build> 中添加<finalName>{finalname}</finalName>
```
刚最后生成的war包名为finalName名.

- 参考: http://mojo.codehaus.org/tomcat-maven-plugin/index.html 