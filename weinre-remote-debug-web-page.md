Title: 用weinre远程调试web页面
Date: 2014-04-25 06:19
Author: chengz
Category: 技术流
Slug: weinre-remote-debug-web-page

1.  http://people.apache.org/\~pmuellr/weinre/builds 下载bin文件
2.  安装  
    `npm -g install apache-cordova-weinre-2.0.0-pre-HH0SN197-bin.tar.gz`
3.  如果网络好的话,可以直接 npm -g install weinre安装
4.  启动

        weinre [options]
        weinre --httpPort 8088 --boundHost -all- 

5.  浏览器打开 http://localhost:8088/client/ ,这是操作页面
6.  用手机访问server, 假设手机可以通过 http://a.b.c:8088/ 访问server,
    记下这个地址
7.  在要调试的页面中加入

         <script src="http://a.b.c:8088/target/target-script-min.js"></script>

8.  用手机访问页面,
    浏览器端的http://localhost:8088/client/页面会收到提示,
    可以开始调试了

-   [文档地址](http://people.apache.org/~pmuellr/weinre/docs/latest/Running.html)

