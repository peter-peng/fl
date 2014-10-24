Title: java nio为什么是通道(三):文件通道
Date: 2014-01-07 00:41
Author: fuqiang
Category: 技术流
Tags: java, nio
Slug: java-nio-channel-3


前面已经提过，FileChannel对象不能直接创建，而是在一个打开的file对象(RandomAccessFile,FileInputStream或者FileOutputStream)上调用getChannel()方法获取。需要注意的是，只要有可能，FileChannel都会尝试使用本地I/O服务。FileChannel对象是线程安全的，多个进程可以再同一个实例上并发调用方法而不会引起任何问题，不过并非所有的操作都是多线程的。影响通道位置或者影响文件大小的操作都是单线程的。
FileLock是FileChannel的内部类，主要用于同步更新文件内容，fileChannel.locak(start,limit,mode)方法是一个比较有用的方法，它可以让你决定锁定文件的那些区域，而剩下的区域是不被锁定的。但是要注意的是，它所代表的锁是一个与地层文件关联的，而不是与通道关联，因此如果在使用完一个锁后而不释放它的话，可能会导致冲突或者死锁。
FileChannel提供了一个map()方法，该方法可以在一个打开的文件盒一个特殊的类型的ByteBuffer之间建立一个虚拟内存映射。在FileChannel上调用map()方法会创建一个由磁盘文件支持的虚拟内存映射并在那块虚拟内存空间外部封装一个MappedByteBuffer对象。这个对象类似于一个机遇内存的缓冲区，只不过该对象的数据元素存储在磁盘上的一个文件中。调用get()方法会从磁盘文件中获取数据，此数据将反应该文件的当前内容，即时在映射建立之后文件已经被一个外部进程做了修改，通过文件映射看到的数据与常规方法读取文件看到的内容是完全一样的，当然，对映射的缓冲区实现一个put()方法会更新磁盘上的那个文件，并且该文件的修改对于其他处理该文件的进程或者程序都是可见的。
通过内存映射机制来访问一个文件会比使用常规方法读写高效的多，甚至比使用通道的效率都高，因为不需要做明确的系统调用，而且操作系统的虚拟内存可以自动缓存内存页，这些页是用系统内存来缓存的，索引不会消耗jvm内存堆。
对于MappedByteBuffer需要注意的是，映射缓冲区没有绑定到创建它的通道上，关闭相关联的FileChannel不会破坏映射，只有丢弃缓冲区对象不慎才会破坏该映射。

    package com.nio.channel;

    import java.io.FileInputStream;
    import java.io.FileOutputStream;
    import java.net.URLConnection;
    import java.nio.ByteBuffer;
    import java.nio.MappedByteBuffer;
    import java.nio.channels.FileChannel;
    import java.nio.channels.FileChannel.MapMode;

    public class MappedHttp {

    private static final String OUTPUT_FILE = "MappedHttp.out";
    private static final String LINE_SEP = "\r\n";
    private static final String SERVER_ID = "Server: Ronsoft Dummy Server";
    private static final String HTTP_HDR = "HTTP/1.0 200 OK" + LINE_SEP + SERVER_ID + LINE_SEP;
    private static final String HTTP_404_HDR = "HTTP/1.0 404 Not Found" + LINE_SEP+SERVER_ID+LINE_SEP;

    /**
    * @param args
    */
    public static void main(String[] args) throws Exception{
    // TODO Auto-generated method stub
    String file = "D:\\apache-tomcat-6.0.29\\logs\\weekly_error_.log.2013-01";
    ByteBuffer header = ByteBuffer.wrap(bytes(HTTP_HDR));
    ByteBuffer dynhdrs = ByteBuffer.allocate(128);
    ByteBuffer[] gather = {header ,dynhdrs, null};
    long contentLength = -1;
    String contentType = "unknown/unknown";
    try {
    FileInputStream fis = new FileInputStream(file);
    FileChannel fc = fis.getChannel();
    MappedByteBuffer filedata = fc.map(MapMode.READ_ONLY, 0, fc.size());
    gather[2] = filedata;
    contentLength = fc.size();
    contentType = URLConnection.guessContentTypeFromName(file);
    } catch(Exception e) {
    ByteBuffer buf = ByteBuffer.allocate(128);
    String msg = HTTP_404_HDR+e+LINE_SEP;
    buf.put(bytes(msg));
    buf.flip();
    gather[0] = ByteBuffer.wrap(bytes(HTTP_404_HDR));
    gather[2] = buf;
    contentLength = msg.length();
    contentType = "text/plain";
    }
    StringBuffer sb = new StringBuffer();
    sb.append("Content-Length: ").append(contentLength);
    sb.append(LINE_SEP);
    sb.append("Content-Type: ").append(contentType);
    sb.append(LINE_SEP).append(LINE_SEP);
    dynhdrs.put(bytes(sb.toString()));
    dynhdrs.flip();
    FileOutputStream fos = new FileOutputStream(OUTPUT_FILE);
    FileChannel out = fos.getChannel();
    while(out.write(gather) > 0) {

    }
    out.close();
    System.out.println("output written to " + OUTPUT_FILE);
    }

    private static byte[] bytes(String string) throws Exception{
    return string.getBytes("UTF-8");
    }

    }
