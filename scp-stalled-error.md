Title: Scp stalled 问题解析
Date: 2013-05-09 00:13
Author: neoyin
Category: 技术流
Slug: scp-stalled-error

今天在做备份的时候，远程将tar.gz
文件scp到本地时，时不时出现stalled的情况。

这个文件也不是太大。就100+M左右

网上搜索了一下如下：

When transferring large files(for example mksysb images) using scp
through a firewall, the scp connection stalls.

Cause:
------

The reason for scp to stall, is because scp greedily grabs as much
bandwith of the network as possible when it transfers files, any delay
caused by the network switch of the firewall can easily make the TCP
connection stalled.

Solution:
---------

There’s a solution to this problem: Add “-l 8192″ to the scp command.

Adding the option “-l 8192″ limits the scp session bandwith up to 8192
Kbit/second, which seems to work safe and fast enough (up to 1
MB/second):

scp
对带宽的使用是贪婪的，有多少的带宽他就会使用多少，但是如果一旦由于网络设备或者防火墙造成了一些延时，就会导致传输出现
TCP stalled

这种情况可以通过限速来解决：

另外还有个次要原因，可能是由于 Linux 下的 SACK
的造成。一般开启限速功能应该就能解决绝大多数的情况了。SACK
是什么请看[这里](http://packetlife.net/blog/2010/jun/17/tcp-selective-acknowledgments-sack/)。  
要使用 TCP option 中的 SACK，需要两端都支持。

根据[这篇](http://serverfault.com/questions/10955/when-to-turn-tcp-sack-off)文章的描述，在局域网或者低延迟的网络中，就完全没有必要开启
SACK options 了，另外在很小的带宽下，比如 1Mbps
的情况下，同样没有必要开启这个选项。该选项可以在 "high bandwidth, lossy
(or high delay)" 的情况下开启。因此出现 stalled
的情况一般都是在客户从公网连接某台服务器，带宽一般都是比较小的，瓶颈带宽在客户端这边，而不大可能是两台同一链路(连在同一台交换机)的服务器之间的数据传输。因此关闭
SACK 会比较有利传输:  
`# echo 0 > /proc/sys/net/ipv4/tcp_sack`
