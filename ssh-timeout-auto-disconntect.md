Title: 避免ssh超时自动断开连接
Date: 2014-07-17 17:45
Author: chengz
Category: 每天进步一点点
Tags: linux
Slug: ssh-timeout-auto-disconntect

当用SSH Secure Shell连接Linux时，如果几分钟没有任何操作，连接就会断开。

修改/etc/ssh/sshd\_config文件，将`ClientAliveInterval 0`和`ClientAliveCountMax 3`的注释符号去掉,将ClientAliveInterval对应的0改成60,ClientAliveInterval指定了服务器端向客户端请求消息的时间间隔,
默认是0, 不发送.而ClientAliveInterval 60表示每分钟发送一次,
然后客户端响应, 这样就保持长连接了.ClientAliveCountMax,
使用默认值3即可.ClientAliveCountMax表示服务器发出请求后客户端没有响应的次数达到一定值,
就自动断开.
