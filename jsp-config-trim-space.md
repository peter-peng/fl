Title: jsp-config去除JSP页面生成的空行
Date: 2013-12-04 00:30
Author: neoyin
Category: 技术流
Tags: java, tomcat
Slug: jsp-config-trim-space

一般jsp页面查看源码时，会有许多空行。他们是由<%...%\>的回车换行或其它一些jsp标签产生而生成的,虽然并不会影响浏览，但确实不够雅观，也产生了一些不必要的网络流量。

去除jsp页面空行的办法如下：

1.在jsp页面添加如下代码( 支持JSP 2.1+ )：

    <%@ page trimDirectiveWhitespaces="true" %>

2.在web项目web.xml(支持servlet 2.5+, 即 web.xml的 XSD版本为2.5)中添加：

    <jsp-config>
      <jsp-property-group>
        <url-pattern>*.jsp</url-pattern>
        <trim-directive-whitespaces>true</trim-directive-whitespaces>
      </jsp-property-group>
    </jsp-config>

3.在tomcat配置文件conf/web.xml中添加

    <init-param>
        <param-name>trimSpaces</param-name>
        <param-value>true</param-value>
    </init-param>

jsp-config 包括 taglib 和 jsp-property-group 两个子元素。  
jsp-property-group 元素主要有八个子元素，它们分别为：

    1.< description>：设定的说明；
    2.< display-name>：设定名称；
    3.< url-pattern>：设定值所影响的范围，如：/CH2 或 /*.jsp；
    4.< el-ignored>：若为true，表示不支持EL 语法；
    5.< scripting-invalid>：若为true，表示不支持< % scripting %>语法；
    6.< page-encoding>：设定JSP 网页的编码；
    7.< include-prelude>：设置JSP 网页的抬头，扩展名为.jspf；
    8.< include-coda>：设置JSP 网页的结尾，扩展名为.jspf。

---

http://www.giantgeek.com/blog/?p=348
