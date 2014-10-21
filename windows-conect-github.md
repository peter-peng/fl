Title: windows连接github
Date: 2014-04-06 15:59
Author: chengz
Category: 技术流
Slug: windows-conect-github

使用putty的ssh连接github
========================

putty生成sshkey文件
-------------------

运行puttygen,点击generate，按提示生成一个并保存就好了。

git clone 报错
--------------

### 没有rsa2 key

    The server's host key is not cached in the registry. You  
    have no guarantee that the server is the computer you 
    think it is.
    The server's rsa2 key fingerprint is:
    ssh-rsa 2048 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48
    Connection abandoned.
    fatal: The remote end hung up unexpectedly

这个原因是主机没有加入信任hosts列表， 先用putty
ssh连接一次github.com，它会询问是否将rsa2 key存储在本机，点是就行了

### 验证方式错误

    FATAL ERROR: Disconnected: No supported authentication methods available
    fatal: The remote end hung up unexpectedly

1.  把windows的ssh key添加到github中的信任ssh key列表中。  
    打开puttygen load一个public key, 把public
    key内容copy到github中添加ssh key.
2.  启动pagent, 并且加载本地的sshkey文件。

使用git自带ssh连接
==================

配置文件
--------

在C:\\Users[user] 目录中新建一个.profile目录，内容如下  
GIT\_SSH="/usr/bin/ssh.exe"  
这样git就使用自带的ssh连接工具，.profile在Git
bash中才能起作用，所以要在Git bash中使用git
