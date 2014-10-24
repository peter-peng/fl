Title: 利用Sonar及Maven 插件 进行代码质量检测管理
Date: 2013-07-20 10:11
Author: neoyin
Category: 技术流
Tags: maven, sonar
Slug: sonar-maven-code-quality

Sonar 是一个用于代码质量管理的开放平台。通过插件机制，Sonar
可以集成不同的测试工具，代码分析工具，以及持续集成工具。

与持续集成工具（例如 Hudson/Jenkins 等）不同，Sonar
并不是简单地把不同的代码检查工具结果（例如 FindBugs，PMD 等）直接显示在 Web
页面上，而是通过不同的插件对这些结果进行再加工处理，通过量化的方式度量代码质量的变化，从而可以方便地对不同规模和种类的工程进行代码质量管理。

**Sonar安装**

可以到官方网站下载最新版本:
[http://www.sonarqube.org/downloads/](http://www.sonarqube.org/downloads/)

直接解压，不需要进行额外的安装

    到bin目录下对应的系统执行脚本启动即可
    bin/
    |-- jsw-license
    |-- linux-ppc-64
    |-- linux-x86-32
    |-- linux-x86-64
    |-- macosx-universal-32
    |-- macosx-universal-64
    |-- solaris-sparc-32
    |-- solaris-sparc-64
    |-- solaris-x86-32
    |-- windows-x86-32
    `-- windows-x86-64

项目默认端口为9000 在浏览器中访问：http://localhost:9000/

![image003](http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=2ba889158418367aa9897fd81e48fae9/f9198618367adab4d1145f7f89d4b31c8601e4c2.jpg?referer=bd6e28e23f6d55fb9cd1421644c0&x=.jpg)

<!--more-->

**数据库设置**

Sonar 默认使用的是 Derby
数据库，但这个数据库一般用于评估版本或者测试用途。商用及对数据库要求较高时，建议使用其他数据库。Sonar
可以支持大多数主流关系型数据库（例如 Microsoft SQL Server, MySQL, Oracle,
PostgreSQL 等）

在conf/sonar.properties 中修改相关配置

    sonar.jdbc.username: sonar
    sonar.jdbc.password: sonar
    sonar.jdbc.driverClassName: com.mysql.jdbc.Driver
    sonar.jdbc.url:
    其端口号也可以在这里配置
    sonar.web.port: 9000

**Maven 插件及配置**

Maven 插件会自动把所需数据（如单元测试结果、静态检测结果等）上传到 Sonar
服务器上，需要说明的是，关于 Sonar 的配置并不在每个工程的 pom.xml
文件里，而是在 Maven 的配置文件 settings.xml 文件里，具体配置如下

    在maven  sittings.xml 中添加：
    <profile>
         <id>sonar</id>
         <activation>
             <activeByDefault>true</activeByDefault>
         </activation>
         <properties>
              <sonar.jdbc.url>
              jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8
              </sonar.jdbc.url>
              <sonar.jdbc.driver>com.mysql.jdbc.Driver</sonar.jdbc.driver>
              <sonar.jdbc.username>sonar</sonar.jdbc.username>
              <sonar.jdbc.password>sonar</sonar.jdbc.password>
             <sonar.host.url>http://localhost:9000</sonar.host.url>
         </properties>
      </profile>

    maven 项目中添加插件

    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>sonar-maven-plugin</artifactId>
        <version>2.0</version>
        <configuration>
            <sonarHostURL>http://192.168.1.100:8082</sonarHostURL>
        </configuration>
    </plugin>

maven 项目编译后运行

    mvn sonar:sonar

将 Soanr 所需要的数据上传到 Sonar 服务器上之后，Sonar
安装的插件会对这些数据进行分析和处理，并以各种方式显示给用户，从而使用户方便地对代码质量的监测和管理。  
例如 Radiator 插件可以根据项目的规模进行排序，并用不同演示显示代码质量：

![image013](http://c.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=968ffe5896eef01f491418c0d0c5e818/8d5494eef01f3a290ff592f29b25bc315c607cc8.jpg?referer=8586bb1b7d3e6709e71770cfbcc6&x=.jpg)

Sonar 为代码的质量管理提供了一个平台，对传统的代码静态检测如
PMD、FindBugs 等工具进行整合，可以说是目前最强大的代码质量管理工具之一。

参考：

[http://www.ibm.com/developerworks/cn/java/j-lo-sonar/](http://www.ibm.com/developerworks/cn/java/j-lo-sonar/)
