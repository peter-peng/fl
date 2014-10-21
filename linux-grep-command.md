Title: Linux下常用的12个grep命令范例
Date: 2014-01-25 16:26
Author: neoyin
Category: 技术流
Tags: grep, linux
Slug: linux-grep-command

> Have you ever been confronted with the task of looking for a
> particular string or pattern in a file, yet have no idea where to
> start looking? Well then, here is grep to the rescue!

你是否面临过查找特殊字符的文件去不知从何下手?那么,就让**grep**命令来帮助你吧!

> **grep** is a powerful file pattern searcher that comes equipped on
> every distribution of Linux. If, for whatever reason, it is not
> installed on your system, you can easily install it via your package
> manager (apt-get on Debian/Ubuntu and yum on RHEL/CentOS/Fedora).

**grep**
命令是配备在每一个Linux发行版中的功能强大的文件搜索功能.如果某种原因你的系统没有安装,你可以通过系统携带的包安装器来安装它
(**apt-get on Debian/Ubuntu and yum on RHEL/CentOS/Fedora**).

    $ sudo apt-get install grep         #Debian/Ubuntu
    $ sudo yum install grep             #RHEL/CentOS/Fedora

<!--more-->

> I have found that the easiest way to get your feet wet with grep is to
> just dive right in and use some real world examples.

我发现在grep的世界中把脚弄湿的最简单方法就是直接去潜水....(咋翻译呀)

1. Search and Find Files
========================

> Let’s say that you have just installed a fresh copy of the new Ubuntu
> on your machine, and that you are going to give Python scripting a
> shot. You have been scouring the web looking for tutorials, but you
> see that there are two different versions of Python in use, and you
> don’t know which one was installed on your system by the Ubuntu
> installer, or if it installed any modules. Simply run this command:

假设你的机器上安装了最新的Ubuntu系统,你需要给Python脚本一个快照,你一直在网上找相关的教程,但是你看到了两个不同的Python版本,你不知道哪一下是Ubuntu系统安装的.你可以运行以下命令:(翻译得太烂了)

    # dpkg –l | grep –i python
    Sample Output
    ii  python2.7                        2.7.3-0ubuntu3.4                    Interactive high-level object-oriented language (version 2.7)
    ii  python2.7-minimal                2.7.3-0ubuntu3.4                    Minimal subset of the Python language (version 2.7)
    ii  python-openssl                   0.12-1ubuntu2.1                     Python wrapper around the OpenSSL library
    ii  python-pam                       0.4.2-12.2ubuntu4                   A Python interface to the PAM library

> First, we ran **dpkg –l**, which lists installed \*.deb packages on
> your system. Second, we piped that output to **grep –i** python, which
> simple states “go to grep and filter out and return everything with
> ‘python’ in it.” The –i option is there to ignore-case, as grep is
> case-sensitive. Using the –i option is a good habit of getting into,
> unless of course you are trying to nail down a more specific search.

首先我们运行**dpkg -l**,会列出系统中安装过的\*.deb包.
然后我们通过管道输出 **grep –i**
python,这个意思是"通过grep过滤出含有python并返回."
-i选项是忽略大小写(grep
命令是区分大小写的),用-i选项是一个良好的习惯,除非你在进行一个更具体的搜索

2. Search and Filter Files
==========================

> The grep can also be used to search and filter within individual files
> or multiple files. Lets take this scenario:

grep命令也可以对单个或多个文件进行搜索过滤.假设如下场景:

> You are having some trouble with your Apache Web Server, and you have
> reached out to one of the many awesome forums on the net asking for
> some help. The kind soul who replies to you has asked you to post the
> contents of your /etc/apache2/sites-available/default-ssl file.
> Wouldn’t it be easier for you, the guy helping you, and everyone
> reading it, if you could remove all of the commented lines? Well you
> can! Just run this:

你的Apache Web Server 遇到了一些麻烦,你通过一些相关论坛寻找帮助.**The
kind
soul**(不会翻译)回复你说需要你查看你/etc/apache2/sites-available/default-ssl文件的内容.如果能去除掉文件中所有的注释行,会对这些查看文件并帮助你的人更容易些.你可以运行:

    # grep –v “#”  /etc/apache2/sites-available/default-ssl

> The **–v** option tells grep to invert its output, meaning that
> instead of printing matching lines, do the opposite and print all of
> the lines that don’t match the expression, in this case, the \#
> commented lines.

**-v** 选项的意思是反转输出,即输入不匹配的行.在这里就是输出不含有\#的行.

3. Find all .mp3 Files Only
===========================

> The grep can be very useful for filtering from stdout. For example,
> let’s say that you have an entire folder full of music files in a
> bunch of different formats. You want to find all of the \*.mp3 files
> from the artist JayZ, but you don’t want any of the remixed tracks.
> Using a find command with a couple of grep pipes will do the trick:

