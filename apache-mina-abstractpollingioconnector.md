Title: apache mina基础类分析(四): AbstractPollingIoConnector
Date: 2013-12-22 20:56
Author: fuqiang
Category: 技术流
Tags: apache, mina
Slug: apache-mina-abstractpollingioconnector

<div style="display: none;">
[aire jordan](http://www.primaveraresidences.com/jordan-pas-cher/)

</div>
</p>
    AbstractPollingIoConnector：
    /**
    * A base class for implementing client transport using a polling strategy. The
    * underlying sockets will be checked in an active loop and woke up when an
    * socket needed to be processed. This class handle the logic behind binding,
    * connecting and disposing the client sockets. A {@link Executor} will be used
    * for running client connection, and an {@link AbstractPollingIoProcessor} will
    * be used for processing connected client I/O operations like reading, writing
    * and closing.
    *
    * All the low level methods for binding, connecting, closing need to be
    * provided by the subclassing implementation.
    *
    * @see NioSocketConnector for a example of implementation
    */

这个类的英文如上，意思是：这是一个客户端使用轮询策略的一个基础实现类，底层的socket会在循环中被检查，当有socket需要被处理时，则会被唤醒。这个类处理connecting,disposing的客户端逻辑。当有客户端连接时，则会调用Executor，
并且会调用AbstractPollingIoProcessor，这个类用于处理客户端的读写关闭操作。
所有的低级别的方法，如binding,connecting,closing需要在子类中实现。
从这个类描述中不难看出，这个类是一个真正的socket(channel)的阻塞实现类，具体分析如下：

    protected final ConnectFuture connect0(SocketAddress remoteAddress, SocketAddress localAddress,
    IoSessionInitializer<? extends ConnectFuture> sessionInitializer) {
    H handle = null;
    boolean success = false;
    try {
    handle = newHandle(localAddress);
    if (connect(handle, remoteAddress)) {
    ConnectFuture future = new DefaultConnectFuture();
    T session = newSession(processor, handle);
    initSession(session, future, sessionInitializer);
    // Forward the remaining process to the IoProcessor.
    session.getProcessor().add(session);
    success = true;
    return future;
    }


    success = true;
    } catch (Exception e) {
    return DefaultConnectFuture.newFailedFuture(e);
    } finally {
    if (!success && handle != null) {
    try {
    close(handle);
    } catch (Exception e) {
    ExceptionMonitor.getInstance().exceptionCaught(e);
    }
    }
    }


    ConnectionRequest request = new ConnectionRequest(handle, sessionInitializer);
    connectQueue.add(request);
    startupWorker();
    wakeup();


    return request;
    }

在这个类中实现了AbstractIoConnector的抽象方法connect0,下面来拜访一下这个大神级方法吧。。。。。
在这个方法中主要，首先会初始化一个H，而这个H是什么呢？看看AbstractIoConnector的类定义public
final class NioSocketConnector extends
AbstractPollingIoConnector<NioSession, SocketChannel\> implements
SocketConnector，对，H就是SocketChannel,在泛型中定义的对象，调用方法newHandle(localAddress)，而这个方法是在其子类NioSocketConnector中实现：

    protected SocketChannel newHandle(SocketAddress localAddress) throws Exception {
    SocketChannel ch = SocketChannel.open();


    int receiveBufferSize = (getSessionConfig()).getReceiveBufferSize();
    if (receiveBufferSize > 65535) {
    ch.socket().setReceiveBufferSize(receiveBufferSize);
    }


    if (localAddress != null) {
    ch.socket().bind(localAddress);
    }
    ch.configureBlocking(false);
    return ch;
    }

不难看出，在这个方法中打开了一个监听器来监听客户端的连接，如果有连接达到，则取得定义的session缓冲大小，如果设置的缓冲区大小大于65535,则改变SocketChannel的默认接收缓冲区大小为新设置的缓冲区大小，
接着讲这个localAddress地址绑定到socket来接收消息，接着将socketChannel设置为非阻塞模式，这样可以提高构建的系统性能。
这个方法这行完成之后，又来调用connect方法，在connect方法中调用了socketChannel.connect(address)(检查socket连接以及初始化socket连接),如果返回true,则创建一个session,而newSession方法是由子类NioSocketConnector实现的，在newSession
方法中实现了一个NioSocketSession类，这个类从名称上可以想到，它是一个处理IO操作和socket
channel的处理类，在NioSocketSession的构造方法中又初始化了一个SessionConfigImpl类，这个类是一个session的基础配置信息类。
initSession方法中主要初始化一些session的配置参数
接着调用session的getProcessor().add(session)这个方法是这个socket通信的核心类，在add方法中就开始接收消息以及其它的一些socket的操作，但是processor类还没有说到，所以add方法就先不进去了，等分析到IoProcessor的时候再说
然后会初始化一个ConnectionRequest内部类，这个类实现了DefaultConnectionFuture，然后将request放入一个连接队列中，注意，从request开始的代码，是在上面的socket没有被连接时调用的，如果连接了，则不会执行下面的代码。
