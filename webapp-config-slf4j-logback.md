Title: 在web app中配置 slf4j 和 logback 
Date: 2014-06-25 13:27
Author: chengz
Category: 技术流
Slug: webapp-config-slf4j-logback

在web app中配置 slf4j 和 logback (简略翻译)
===========================================

原文 : [How to setup SLF4J and LOGBack in a web app -
fast](https://wiki.base22.com/display/btg/How+to+setup+SLF4J+and+LOGBack+in+a+web+app+-+fast)

第0步 添加依赖jar (用maven的略过此步骤)
---------------------------------------

-   WEB-INF  
    \*\* lib  
    \*\*\* logback-classic.x.x.x.jar  
    \*\*\* logback-core.x.x.x.jar  
    \*\*\* slf4j-api-x.x.x.jar

step 1 加入 maven 依赖
----------------------

    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.0.13</version>
    </dependency>

step 2 导入 初始的xml配置
-------------------------

下面两个链接是可以用来做起步的初始配置文件

-   src  
    \*\* main  
    \*\*\* resources  
    \*\*\*\*
    [logback.xml](https://wiki.base22.com/download/attachments/35488048/logback.xml?version=1&modificationDate=1261291177000&api=v2)  
    \*\* test  
    \*\*\* resources  
    \*\*\*\*
    [logback-test.xml](https://wiki.base22.com/download/attachments/35488048/logback-test.xml?version=1&modificationDate=1261291177000&api=v2)

step 3 自定义 XML 配置
----------------------

上面下载的文件内容差不多是这样, logger 的名字是一个包的层级
("com.base22"), 你可以改成自己的包, 也可以定义更多的logger (package
和/或 class).

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
          <Pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</Pattern>
        </layout>
      </appender>

      <logger name="com.base22" level="TRACE"/>


      <root level="debug">
        <appender-ref ref="STDOUT" />
      </root>
    </configuration>

step 4 加入log代码
------------------

imports

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

把下面代码加入到类的第一行里

    static final Logger LOG = LoggerFactory.getLogger(MyClassName.class);

log代码

    LOG.trace("Hello World!");
    LOG.debug("How are you today?");
    LOG.info("I am fine.");
    LOG.warn("I love programming.");
    LOG.error("I am programming.");

step 5 运行程序看结果
---------------------
