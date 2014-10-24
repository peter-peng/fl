Title: 你不知道的java nio缓冲区（一)
Date: 2014-01-01 13:47
Author: fuqiang
Category: 技术流
Tags: buffer, java, nio
Slug: java-nio-buffer-1


因为一直在研究apache mina,看到底层代码之后，发现我对java
nio了解甚少，于是又开始深入学习java nio,所以，先来说说java
nio的一些知识，然后再来攻克mina吧。 java
nio准备写三章，分别是：你不知道的java nio缓冲区，为什么是通道，高效选择器
关于java
nio的缓冲区，不得不理解的是四个属性，即capacity,limit,position,mark
1.capacity:缓冲区可容纳的最大数据量 2.position: 开始读取数据的地方
3.limit:：缓冲区第一个不能被读写的元素位置 4.
mark:将读取数据的底线设置为mark所在的前一个position
可以将position和limit理解为mysql语句里的limit skip,
limit，只不过limit是不能被读到的第一个位置而已，而mark可以理解为改变skip
四个属性的值域为：0<=mark<=position<=limit<=position
新建一个ByteBuffer，则其属性如下图：
![image](http://img.blog.csdn.net/20140101130150312?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlhbmdmdWxsbGw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
位置被设置为0，因为此时还没有任何数据，容量和limit都是最大值，mark是undefined.
<!--more--> 再来看看缓冲区的api，以下是Buffer类的

    package java.nio;

    public abstract class ByteBuffer extends Buffer implements Comparable{

    public abstract byte get();

    public abstract byte get(int index);

    public abstractByteBuffer put(byte b);

    public abstract ByteBuffer put(int index, byte b);

    }

    buffer.put((byte)'H').put((byte)'e').put((byte)'l').put((byte)'l').put((byte)'0');

五次调用填充方法之后，缓冲区如下图所示：

![image](http://img.blog.csdn.net/20140101130204796?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlhbmdmdWxsbGw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
好了，现在buffer中已经有数据了，那如果我们不想丢失位置的情况下进行一些更改，可以用带参数的put方法实现：
buffer.put(0,(byte)'M').put((byte)'w');
我们将H替换为M，然后在缓冲区的顶部再加一个w，更改后，缓冲区如下图所示：
![image](http://img.blog.csdn.net/20140101130229484?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlhbmdmdWxsbGw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
可以看到，使用put()方法，会将位置值加一，而使用put(int index, byte
b)则只会替换index下的值
接下来我们该怎么样读取这些数据呢，接下来就需要翻转缓冲区，将其position设置为0，然后就可以读取数据了。需要注意的是，读取缓冲区时，读取的数据是从position开始，到limit-1结束的，也就是说，当读取数据时，需要同时把limit设置为poisition，即buffer.limit(buffer.position()).position(0);这样就可以正确读取数据了。不过这样太麻烦，缓冲区已经提供了一个方法来完成这些事情，调用buffer.flip()方法，则会将limit设置为position,然后将position设置为0
调用flip()方法后，缓冲区的如下图所示：
![image](http://img.blog.csdn.net/20140101130241109?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlhbmdmdWxsbGw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
可以看到position和limit的位置已经发生改变，当然，buffer还有一个方法rewind()，这个方法和flip()相似，唯一不同的是，rewind()方法只将position设置为0，而不会将limit设置为position。
设置完成之后，在调用buffer.get()或者buffer.get(index)就可以读取数据了。看下面这个例子：

    import java.nio.CharBuffer;

    public class BufferFillData {

    private static int index = 0;
    private static String[] strings = {
    "A random string value",
    "The product of an infinite number of monkeys",
    "Hey hey we're the Monkees",
    "Opening act for the Monkees: jiangfuqiang",
    "Help me! Help me!"
    };

    /**
    * @param args
    */
    public static void main(String[] args) throws Exception{
    CharBuffer buffer = CharBuffer.allocate(100);
    while(fillBuffer(buffer)) {
    buffer.flip();
    drainBuffer(buffer);
    buffer.clear();
    }
    }

    private static void drainBuffer(CharBuffer buffer) {
    while(buffer.hasRemaining()) {
    System.out.print(buffer.get());
    }
    System.out.println();
    }
    private static boolean fillBuffer(CharBuffer buffer) {
    if(index >= strings.length) {
    return false;
    }
    String string = strings[index++];
    for(int i = 0; i < string.length(); i++) {
    buffer.put(string.charAt(i));
    }
    return true;
    }

    }
