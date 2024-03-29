Title: Linux系统性能和使用活动监控工具 sysstat
Date: 2014-10-24 14:52
Author: neoyin
Category: 技术流
Tags: linux,sysstat
Slug: linux-command-sysstat

Sysstat是一个非常方便的工具，它带有众多的系统资源监控工具，用于监控系统的性能和使用情况。我们在日常使用的工具中有相当一部分是来自sysstat工具包的。同时，它还提供了一种使用cron表达式来制定性能和活动数据的收集计划。

<!-- more -->

下表是包含在sysstat包中的工具

- iostat: 输出CPU的统计信息和所有I/O设备的输入输出（I/O）统计信息。
- mpstat: 关于CPU的详细信息（单独输出或者分组输出）。
- pidstat: 关于运行中的进程/任务、CPU、内存等的统计信息。
- sar: 保存并输出不同系统资源（CPU、内存、IO、网络、内核等。。。）的详细信息。
- sadc: 系统活动数据收集器，用于收集sar工具的后端数据。
- sa1: 系统收集并存储sadc数据文件的二进制数据，与sadc工具配合使用
- sa2: 配合sar工具使用，产生每日的摘要报告。
- sadf: 用于以不同的数据格式（CVS或者XML）来格式化sar工具的输出。
- Sysstat: sysstat工具的man帮助页面。
- nfsiostat: NFS（Network File System）的I/O统计信息。
- cifsiostat: CIFS(Common Internet File System)的统计信息。

最近(在2014年6月17日)，sysstat 11.0.0（稳定版）已经发布了，同时还新增了一些有趣的特性，如下：

pidstat命令新增了一些新的选项：首先是“-R”选项，该选项将会输出有关策略和任务调度的优先级信息。然后是“-G”选项，通过这个选项我们可以使用名称搜索进程，然后列出所有匹配的线程。

sar、sadc和sadf命令在数据文件方面同样带来了一些功能上的增强。与以往只能使用“saDD”来命名数据文件。现在使用-D选项可以用“saYYYYMMDD”来重命名数据文件，同样的，现在的数据文件不必放在“var/log/sa”目录中，我们可以使用“SA_DIR”变量来定义新的目录，该变量将应用与sa1和sa2命令。

#### 在Linux系统中安装sysstat

在主要的linux发行版中，‘sysstat’工具包可以在默认的程序库中安装。然而，在默认程序库中的版本通常有点旧，因此，我们将会下载源代码包，编译安装最新版本（11.0.0版本）。

首先，使用下面的连接下载最新版本的sysstat包，或者你可以使用wget命令直接在终端中下载。

- http://sebastien.godard.pagesperso-orange.fr/download.html
```
# wget http://pagesperso-orange.fr/sebastien.godard/sysstat-11.0.0.tar.gz
```
下载sysstat包

然后解压缩下载下来的包，进去该目录，开始编译安装
```
# tar -xvf sysstat-11.0.0.tar.gz 
# cd sysstat-11.0.0/
```

这里，你有两种编译安装的方法：

a)
第一，你可以使用iconfig(这将会给予你很大的灵活性，你可以选择/输入每个参数的自定义值)
```
# ./iconfig
```

sysstat的iconfig命令

b)
第二，你可以使用标准的configure，在命令行中定义所有选项。你可以运行 ./configure –help 命令来列出该命令所支持的所有限选项。

```
# ./configure --help
```

stsstat的cofigure -help

在这里，我们使用标准的./configure命令来编译安装sysstat工具包。

```
# ./configure
# make
# make install
```

在Linux系统中配置sysstat

在编译完成后，我们将会看到一些类似于上图的输出。现在运行如下命令来查看sysstat的版本。
```
# mpstat -V
 
sysstat version 11.0.0
(C) Sebastien Godard (sysstat <at> orange.fr)
```
#### 更新Linux 系统中的sysstat

默认的，sysstat使用“/usr/local”作为其目录前缀。因此，所有的二进制数据/工具都会安装在“/usr/local/bin”目录中。如果你的系统已经安装了sysstat 工具包，则上面提到的二进制数据/工具有可能在“/usr/bin”目录中。

因为“$PATH”变量不包含“/usr/local/bin”路径，你在更新时可能会失败。因此，确保“/usr/local/bin”路径包含在“$PATH”环境变量中，或者在更新前，在编译和卸载旧版本时将-prefix选项指定值为“/usr”。
```
# yum remove sysstat            [On RedHat based System]
# apt-get remove sysstat        [On Debian based System]

# ./configure --prefix=/usr
# make
# make install
```
现在，使用‘mpstat’命令的‘-V’选项查看更新后的版本。
```
# mpstat -V
 
sysstat version 11.0.0
(C) Sebastien Godard (sysstat <at> orange.fr)
```
---

参考:
[ Sysstat Documentation](http://sebastien.godard.pagesperso-orange.fr/documentation.html)




