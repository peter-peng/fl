Title: Apple OS X 开启NFS server
Date: 2013-11-22 16:10
Author: neoyin
Category: 技术流
Tags: apple,nfs
Slug: apple-os-nfs-server

Macbook pro系统中程序员命令操作并不太友好,本人也不想mac
os中做太多开发和研究而装一堆软件,所以用虚拟机开了一个Ubuntu系统用来平时瞎研究.

由于虚拟的Ubuntu 系统并没有初始化太大的空间,也想将存储分开.所以就是mac
Os NFS server + Ubuntu nfs client 的构架了.

Mac os NFS 操作必需要不一样. 所以简单的google了一下

这货必需要要有图形界面的吧,果然如下链接:

[http://www.bresink.com/osx/NFSManager.html](http://www.bresink.com/osx/NFSManager.html)

还要打开一个application操作神马的并不是哥想要的.

在/etc/exports编辑 (如果没有此文件,需要创建)

    /www -maproot=root -alldirs -rw

    #可以man查看:man exports 查看更多相关设置参数
    #So easy 
    #开启nfsd
    sudo nfsd enable
    sudo nfsd start

Ubuntu中设置

    sudo mount -t nfs ${master_ip}:/${master_path} /${mount_point}
    #也可以在/etc/fstab中添加用以开机自运行 
    ${master_ip}:/${master_path} /${mount_point} nfs rw,hard 0 0

需要注意的，如果路径中含有空字符需要\\040 转义。  
That is all.

参考：

- [http://www.manpagez.com/man/5/exports/](http://www.manpagez.com/man/5/exports/)
- [http://www.barryodonovan.com/index.php/2012/12/12/apple-os-x-as-an-nfs-server-with-linux-clients](http://www.barryodonovan.com/index.php/2012/12/12/apple-os-x-as-an-nfs-server-with-linux-clients)
