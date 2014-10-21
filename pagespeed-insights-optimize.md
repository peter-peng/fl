Title: PageSpeed Insights 优化网站
Date: 2013-11-27 23:14
Author: neoyin
Category: 技术流
Tags: gzip, nginx, optimize, wordpress
Slug: pagespeed-insights-optimize

PageSpeed Insights
是google提供的网站性能工具。它可以分析网页的性能并给出提升性能的建议和措施，是你用来分析和提高网站性能的利器。

地址如下:[https://developers.google.com/speed/pagespeed/insights/](https://developers.google.com/speed/pagespeed/insights/)

输入网址即可分析出网页性能以及一些相关建议。

FloatingLife 建站以来并未做过什么优化

第一次 google给出的提示从以下几个方面进行优化:

**1.清除首屏内容中阻止呈现的 JavaScript 和
CSS**此原因是wordpress中的js都是在header中引入的。

<span style="color: #0000ff;">解决方案：安装插件**Autoptimize**</span>

**2.启用gzip** 使用 gzip 或 deflate
压缩资源可以减少通过网络发送的字节数。

       
            解决方案：可在nginx配置中添加如下配置开启gzip
            gzip on;
            gzip_min_length  1000;
            gzip_proxied     expired no-cache no-store private auth;
            gzip_types       text/plain application/xml;
            gzip_disable     "MSIE [1-6]\.";

<!--more-->

**3.使用浏览器缓存** 在 HTTP
标头中为静态资源设置过期日期或最长存在时间，可指示浏览器从本地磁盘中加载以前下载的资源，而不是通过网络加载。

           解决方案：在nginx配置中添加如下使本地浏览器缓存 
           location ~.*\.(jpg|png|jpeg)$ {
                    expires 30d;
            }
            location ~.*\.(js|css)?$ {
                    expires 3d;
            }

**4.压缩 JavaScript** 压缩
JavaScript代码可以节省大量数据字节空间，并提高下载、解析和执行的速度。

解决方案：安装插件**Autoptimize**

5.压缩CSS

解决方案：安装插件**Autoptimize**

6.压缩Html

解决方案：同样安装插件[**Autoptimize**](http://blog.futtta.be/autoptimize)

-   整合所有的脚本和样式表并将之缩小和压缩
-   添加有时限header缓存
-   优化缩小HTML代码
-   将样式表移到页首，脚本移交到页脚。

**5.优化图片**
适当地设置图片的格式并进行压缩可以节省大量的数据字节空间。

    解决方案：将图片上传至百度云，通过外链图片交由百度处理。
    不足：1.百度图片外链有log. 2.访问次数限制，不过对于目前需求足够。

优化修改完成后测试结果如下：

[caption id="" align="alignnone"
width="460"]![image](http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=046c22cd184c510faac4e21f50625410/eaf81a4c510fd9f95402a96c272dd42a2834a49a.jpg?referer=b28502da74094b3682852edd0478&x=.jpg)
PageSpeed Insights[/caption]

由于网站服务器在国外vps,且配置并不太好，这样的结果还是比较不错的。

[https://developers.google.com/speed/pagespeed/insights/](https://developers.google.com/speed/pagespeed/insights/)

[http://wiki.nginx.org/HttpGzipModuleChs](http://wiki.nginx.org/HttpGzipModuleChs)
