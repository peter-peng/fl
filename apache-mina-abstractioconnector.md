Title:  apache mina 基础类分析(三):AbstractIoConnector
Date: 2013-12-22 20:55
Author: fuqiang
Category: 技术流
Slug: apache-mina-abstractioconnector

AbstractIoConnector:这个类继承了AbstractIoService，实现了IoConnector接口，AbstractIoService已经介绍过了，在这个类中，主要实现了一个connect()(以及重载)方法：

    public final ConnectFuture connect(SocketAddress remoteAddress, SocketAddress localAddress,
    IoSessionInitializer<? extends ConnectFuture> sessionInitializer) {
    if (isDisposing()) {
    throw new IllegalStateException("The connector has been disposed.");
    }

    if (remoteAddress == null) {
    throw new IllegalArgumentException("remoteAddress");
    }

    if (!getTransportMetadata().getAddressType().isAssignableFrom(remoteAddress.getClass())) {
    throw new IllegalArgumentException("remoteAddress type: " + remoteAddress.getClass() + " (expected: "
    + getTransportMetadata().getAddressType() + ")");
    }

    if (localAddress != null && !getTransportMetadata().getAddressType().isAssignableFrom(localAddress.getClass())) {
    throw new IllegalArgumentException("localAddress type: " + localAddress.getClass() + " (expected: "
    + getTransportMetadata().getAddressType() + ")");
    }

    if (getHandler() == null) {
    if (getSessionConfig().isUseReadOperation()) {
    setHandler(new IoHandler() {
    public void exceptionCaught(IoSession session, Throwable cause) throws Exception {
    // Empty handler
    }

    public void messageReceived(IoSession session, Object message) throws Exception {
    // Empty handler
    }

    public void messageSent(IoSession session, Object message) throws Exception {
    // Empty handler
    }

    public void sessionClosed(IoSession session) throws Exception {
    // Empty handler
    }

    public void sessionCreated(IoSession session) throws Exception {
    // Empty handler
    }

    public void sessionIdle(IoSession session, IdleStatus status) throws Exception {
    // Empty handler
    }

    public void sessionOpened(IoSession session) throws Exception {
    // Empty handler
    }
    });
    } else {
    throw new IllegalStateException("handler is not set.");
    }
    }

    return connect0(remoteAddress, localAddress, sessionInitializer);
    }

1.这个方法返回一个ConnectFuture对象，也就是说，调用这个方法后，会阻塞线程，在if(getHandler()
==
null)中，如果handler是空的，则默认实现一个空的handler,那也就不难理解，在开发时，在开发时，必须要自定义一个实现了IoHandler的类。  

这个类需要实现IoHandler的七个方法，再仔细看看这个接口中的几个方法，也就不难得出，这个IoHandler是一个顶层的阻塞类，等待接收消息。这个IoHandler接口跟android中的Handler接口意义上差不多。  

2.接着在这个类中调用了connect0方法，这个方法在这个类中是个抽象类，由其子类实现，下一篇将有这个类的分析

总结：这个类是一个实现接收消息阻塞功能的定义类。
