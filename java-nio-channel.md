Title: java nio为什么是通道(一)：通往nio的云梯
Date: 2014-01-01 21:00
Author: fuqiang
Category: 技术流
Tags: channel, java, nio
Slug: java-nio-channel


java nio的通道是一个全新的I/O实现，没有扩展或者继承其它的类或者包。
通过只能在字节缓冲区上操作，因此Channel是面向字节的接口，为什么要这样设计呢？主要是因为通道直接会和操作系统的I/O服务通信，而操作系统都是以字节的形式实现底层I/O接口的。
1.打开一个通道 在通道界主要用的有四个类：FileChannel,SocketChannel,
ServerSocketChannel,DatagramChannel
socket通道可以直接创建新socket通道通过工厂方法，但是FileChannel的对象创建，只能通过打开一个RandomAccessFile,FileInputStream,FileOutputStream对象上的getChannel()方法来获取。

</p>
    SocketChannel sc = SocketChannel.open();
    sc.connect(new InetSocketAddress(host,port));
    ServerSocketChannel ssc = ServerSocketChannel.open();
    ssc.socket().bind(new InetSocketAddress(host,port));
    DatagramChannel dc = DatagramChannel.open();

    RandomAccessFile raf = new RandomAccessFile(host,port);

    FileChannel fc = raf.getChannel();

为什么是RandomAccessFile类而不是其它的IO类呢？我认为，这是可能因为只有RandomAccessFile类可以处理只读文件，并且可以随意访问文件的任何位置

<!--more--> 2.通道的实现
有两个接口实现了通道，即ReadableByteChannel和WriteByteChannel类

    public interface ReadableByteChannel extends Channel {

    public int read(ByteBuffer dst) throws IOException;

    }

    public interface WritableByteChannel extends Channel {

    public int write(ByteBuffer src) throws IOException;

    }

    public interface ByteChannel extends ReadableByteChannel,WritableByteChannel {}

可以看到,ByteChannel实现了两个接口read和write,因此，ByteChannel接口是双向的，也就是说，实现ByteChannel接口的其它接口或者类也将是双向的，但是对于file来说，它的双向只是语义上的，实际的实现可能并不都是可读可写，比如，你使用一个FileInputStream获取了一个FileChannel，那么你如果调用write方法，则会抛出NonWritableChannelException异常。

    FileInputStream input = new FileInputStream(filename);

    FileChannel channel = input.getChannel();

    channel.write(buffer);  //这里将抛出异常

    package com.nio.channel;

    import java.io.IOException;
    import java.nio.ByteBuffer;
    import java.nio.channels.Channels;
    import java.nio.channels.ReadableByteChannel;
    import java.nio.channels.WritableByteChannel;

    public class ChannelCopy {

    /**
    * @param args
    */
    public static void main(String[] args) throws IOException{
    ReadableByteChannel source = Channels.newChannel(System.in);
    WritableByteChannel dest = Channels.newChannel(System.out);

    channelCopy(source, dest);
    source.close();

    dest.close();

    }

    private static void channelCopy(ReadableByteChannel src, WritableByteChannel dest) throws IOException{
    ByteBuffer buffer = ByteBuffer.allocateDirect(16*1024);
    while(src.read(buffer) != -1) {
    buffer.flip();
    dest.write(buffer);
    buffer.compact();
    }
    buffer.flip();
    while(buffer.hasRemaining()) {  //由于调用了compact方法，为防止还有剩下的数据，则再读取一次
    dest.write(buffer);
    }
    }

    private static void channelCopy2(ReadableByteChannel src, WritableByteChannel dest) throws IOException {
    ByteBuffer buffer = ByteBuffer.allocateDirect(16*1024);
    while(src.read(buffer) != -2) {
    buffer.flip();
    while(buffer.hasRemaining()) {
    dest.write(buffer);
    }
    buffer.clear();
    }
    }
    }

通道可以以阻塞或者非阻塞模式运行，非阻塞模式的通道永远不会让调用的线程休眠，请求的操作要么立即完成，要儿返回一个结果表明未进行任何操作。只有面向流的通道，如sockets何pipes才能使用非阻塞模式。可以看到上面的例子我使用了直接缓冲区，因为在非阻塞模式下，I/O是直接跟操作系统的I/O服务交互的，使用直接缓冲区性能会更好一些。
