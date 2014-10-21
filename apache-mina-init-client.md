Title: mina的server/client初始化过程分析(一)
Date: 2013-12-08 00:40
Author: fuqiang
Category: 技术流
Tags: apache, mina
Slug: apache-mina-init-client

这几天决定研究一下mina，也是我决定研究apache下面的第一个项目，我会持续更新的。

研究一个框架时，有一个牛人给我说过，先找到几个主要的接口，然后先了解这几个接口提供的方法大体上都用在什么地方，然后通过写测试程序走一遍，也就基本上可以掌握其中的原理了，于是我按照他说的开始研究mina了....

先来介绍一下mina的几个暴露的主要接口的作用：

一，IoAcceptor:该接口的描述如下：Accepts incoming connection, communicates
with clients, and fires events to {@link
IoHandler}s.意思是说接收请求的连接，并且直接跟客户端通信，还有就是激活IoHandler事件。可以看出，该接口主要用来接收连接，激活事件句柄。

二，NioSocketAcceptor:该类是一个IoAccept的直接实现类，这个类是操作tcp/ip的一个出入口。

三，DefaultIoFilterChainBuilder：该类是一个默认的过滤器链的构造器，可以把它理解为一个黑盒子，而在这个黑盒子里面处理着各种让人蛋疼的全局的事情。

四，ProtocolCodecFilter：这个是一个将二进制或者协议数据转换为一个消息对象的类。

五，ProtocolCodecFactory：这个接口提供对ProtocolCodecFilter的编码和解码功能。

六，IoHandlerAdapter：这个类实现了IoHandler接口，这个适配器实现的接口方法默认情况下不做任何操作。实现这个类后，其中的方法是可选的。这个类是一个事件对象的顶级类，所有的事件是通过它来驱动的

七，IoConnector：这个接口作为与服务端的连接器。这个接口和IoAcceptor接口是相对的，它俩是业务间通信连接的桥梁，但是不对业务做任何操作。

八，NioSocketConnector：这个类时IoConnector接口的实现类

九，IoSession：这个类完成了client/server之间的通信，这是一个核心类。

好了，上面的主要的类和接口介绍完成了，现在先类分析服务端的启动过程：

    org.apache.mina.transport.socket.nio.NioSocketAcceptor
    org.apache.mina.core.filterchain.IoFilterChainBuilder
    org.apache.mina.core.service.IoServiceListener
    org.apache.mina.core.session.IoSessionDataStructureFactory
    java.util.concurrent.Executor
    org.apache.mina.core.service.IoProcessor
    java.net.SocketAddress
    org.apache.mina.core.session.IoSessionConfig
    org.apache.mina.transport.socket.nio.NioSession
    org.apache.mina.transport.socket.nio.NioSocketSession
    org.apache.mina.core.service.AbstractIoService

上面的类是在初始化NioSocketAcceptor时类加载器加载的，从这个顺序可以看出，类加载器首先加载NioSocketAcceptor类，完了之后，再加载IoFilterChainBuilder类，可以想象，在初始化这个acceptor时，需要有个过滤器类去初始化IoFilterChainBuilder，然后又载入IoServiceListener，这个接口继承了一个事件监听器，于是不难理解，在初始化acceptor后还需要提供一个关于事件的启动器来启动事件；再看看Executor接口，这个接口大家都很熟悉了，那也不难理解了，它在底层就开始启动线程池准备接收通信线程了；到了IoProcessor类了，这个接口是对IO操作的实际接口，它继承了IoSession，也就是说，它初始化了一个IoSession，这下可以猜测，是不是在线程池中存在共享的IoSession或者每个线程都会拥有一个IoSession呢？不要忘了，它是在Executor后面紧接着启动的；好了，终于到了mina的心脏了，SocketAddress和InetSocketAddress类，大家也都熟悉了，初始化socket时必须要用到的类，在IoProcessor的实现类初始化后，就开始构造ServerSocket，开启socket的监听；嗯，IoSessionConfig，当然是IoSession的配置接口了；NioSession和NioSocketSession是对io的操作类，哎呀，终于出来了，AbstractIoService类是IoService接口的实现类，这个抽象类是管理IoServiceListener监听器的类。好了，acceptor初始化完毕了，当然现在就要给这个类添加过滤器和句柄以及iosession的参数设置了。接下来的一些参数设置就不再赘述了，看看api的介绍就明白了。

接着再来看看客户端的初始化过程是个神马了不起的事情吧...

    org.apache.mina.transport.socket.nio.NioSocketConnector
    org.apache.mina.core.filterchain.IoFilterChainBuilder
    org.apache.mina.core.service.IoServiceListener
    org.apache.mina.core.session.IoSessionDataStructureFactory
    java.util.concurrent.Executor
    org.apache.mina.core.future.IoFutureListener
    org.apache.mina.core.session.IoSession
    org.apache.mina.core.future.IoFuture
    org.apache.mina.core.future.ConnectFuture
    org.apache.mina.core.service.IoProcessor
    org.apache.mina.core.session.AbstractIoSession
    org.apache.mina.transport.socket.nio.NioSession
    org.apache.mina.transport.socket.nio.NioSocketSession
    org.apache.mina.core.session.IoSessionConfig
    java.net.SocketAddress
    org.apache.mina.core.service.AbstractIoService

可以对比一下，看来server和client端初始化的过程还是有很大的差别的。最主要的区别有两个，客户端载入了关于future的接口和类，还有socket的初始化置后了。IoFutureListener接口继承了事件监听器，也就是说，它跟事件是有关系的，它的作用是在事件异步完成之后，获取返回值。ConnectFuture是一个连接类，它会挂起当前线程知道获取到请求(读/写)。至于为什么socket的初始化顺序不一样，我想在下一篇文章介绍初始化的类中发生了什么事情的时候再详述。

好了，本人刚刚研究框架，有很多研究的方式或者研究的问题不对，希望大家多多指出错误。我也将很快更新。


