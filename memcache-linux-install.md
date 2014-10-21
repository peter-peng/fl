Title: 缓存的重要性（Linux下安装Memcache）
Date: 2013-06-21 16:05
Author: neoyin
Category: 技术流
Tags: memcache
Slug: memcache-linux-install

Cache就是高速缓冲存储器
一种特殊的存储器子系统，其中复制了频繁使用的数据以利于快速访问，

现需要安装一些memcached来做测试

主页：https://code.google.com/p/memcached

目前最新版本为1.4.15

Linux下 需要先安装 libevent

<div>
You'll likely need to install the development package for libevent

-   **Ubuntu:** `apt-get install libevent-dev`
-   **Redhat/Fedora:** `yum install libevent-devel`

</div>
根据文档 https://code.google.com/p/memcached/wiki/NewInstallFromSource

    wget http://memcached.org/latest
    tar -zxvf memcached-1.x.x.tar.gz
    cd memcached-1.x.x
    ./configure --prefix=/usr/local/memcached
    make && make install

启动Memcache的服务器端：  
\# /usr/local/bin/memcached -d -m 10 -u root -l 192.168.0.200 -p 12000
-c 256 -P /tmp/memcached.pid

-d选项是启动一个守护进程，  
-m是分配给Memcache使用的内存数量，单位是MB，我这里是10MB，  
-u是运行Memcache的用户，我这里是root，  

-l是监听的服务器IP地址，如果有多个地址的话，我这里指定了服务器的IP地址192.168.0.200，  
-p是设置Memcache监听的端口，我这里设置了12000，最好是1024以上的端口，  

-c选项是最大运行的并发连接数，默认是1024，我这里设置了256，按照你服务器的负载量来设定，  
-P是设置保存Memcache的pid文件

OK Done! So easy

https://code.google.com/p/memcached/wiki/NewInstallFromSource

http://www.ccvita.com/257.html
