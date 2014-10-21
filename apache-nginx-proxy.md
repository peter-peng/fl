Title: Apache Nginx正向和反向代理
Date: 2013-12-16 14:12
Author: neoyin
Category: 技术流
Tags: apache, nginx, proxy
Slug: apache-nginx-proxy

最近需要调试手机端与服务器端的网络通讯协议,需要对网络封包的截取和分析,所以考虑在机器上做一层代理.通过此方案来抓取分析网络包.

![image](http://f.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=6c5cec13b119ebc4c478769cb21dbec1/0b55b319ebc4b74556c46460cdfc1e178a821577.jpg?referer=1f16e44e79f40ad14cf3f2d3532e&x=.jpg)

<dfn>正向代理</dfn>是一个位于客户端和*原始服务器(origin
server)*之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。客户端必须要进行一些特别的设置才能使用正向代理。

正向代理的典型用途是为在防火墙内的局域网客户端提供访问Internet的途径。

<!--more-->

可以通过如下配制Apache下正向代理

    #Apache 正向代理 
    ProxyRequests On
    ProxyVia On
    <Proxy *>
      Order deny,allow
      Deny from all
      Allow from all
    </Proxy>

Nginx 下的正向代理配置

    #nginx实现代理上网，有三个关键点必须注意，其余的配置跟普通的nginx一样
    #1.增加dns解析resolver
    #2.增加无server_name名的server
    #3.proxy_pass指令
    http {
        resolver 8.8.8.8;
            server {
                listen 8088;
                location / {
                    proxy_pass http://$http_host$request_uri;
                }
        }
    }
    #重启nginx

<dfn>反向代理</dfn>正好相反，对于客户端而言它就像是原始服务器，并且客户端不需要进行任何特别的设置。客户端向反向代理的命名空间(name-space)中的内容发送普通请求，接着反向代理将判断向何处(原始服务器)转交请求，并将获得的内容返回给客户端，就像这些内容原本就是它自己的一样。

![image](http://g.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=cccd856160d0f703e2b295d938c12000/622762d0f703918fe632eebc533d269758eec4e1.jpg?referer=92f4ce72369b033b759fc8ea24bf&x=.jpg)

反向代理的典型用途是将防火墙后面的服务器提供给Internet用户访问。反向代理还可以为后端的多台服务器提供负载平衡，或为后端较慢的服务器提供缓冲服务。另外，还可以启用高级URL策略和管理技术，从而使处于不同web服务器系统的web页面同时存在于同一个URL空间下。

Nginx配置反向代码

    upstream xxx_server_backend {
            server 192.168.1.xx:81;
    }

    server {
            listen 80;
            access_log logs/access.www.xxx.com.log main;
            error_log logs/error.www.xxx.com.log;
            server_name www.xxx.com;

            location / {
    #               auth_basic "Restricted";
    #               auth_basic_user_file  xxx.com/xxx_test.pwd;

                    #limit_req zone=www_zone;
                    #more_set_headers "Cache-Control" "no-cache";
                    proxy_cache off;
                    proxy_next_upstream http_502 http_504 error timeout invalid_header;
                    proxy_ignore_headers   Expires Cache-Control;
            #       proxy_ignore_headers    Cache-Control   no-cache;
                    proxy_store         off;
                    proxy_set_header        Host            $host;
                    proxy_set_header        X-Real-IP       $remote_addr;
                    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
                    proxy_pass http://xxx_server_backend;
                    more_clear_headers  "Cache-Control";
                    add_header      Cache-Control "no-cache,max-age=0";
                    #more_set_headers       "Cache-Control" "no-cache";
                    #expires        0;
            }

    }

Apache配置反向代理

    ProxyRequests Off
    <Proxy *>
       Order deny,allow
       Allow from all
    </Proxy>

    ProxyPass /foo http://foo.example.com/bar
    ProxyPassReverse /foo http://foo.example.com/bar

参考:

[http://apache.jz123.cn/mod/mod\_proxy.html](http://apache.jz123.cn/mod/mod_proxy.html)

[http://www.nginx.cn/482.html](http://www.nginx.cn/482.html)

[http://wiki.nginx.org/NginxChs](http://wiki.nginx.org/NginxChs)

[http://lenky.info/archives/2013/01/12/2263](http://lenky.info/archives/2013/01/12/2263)
