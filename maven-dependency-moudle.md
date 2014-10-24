Title: maven项目继承及依赖
Date: 2013-05-08 20:45
Author: neoyin
Category: 技术流
Tags: maven
Slug: maven-dependency-moudle

最近一个简单的搜索项目利用maven 特性构建

项目目录结构：

    lifeix-search-api
    ├── lifeix-search-api-bean
    │   ├── pom.xml
    │   └── src
    │       ├── main
    │       └── test
    ├── lifeix-search-api-client
    │   ├── pom.xml
    │   └── src
    │       ├── main
    │       └── test
    ├── lifeix-search-api-server
    │   ├── pom.xml
    │   └── src
    │       ├── main
    │       └── test
    └── pom.xml

<div>
<!--more-->

</div>
<div>
\1. lifeix-search-api 为项目根目录，有一个pom.xml
文件，为lifeix-search-api-bean，lifeix-search-api-client，lifeix-search-api-server
三个项目的父pom文件

</div>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.lifeix</groupId>
    <artifactId>lifeix-search-api</artifactId>
    <version>0.1.0-SNAPSHOT</version>
    <!-- 设置成pom类型 -->
    <packaging>pom</packaging>
    <name>lifeix-search-api</name>
    <!-- 设置子模块 -->
    <modules>
    <module>lifeix-search-api-bean</module>
    <module>lifeix-search-api-client</module>
    <module>lifeix-search-api-server</module>
    </modules>
    <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding><svn.url>svn://${svnURL}</svn.url>
    <jersey.version>1.17.1</jersey.version>
    </properties>
    <scm>...</scm>
    <dependencies>...各种依赖</dependencies>

lifeix-search-api 在myeclipse 下表现如图：

![lifeix-search-api-myeclipse](http://www.floatinglife.cn/wp-content/uploads/2013/05/lifeix-search-api-myeclipse.png)

将父pom.xml 文件deploy 到maven 私服中. 以便子项目可独立依赖。

2.进入lifeix-search-api-bean 项目中。其pom.xml 文件如下。

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <parent>
    <artifactId>lifeix-search-api</artifactId>
    <groupId>com.lifeix</groupId>
    <version>0.1.0-SNAPSHOT</version>
    </parent>

    <groupId>com.lifeix</groupId>
    <artifactId>lifeix-search-api-bean</artifactId>
    </project>

其它所有属性依赖父pom文件

由于lifeix-search-api-client lifeix-search-api-server
都依赖lifeix-search-api-bean 项目，为让这两个项目可独立打包
需将lifeix-search-api-bean deploy 到maven 私服 mvn package source:jar
javadoc:jar deploy

3.进入lifeix-search-api-client 项目中.其pom.xml 文件如下:

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
    <artifactId>lifeix-search-api</artifactId>
    <groupId>com.lifeix</groupId>
    <version>0.1.0-SNAPSHOT</version>
    </parent>
    <groupId>com.lifeix</groupId>
    <artifactId>lifeix-search-api-client</artifactId>
    <version>0.1.0-SNAPSHOT</version>

    <dependencies>
    <!-- oauth-client -->
    <dependency>
    <groupId>com.sun.jersey.contribs.jersey-oauth</groupId>
    <artifactId>oauth-client</artifactId>
    <version>1.17.1</version>
    </dependency>

    <!-- oauth-signature -->
    <dependency>
    <groupId>com.sun.jersey.contribs.jersey-oauth</groupId>
    <artifactId>oauth-signature</artifactId>
    <version>1.17.1</version>
    </dependency>

    <!--  依赖 bean jar  -->
    <dependency>
    <groupId>com.lifeix</groupId>
    <artifactId>lifeix-search-api-bean</artifactId>
    <version>0.1.0-SNAPSHOT</version>
    </dependency>

    </dependencies>

    </project>

可以看到此pom.xml 文件只需配置很少依赖

4.进入lifeix-search-api-server 项目中.其pom.xml 文件如下:

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
    <artifactId>lifeix-search-api</artifactId>
    <groupId>com.lifeix</groupId>
    <version>0.1.0-SNAPSHOT</version>
    </parent>
    <groupId>com.lifeix</groupId>
    <artifactId>lifeix-search-api-server</artifactId>
    <packaging>war</packaging>
    <properties>
    <jackson.version>1.9.12</jackson.version>
    <mysql.connector.version>5.1.24</mysql.connector.version>
    </properties>

    <dependencies>
    <!-- bean -->
    <dependency>
    <groupId>com.lifeix</groupId>
    <artifactId>lifeix-search-api-bean</artifactId>
    <version>0.1.0-SNAPSHOT</version>
    </dependency>

    <!-- other dependency -->
    <dependency>
    ... others 
    </dependency>

    </dependencies>

    </project>

由于lifeix-search-api-server 为web 项目，则packaging设置为war 打包为war
包

OK. 我们可以在lifeix-search-api 目录下执行:

     mvn package -DskipTests
    [INFO] Scanning for projects...
    [INFO] ------------------------------------------------------------------------
    [INFO] Reactor Build Order:
    [INFO] 
    [INFO] lifeix-search-api
    [INFO] lifeix-search-api-bean
    [INFO] lifeix-search-api-client
    [INFO] lifeix-search-api-server
    [INFO]                                                                         
    [INFO] ------------------------------------------------------------------------
    [INFO] Building lifeix-search-api 0.1.0-SNAPSHOT
    [INFO] ------------------------------------------------------------------------
    [INFO]                                                                         
    [INFO] ------------------------------------------------------------------------
    [INFO] Building lifeix-search-api-bean 0.1.0-SNAPSHOT
    [INFO] ------------------------------------------------------------------------
    ....
    [INFO] ------------------------------------------------------------------------
    [INFO] Reactor Summary:
    [INFO] 
    [INFO] lifeix-search-api ................................. SUCCESS [0.002s]
    [INFO] lifeix-search-api-bean ............................ SUCCESS [1.261s]
    [INFO] lifeix-search-api-client .......................... SUCCESS [0.170s]
    [INFO] lifeix-search-api-server .......................... SUCCESS [3.749s]
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 5.326s
    [INFO] Finished at: Wed May 08 16:50:13 CST 2013
    [INFO] Final Memory: 15M/214M

    也可以到各自项目目录下执行 maven 命令.
