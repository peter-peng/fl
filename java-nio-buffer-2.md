Title: 你不知道的java nio缓冲区(二)
Date: 2014-01-01 13:48
Author: fuqiang
Category: 技术流
Tags: buffer, java, nio
Slug: java-nio-buffer-2


上一篇介绍了一些缓冲区的基本属性，这一片来说说缓冲区的压缩

</p>
    public abstract class ByteBuffer extends Buffer implements Comparable {
    public abstract ByteBuffer compact();
    }

有时，我们可能指向从缓冲区中取出部分数据来，然后重新填充，compact方法会为你完成这些事情，它的作用就是将已经释放的数据丢掉，保留未释放的数据，并且将position的值设置为剩余数量的大小加1，将limit设置为compacity。

![image](http://img.blog.csdn.net/20140101133042234?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlhbmdmdWxsbGw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
<!--more-->
若开始的缓冲区如上图所示，则调用buffer.compact()方法之后，缓冲区将如下图所示：

![image](http://img.blog.csdn.net/20140101133244093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlhbmdmdWxsbGw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
缓冲区还有一个mark属性，这个属性是记住前一个position的位置。当调用缓冲区的reset方法时，如果设置了mark,则将mark=position,如果没有设置，则会抛出InvalidMarkException异常。
如果调用buffer.position(2).mark().position(4)，则缓冲区将如下图所示：
![image](http://img.blog.csdn.net/20140101133810453?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlhbmdmdWxsbGw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
如果读取这个缓冲区，则会读取到MW,然后位置会前进到6，接着如果再调用reset()方法，则会将位置设置为mark标记的位置，再读取缓冲区，则会返回owMW
