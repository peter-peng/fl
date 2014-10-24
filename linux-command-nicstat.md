Title: (Linux命令) nicstat 网络流量统计利器
Date: 2013-02-27 15:11
Author: neoyin
Category: 技术流
Tags: Linux,nicstat
Slug: linux-command-nicstat

----

前段时间看到brendangregg的 Linux Performance Analysis and Tools PPT里面提到的nicstat，研究了下是个不错的东西，分享给大家。
```
nicstat is to network interfaces as “iostat” is to disks, or “prstat” is to processes.
```
nicstat原本是Solaris平台下显示网卡流量的工具，Tim Cook将它移植到linux平台，官方网站见 这里。 相比netstat, 他有以下关键特性：

- Reports bytes in & out as well as packets.
- Normalizes these values to per-second rates.
- Reports on all interfaces (while iterating)
- Reports Utilization (rough calculation as of now)
- Reports Saturation (also rough)
- Prefixes statistics with the current time

我们来体验下，首先安装之，源码在 这里 下， 目前最新的版本是1.92。
解开后，由于这个版本默认是在32位linux下编译，所以需要改下Makefile.Linux：
```
$ diff Makefile.Linux64 Makefile.Linux
17c17
< CFLAGS = $(COPT) -m32
---
> CFLAGS = $(COPT)

$ sudo make -f Makefile.Linux install
```
就安装好了。

使用文档在这里： `man nicstat`
由于在linux下需要获取网卡的speed等信息，需要要以特权用户运行。
```
$ sudo enicstat -l
Int      Loopback   Mbit/s Duplex State
lo        Yes        -   unkn    up
eth0        No     1000   full    up
bond0        No        0   unkn    up

$ sudo enicstat
    Time      Int   rKB/s   wKB/s   rPk/s   wPk/s    rAvs    wAvs %Util    Sat
13:04:10       lo   161.1   161.1   489.2   489.2   337.3   337.3  0.00   0.00
13:04:10     eth0   601.9   589.1  1868.1  1894.1   330.0   318.5  0.98   0.00
13:04:10    bond0   601.9   589.1  1868.1  1894.1   330.0   318.5  0.00   0.00
```

上面很特色的一个选项就是网卡的Util（利用率）以及Saturation（This the number of errors/second seen for the interface）在实践中用途挺大的。

通过strace和源码进行简单的分析：
```
open(“/proc/net/dev”, O_RDONLY) = 3
open(“/proc/net/snmp”, O_RDONLY) = 5
open(“/proc/net/netstat”, O_RDONLY) = 6
open(“/proc/uptime”, O_RDONLY) = 9
```
大部分统计信息是从上面3个文件获取到的，snmp提供了tcp和udp的统计信息。

默认统计信息 以KB为单位，-M选项可以更改为以M为单位。

```
$ sudo enicstat -M
    Time      Int   rMbps   wMbps   rPk/s   wPk/s    rAvs    wAvs %Util    Sat
13:14:51       lo    1.26    1.26   489.1   489.1   337.3   337.3  0.00   0.00
13:14:51     eth0    4.70    4.60  1868.0  1894.0   330.0   318.5  0.98   0.00
13:14:51    bond0    4.70    4.60  1868.0  1894.0   330.0   318.5  0.00   0.00
```

nicstat提供了tcp链接外联和内联的个数，重置，Drops信息还有包重传率等信息，这些信息对于诊断tcp问题挺有用的。
```
$ sudo enicstat -t
13:09:40    InKB   OutKB   InSeg  OutSeg Reset  AttF %ReTX InConn OutCon Drops
TCP        0.00    0.00  2123.7  2251.7  1.84  0.79 0.000   7.16   2.95  0.00
```
最后需要注意的是, 解读输出信息的时候：

>NOTES
On Linux, the NoCP, Defer, TCP InKB, and TCP OutKB statistics are always reported as zero.
The way that saturation is reported is a best effort, as there is no standardized naming to capture
all errors related to an interface’s inability to receive or transmit a packet. Monitoring %Util and
packet rates, along with an understanding of the specific NICs may be more useful in judging whether
you are nearing saturation.

 

