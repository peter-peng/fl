Title: X-Forwarded-For引发的悬案
Date: 2014-03-27 16:01
Author: neoyin
Category: 技术流
Tags: nginx
Slug: haproxy-x-forwarded-for


最近线上nginx服务压力过大，运维的小伙伴们扩充了相关的服务器，在nginx服务器上层又加了一层haproxy服务器
现在的线上的结构变成

      　haproxy
      |    　　　| 
    nginx... nginx...(负载均衡)
      |      　 |    　　　　　　　　　　　| 
    nginx ... nginx .(app server转发).. nginx (统计服务)
      |        |           |
    tomcat... tomcat .. tomcat 

但是在加了一层haproxy之后，发现统计系统中数据突降。

这就奇了个怪，怎么会出现这种情况?怎么能出现这种情况？按道理讲这数据应该上升的趋势呀。

看看日志发现MS每个请求都很正常。正在这时，一个运营的小伙伴反馈说在后台发现很多登陆的用户都是内网用户。

根据这个反馈，查看了一下数据库，发现所有的用户登陆ip都变成了haproxy的ip地址.

我了个去，这个问题太严重了。

先来看一下程序中是怎么获得用户ip的

    public static String getIpAddr(HttpServletRequest request) {
            String ip = request.getHeader("x-forwarded-for");
            if (ip != null && ip.length() > 0 && !"unknown".equalsIgnoreCase(ip)) {
                String[] ips = ip.split(",");
                if (ips.length > 0) {
                    ip = ips[0];
                }
            }
            #...
            return ip;
        }

<!--more-->

程序中是根据header中的x-forwarded-for 来拿到用户ip的,这没问题。

我们再来看一下上层nginx 的配置

    location / {
    proxy_ignore_headers Expires Cache-Control;
    proxy_store off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://127.0.0.1:8080;
    more_clear_headers "Cache-Control";
    add_header Cache-Control "no-cache,max-age=0";

    }

在这里的 `X-Forwarded-For 为 $proxy_add_x_forwarded_for;`
得再去它的上游看一看。

    location / {
    default_type text/plain;
    charset utf-8;

    proxy_cache off;
    proxy_connect_timeout 120s;
    proxy_next_upstream http_502 http_504 error invalid_header;
    proxy_ignore_headers Expires Cache-Control;
    proxy_store off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    more_clear_headers "Cache-Control";
    add_header Cache-Control "no-cache,max-age=0";

    proxy_pass http://lxweb_server_backend;

    allow all;
    }

这里的`X-Forwarded-For 仍然是 $proxy_add_x_forwarded_for;`
而它的上游就是haproxy了，程序中获得的ip也是haproxy的ip
.问题肯定在haproxy的配置上了。 再看一看haproxy的配置

    option http-server-close
     option forwardfor header X-Real-IP

option forwardfor
：如果服务器上的应用程序想记录发起请求的客户端的IP地址，需要在HAProxy上配置此选项，这样HAProxy会把客户端的IP信息发送给服务器，在HTTP请求中添加"X-Forwarded-For"字段。

本来在配置中加上
`option forwardfor`即可。可是运维的小伙伴们在后面加上了`header X-Real-IP`
结果导致header中x-forwarded-for以x-real-ip 转发给下游，下游nginx取到的
X-Forwarded-For
指向$proxy\_add\_x\_forwarded\_for结果全部都是haproxy的ip了.

是的。整个过程就是这样子的。修改了haproxy配置后。数据正常。用户登陆ip正常显示了。
（完）
