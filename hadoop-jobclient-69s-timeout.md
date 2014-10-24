Title: hadoop jobclient69秒超时问题
Date: 2013-12-01 14:40
Author: fuqiang
Category: 技术流
Tags: hadoop
Slug: hadoop-jobclient-69s-timeout

```
FSOutputStream ResponseProcessor exception for block
blk\_1439491087566691588\_6207java.net.SocketTimeoutException: 69000
millis timeout while waiting for channel to be ready for read. ch :
java.nio.channels.SocketChannel[connected
```
上面这个错误是由于没有在hdfs-site.xml中配置dfs.socket.timeout这个值，默认是180s,而每个datanode会再超时的1/2秒发送一次，导致默认的时候，会90秒发送一次，而某个地方设置的是69秒，因此超时。给上面的值设置69秒就不会出现这个异常了
