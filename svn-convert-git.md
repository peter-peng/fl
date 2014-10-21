Title: svn 仓库转为 git
Date: 2014-06-21 02:43
Author: chengz
Category: 每天进步一点点
Slug: svn-convert-git

svn 仓库转为 git仓库
====================

安装git-svn  
创建作者文件,格式如下

    guaniu = Leeiio <xxx@xxx.com>

克隆svn仓库

    $ git svn clone <SVN版本库地址> --no-metadata -A authors.txt -t tags -b branches -T trunk <转换的目的目录名>

[其他转换branch
tag等,看链接](http://www.cnblogs.com/analyzer/articles/1736729.html)  
[svn2git 的转换方法](https://github.com/nirvdrum/svn2git)
