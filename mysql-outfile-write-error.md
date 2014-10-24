Title: 解决select ... into outfile '..' mysql写文件权限问题
Date: 2012-03-02 12:29
Author: neoyin
Category: 技术流
Tags: mysql
Slug: mysql-outfile-write-error

`select * from test into outfile '/home/user/test.txt' `
在linux（centos）下 ，启动了mysql 并给用户文件读写的权利
`grant file on *.* to root@localhost;`
在linux系统上，目录的权限全部是 rwxrwxrwx
```
chmod 777 ...
/home/user/test
drwxrwxrwx 4 root root 4096 Sep 3 18:42 home
drwxrwxrwx 10 mapuser mapuser 4096 Sep 4 03:41 user
drwxrwxrwx 5 mapuser mapuser 4096 Sep 3 17:57 test
```

在mysql下输入
`select * from test into outfile '/home/user/test.txt'`
出现错误信息：

`ERROR 1 (HY000): Can't create/write to file '/home/user/test.txt' (Errcode: 13)`

当时如果是tmp目录的话就不会有这个错误
```
select * from test into outfile '/tmp/test.txt'
Query OK, 0 rows test(0.00 sec)
```

难道只能是tmp目录吗？
有什么地方可以修改的吗？

<!-- more -->

后来把home的所有者改成了mysql
```
drwxrwxrwx 5 mysql mysql 4096 Sep 4 10:08 home
select * from test into outfile '/home/test.txt'
ERROR 1 (HY000): Can't create/write to file '/home/test.txt' (Errcode: 13)
```
也是同样出错。
这个有什么办法可以写入home目录下面吗？或者其他什么目录，只要不是tmp目录，有人说先写入tmp目录，再cp到想要的目录，这样做是可以，不过比较麻烦，文件比较大，2-3G呢，
修改mysql的配置能实现吗？还是修改文件的权限，这个是什么问题呢？ 
```
select * from test into outfile '/tmp/test.txt'
Query OK, 0 rows test(0.00 sec)
```
看一下产生的这个文件的owner 是谁。
```
[root@localhost /]# ls -l
drwxrwxrwx 4 root root 4096 9月 4 21:03 home
drwxrwxrwt 10 root root 4096 9月 4 21:03 tmp
[root@localhost /]# mysql
Welcome to the MySQL monitor. Commands end with ; or \g.
Your MySQL connection id is 27
Server version: 5.1.14-beta MySQL Community Server (GPL)
Type 'help;' or '\h' for help. Type '\c' to clear the buffer.
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
Database changed
mysql> select user from user;
+--------+
| user |
+--------+
| system | 
| root | 
+--------+
2 rows in set (0.03 sec)
mysql> select user from user into outfile '/home/test.txt';
Query OK, 2 rows affected (0.02 sec)
[root@localhost home]# ls -l
-rw-rw-rw- 1 mysql mysql 12 9月 4 21:12 test.txt
[root@localhost home]# cat /home/test.txt
system
root
select * from test into outfile '/home/test.txt'
ERROR 1 (HY000): Can't create/write to file '/home/test.txt' (Errcode: 13)
```

从Errcode: 13来看是没权限
你执行上面语句时，是用什么用户执行的呢？检查下这个用户是否有权限吧
估计和权限没关系，因为已经是777了。
看看是不是selinux打开了，如果没什么特别需要的话，关了为好。
非root用户，在mysql下执行的
```
select * from test into outfile '/home/user/test.txt'`
select * from test into outfile '/home/user/test.txt'该语句产生的文件是
-rw-rw-rw- 1 mysql mysql 12 9月 4 21:12 test.txt
```
mysql组的mysql用户的。
貌似和权限没什么关系，我用root用户登陆系统，执行mysql的语句，其结果还是一样，写入/home目录时
```
select * from test into outfile '/home/test.txt'
ERROR 1 (HY000): Can't create/write to file '/home/test.txt' (Errcode: 13)
```
还是有这个问题。
selinux会阻止其他程序写入操作？？
具体怎么改变一下selinx的配置呢
我理清是什么问题了。
在red hat系列的linux中selinux对哪些daemon可以进行怎么样的操作是有限制的，mysql的select into outfile的命令是mysql的daemon来负责写文件操作的。写文件之前当然要具有写文件的权限。而selinux对这个权限做了限制。如果 selinux是关闭的吧，这个命令执行是没有问题的
```
mysql> select user from user into outfile '/home/test.txt';
Query OK, 2 rows affected (0.02 sec)
```
当时selinux开启时
selinux对mysql的守护进程mysqld进行了限制。
```
mysql> select user from user into outfile '/home/test.txt';
ERROR 1 (HY000): Can't create/write to file '/home/test.txt' (Errcode: 13)
```

出现了没有权限写的error。
解决方法，可以关闭selinux。
可以在/etc/selinux中找到config
root用户，
```
shell>vi /etc/selinux/config
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
# enforcing - SELinux security policy is enforced.
# permissive - SELinux prints warnings instead of enforcing.
# disabled - SELinux is fully disabled.
SELINUX=enforcing
```
修改SELINUX=disabled关闭selinux就可以了，这个问题就可以解决了。
不过全部关闭SELINUX有带来一些安全问题。
当然也可以，单独给mysql的守护进程权限，
```
shell>getsebool -a可以查看当前的对系统一系列守护进程的权限情况。
lpd_disable_trans --> off
mail_read_content --> off
mailman_mail_disable_trans --> off
mdadm_disable_trans --> off
mozilla_read_content --> off
mysqld_disable_trans --> off
nagios_disable_trans --> off
named_disable_trans --> off
named_write_master_zones --> off
nfs_export_all_ro --> on
nfs_export_all_rw --> on
nfsd_disable_trans --> off
nmbd_disable_trans --> off
nrpe_disable_trans --> off
shell>setsebool -P mysqld_disable_trans=1
开启对mysql守护进程的权限，这样
mysql> select user from user into outfile '/home/test.txt';
```

写入到自定义的目录就没有问题了。
-P表示 是永久性设置，否则重启之后又恢复预设值。
getsebool setsebool命令在root用户下有权限。
除了对selinux的权限，当然首先要保证该目录拥有读写权限。
在ubuntu下 ，可以对AppArmor(/etc/apparmor.d/usr.sbin.mysqld) 修改，类似selinux。
添加/etc/squid/lists/eighties.txt w,类似。
