Title: apache mina系列 client初始化分析(三)
Date: 2013-12-15 00:37
Author: fuqiang
Category: 技术流
Slug: apache-mina-basic-client-init

距离上一次更新mina好长时间了，今晚把mina的client初始化补上了，快来一睹为快吧.....

先调用NioSocketConnector类的构造方法，然后在这个构造方法中又初始化父类AbstractPollingIoConnector的构造方法AbstractPollingIoConnector(IoSessionConfig
sessionConfig, Executor executor, IoProcessor
processor)构造方法，这个构造方法中的sessionConfig和processor是在NioSocketConnector的构造方法中实现的，即super(new
DefaultSocketSessionConfig(),NioProcessor.class)，executor是一个空对象；

接着回在上面的构造方法中调用父级的构造方法AbstractIoConnector(IoSessionConfig
sessionConfig, Executor
executor)，然后再在这个构造方法中调用AbstractIoService(IoSessionConfig
sessionConfig, Executor
executor)，在这个构造方法中主要用来初始化一些基础的服务类：  

1.初始化一个IoServiceListenerSupport监听器类，这个监听器的作用是提供service的各种状态，然后这个会给这个监听器添加一个关联这个service的活动监听器  
2.接着会将默认的session配置赋值给一个全局变量  
3.判断executor是否为空，如果为空，则会初始化一个缓存线程池  

初始化AbstractIoService的构造方法中的各种参数之后，又回到AbstractPollingIoConnector类中，然后调用init()方法，这个init方法是一个抽象方法，是交个其子类来实现，即NioSocketConnector实现，好了，终于回到初始类了；  

在NioSocketConnector中，会调用方法getSessionConfig()方法来初始化DefaultSocketSessionConfig的一些参数。上面提到调用init方法，在这个类中，init的方法实现如下：  
/\*\*  
\* {@inheritDoc}  
\*/  
@Override  
protected void init() throws Exception {  
this.selector = Selector.open();  
}  

从上面可以看出，很显然这是一个开启连接的地方，也就是说，在调用了NioSocketConnector后就已经开启了一个socket(channel)连接。  

NioSocketConnector初始化完毕之后，剩下的事情就跟服务器端的基本上一样了。但是在client端有这样一段代码：  
IoSession session = null;  
try {  
ConnectFuture future = connector.connect(new InetSocketAddress(HOST,
PORT));  
future.awaitUninterruptibly(); //等待链接创建完成  
session = future.getSession();  
session.write("网络时空 爱你 mina");  
} catch(Exception e) {  
logger.error("客户端连接异常");  
}

这段代码是调用已经建立连接的socket(channel)，然后通过一个ConnectFuture来获的一个session，而这个session正是处理网络数据读取的核心类。

不好意思，本人看了也觉得不怎么好，不过这个过程必须经历啊，望大家多提建议，我会继续完善的。接下来我有以下计划：1.画出server和client的初始化活动图；2.从server到client，分析每个基础类的具体作用。我觉得这样一步步来才会越练越能写出优秀的文章。望大家海涵！
