Title: Mac os Homebrew安装nginx
Date: 2013-12-05 17:04
Author: neoyin
Category: 技术流
Tags: apple, homebrew, nginx
Slug: mac-os-homebrew-install-nginx

Homebrew作为OS
X上强大的包管理器，为系统软件提供了非常方便的安装方式,就如同Ubuntu
系统下apt-get和Center OS 下yum 一样。

安装命令简单：

    brew install nginx

<!--more-->

由于系统升级的原因c++标准库发生了变化导致如下错误

    Error: nginx dependency pcre was built with the following
    C++ standard library: libstdc++ (from clang)

    This is incompatible with the standard library being used
    to build nginx: libc++ (from clang)
    说明：https://github.com/mxcl/homebrew/wiki/C++-Standard-Libraries

乘着重装homebrew时其整理一上

    #删除原有相关数据
    rm -rf /usr/local/Cellar /usr/local/.git && brew cleanup
    #安装
    ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go/install)"

安装所需前提条件：

-   基于Intel CPU
-   操作系统为Mac OS X 10.5 Leopard或更高版本
-   已安装版本管理工具Git（Mac OS X 10.7 Lion已经预安装）
-   已安装Xcode开发工具
-   已安装Java Developer Update

homebrew使用

查看brew的帮助

    brew --help

安装软件

    brew install git

卸载软件

    brew uninstall git

搜索软件

    brew search git

显示已经安装软件列表

    brew list

更新软件，把所有的Formula目录更新，并且会对本机已经安装并有更新的软件用\*标明。

    brew update

更新某具体软件

    brew upgrade git

查看软件信息

brew [info | home] [FORMULA...]  
删除程序，和upgrade一样，单个软件删除和所有程序老版删除。  
brew cleanup git  
brew cleanup

查看那些已安装的程序需要更新

    brew outdated

程序安装路径及文件夹  

Homebrew将本地的/usr/local初始化为git的工作树，并将目录所有者变更为当前所操作的用户，以后的操作将不需要sudo。

    -bin 用于存放所安装程序的启动链接（相当于快捷方式）
    -Cellar 所以brew安装的程序，都将以[程序名/版本号]存放于本目录下
    -etc brew安装程序的配置文件默认存放路径
    -Library Homebrew 系统自身文件夹
    +–Formula 程序的下载路径和编译参数及安装路径等配置文件存放地
    +–Homebrew brew程序自身命令集

安装nginx信息如下：

    brew install nginx
    ==> Installing nginx dependency: pcre
    ==> Downloading http://downloads.sourceforge.net/project/pcre/pcre/8.33/pcre-8.33.tar.bz2
    ######################################################################## 100.0%
    ==> ./configure --prefix=/usr/local/Cellar/pcre/8.33 --enable-utf8 --enable-unicode-properties --enable-pcregr
    ==> make
    ==> make test
    ==> make install

homebrew 居然将默认的80端口变成了8080 木有言语了.

nginx命令如下,小伙伴们快去试一下吧.

    常用的指令有： 
    nginx -V 查看版本，以及配置文件地址
    nginx -v 查看版本
    nginx -c filename 指定配置文件
    nginx -h 帮助
    nginx -s [reload\reopen\stop\quit]

[http://brew.sh/index\_zh-cn.html](http://brew.sh/index_zh-cn.html)

[http://linfan.info/blog/2012/02/25/homebrew-installation-and-usage/](http://linfan.info/blog/2012/02/25/homebrew-installation-and-usage/)
