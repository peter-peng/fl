Title: 配置yum的安装源
Date: 2014-10-06 16:59
Author: chengz
Category: 每天进步一点点
Tags: linux
Slug: yum-source-config

配置yum的安装源
===============

yum的配置文件是 `/etc/yum.conf`, 安装源的配置在目录 `/etc/yum.repos.d`
中.  
把不用的安装源配置文件备份或删除, 重新编辑一个配置文件,
将要用的源配置粘贴.  
然后执行 `yum clean all  && yum makecache` 重新更新一下仓库文件缓存.

一个可用的源配置:

    # centos 5.x
    wget http://mirrors.163.com/.help/CentOS5-Base-163.repo

    # centos 6.x
    wget http://mirrors.163.com/.help/CentOS6-Base-163.repo

refs:

[163 网易 CentOS镜像使用帮助](http://mirrors.163.com/.help/centos.html)  
[配置 yum
源的两种方法](http://www.cnblogs.com/shuaixf/archive/2011/11/30/2268496.html)  
[redhat
yum源安装与配置](http://blog.chinaunix.net/uid-20729583-id-2972852.html)