**grep**可以非常有用从标准输出中过滤.例如,你有一个音乐文件夹中含有各中不同的格式.你想找出艺术家JayZ的\*.mp3文件,但是又不想要任何含有混音的曲目.你可以利用**find**和**grep**管道来处理:

    # find . –name “*.mp3” | grep –i JayZ | grep –vi “remix”

> In this example, we are using find to print all of the files with a
> \*.mp3 extension, piping it to grep –i to filter out and prints all
> files with the name “JayZ” and then another pipe to grep –vi which
> filters out and does not print all filenames with the string (in any
> case) “remix”.

这里我们先打印出所有\*.mp3文件,利用grep管道过滤出名字中含有"JayZ"再利用**grep
-vi**过滤出不含有"remix"的文件

4. Display Number of Lines Before or After Search String
========================================================

> Another couple of options are the –A and –B switches, which displays
> the matched line and number of lines either that come before or after
> the search string. While the man page gives a more detailed
> explanation, I find it easiest to remember the options as –A = after,
> and –B = before:

另外一对选项 **-A -B** 它会显示匹配的前或后数字行. 在man
page中可以查看到更多的解释.可以理解成**–A = after –B =
before**更容易一些:

    # ifconfig | grep -A4 eth0
    # ifconfig | grep -B2 UP

5. Prints Number of Lines Around Match
======================================

> The grep’s –C option is similar, but instead of printing the lines
> that come either before or after the string, it prints the lines in
> either direction:

grep的-C选项与上面-A
-B类似.但是此选项会同时打印出前后两个方向上的数字行:

    # ifconfig | grep -C2 lo

6. Count Number of Matches
==========================

> Similar to piping a grep string to word count (wc program) grep’s
> built-in option can perform the same for you:

类似grep管道的统计(wc) 可以执行grep的内置选项:

    # ifconfig | grep –c inet6

7. Search Files by Given String
===============================

> The –n option for grep is very useful when debugging files during
> compile errors. It displays the line number in the file of the given
> search string:

在编译错误调试过程中 grep -n选项是非常有用的.它会显示搜索内容的行号:

    # grep –n “main” setup..py

8. Search a string Recursively in all Directories
=================================================

> If you would like to search for a string in the current directory
> along with all of the subdirectories, you can specify the –r option to
> search recursively:

如果你想搜索当前文件夹和其子文件夹,你可能用-r选项上来递归搜索:

    # grep –r “function” *

9. Searches for the entire pattern
==================================

> Passing the –w option to grep searches for the entire pattern that is
> in the string. For example, using:

通过-w 选项你可以搜索字符串整个模式:(不知道怎么翻译了):

    ifconfig | grep -w "RUNNING"
    ifconfig | grep -w "RUN"
    什么也不会返回.除非是整个单词.

10. Search a string in Gzipped Files
====================================

> Deserving some mention are grep’s derivatives. The first is zgrep,
> which, similar to zcat, is for use on gzipped files. It takes the same
> options as grep and is used in the same way:

这里值得提出来的是grep的衍生品,第一是 **zgrep** ,类似 zcat,用于gzip文件.
操作和选项和grep是一样的:

    # zgrep –i error /var/log/syslog.2.gz

11. Match Regular Expression in Files
=====================================

> The egrep is another derivative that stands for “Extended Global
> Regular Expression”. It recognizes additional expression
> meta-characters such at + ? | and (). egrep is very useful for
> searching source files, and other pieces of code, should the need
> arise. It can be invoked from regular grep by specifying the –E
> option.

另一个衍生品是**egrep**
其全称是"全局扩展正则表达式",它可以识别更多的表达式和无字符.例如**+ ? |
and ()** .egrep 在搜索代码和源文件的时候非常有用.grep
可以通过-E选项来使用.

    # grep –E

12. Search a Fixed Pattern String
=================================

> The fgrep searches a file or list of files for a fixed pattern string.
> It is the same as grep –F. A common way of using fgrep is to pass a
> file of patterns to it:

**fgrep** 通过一个或多个文件来匹配搜索,可以如 grep
-F将匹配模式文件传递给它:

    # fgrep –f file_full_of_patterns.txt file_to_search.txt

> This is just a starting point with grep, but as you are probably able
> to see, it is invaluable for a variety of purposes. Aside from the
> simple one line commands we have implemented, grep can be used to
> write powerful cron jobs, and robust shell scripts, for a start. Be
> creative, experiment with the options in the man page, and come up
> with grep expressions that serve your own purposes!

这只是grep的一个开端,正如你可以看到的那样.它可以用在各种用途.除了我们上面简单的例子.grep可以用编写强大的cron任务和shell脚本,只要有创意.你可以通过man
page中的相关选项,编写出符合你自己目的的grep.

[http://www.tecmint.com/12-practical-examples-of-linux-grep-command/](http://www.tecmint.com/12-practical-examples-of-linux-grep-command/)
