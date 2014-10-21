Title:  MySQL启多个实例进行主从复制
Date: 2013-04-29 13:55
Author: neoyin
Category: 技术流
Tags: master-slave, mysql
Slug: mysql-multiple-instances-master-slave

最近开发过程中服务器紧缺，但又需要一些mysql
服务器来做测试，没办法只好在一台服务器上run 多个Mysql 实例来做了。

首先需要了解一下**mysqld读取my.cnf的顺序：**

**１首先读取/etc/my.cnf，多实例这个配置文件不会存在。  
２$datadir/my.cnf，在data目录下寻找此配置文件。  
３defaultfile=/path/my.cnf 通常写在命令行上，mysqld\_safe
defaultfile=/tmp/my.cnf &等执行。  
４\~/my.cnf 当前用户下的配置文件。**

**mysqld多进程运行的必要条件，
pid文件，datadir，socket，port是独立分开的。**

~~~~ {lang="LANGUAGE" line="1"}
[mysqld_multi]
mysqld = /usr/bin/mysqld_safe
mysqladmin = /usr/bin/mysqladmin
user = root
log = /data/mysql_mult/mysqld_mult.log

[mysqld]
datadir=/data/mysql
socket=/data/mysql/mysql.sock
user=mysql
# Default to using old password format for compatibility with mysql 3.x
# clients (those using the mysqlclient10 compatibility package).
old_passwords=1

# Disabling symbolic-links is recommended to prevent assorted security risks;
# to do so, uncomment this line:
# symbolic-links=0

[mysqld1]
socket = /data/mysql_mult/3301/mysql_3301.sock
port = 3301
pid-file = /var/run/mysqld/mysql_3301.pid
datadir = /data/mysql_mult/3301
log = /var/log/mysqld_3301.log
user = mysql

[mysqld2]
socket = /data/mysql_mult/3302/mysql_3302.sock
port = 3302
pid-file = /var/run/mysqld/mysql_3302.pid
datadir = /data/mysql_mult/3302
log = /var/log/mysqld_3302.log
user = mysql
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
~~~~

**启动mysqld 的两个实例**

~~~~ {lang="LANGUAGE" line="1"}
/usr/bin/mysqld_multi –config-file=./my.cnf start 1,2
~~~~

保证各自己目录相关权限正确

初始化数据：bin/mysql\_install\_db --datadir=/${datadir} --user=mysql

查看mysql是否启动并正常监听

~~~~ {lang="LANGUAGE" line="1"}
ps aux | grep mysqld
netstat anouletp | grep 33
mysql -P3301 -S /${socketPath}
~~~~

关于master-slave 配置:

~~~~ {line="1"}
[mysqld_multi]
mysqld = /usr/bin/mysqld_safe
mysqladmin = /usr/bin/mysqladmin
user = root
log = /kvm/192.168.2.6/data/mysql_mult/mysqld_mult.log

[mysqld]
datadir=/kvm/192.168.2.6/data/mysql
socket=/kvm/192.168.2.6/data/mysql/mysql.sock
user=mysql
# Default to using old password format for compatibility with mysql 3.x
# clients (those using the mysqlclient10 compatibility package).
old_passwords=1

# Disabling symbolic-links is recommended to prevent assorted security risks;
# to do so, uncomment this line:
# symbolic-links=0

[mysqld1]
socket  = /lamp/mysql-multi/33dd07/mysql.sock
socket = /kvm/192.168.2.6/data/mysql_mult/3301/mysql_3301.sock
port = 3301
pid-file = /var/run/mysqld/mysql_3301.pid
datadir = /kvm/192.168.2.6/data/mysql_mult/3301
log = /var/log/mysqld_3301.log
user = mysql

default-character-set=utf8
skip-name-resolve

#about master slave
server-id=1
log-bin=mysql-bin
binlog-do-db=lifeix_app
binlog-ignore-db=mysql,test

[mysqld2]
socket = /kvm/192.168.2.6/data/mysql_mult/3302/mysql_3302.sock
port = 3302
pid-file = /var/run/mysqld/mysql_3302.pid
datadir = /kvm/192.168.2.6/data/mysql_mult/3302
log = /var/log/mysqld_3302.log
user = mysql

default-character-set=utf8
skip-name-resolve
#skip-grant-tables

server-id=2
master-port=3302                           # 同步所用端口
master-connect-retry=60                    # 断点从新连接时间
replicate-ignore-db=mysql,test             # 屏蔽对mysql库的同步
replicate-do-db=lifeix_app                 # 同步的数据库的名称


[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

[client]
#socket=/kvm/192.168.2.6/data/mysql/mysql.sock
socket=/kvm/192.168.2.6/data/mysql_mult/3301/mysql_3301.sock
~~~~

其它master-slave 配置雷同，就不一一陈列。


