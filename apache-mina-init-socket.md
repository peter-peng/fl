Title: mina的初始化过程分析进阶(二)
Date: 2013-12-08 22:13
Author: fuqiang
Category: 技术流
Tags: apache, java, mina
Slug: apache-mina-init-socket

AbstractIoService  

在初始化NioSocketAcceptor类时，会给父类AbstractPollingIoAccept的构造方法传递两个参数，即DefaultSocketSessionConfig类的实例和NioProcessor这个类对象，这个类是一个通信基类，它使用轮询来检查底层socket，并且唤醒被处理的socket。这个类在被绑定后，会操作接收和释放server
socket的逻辑，在这个类中会启动一个线程池来管理接收客户端的连接，并且使用AbstractPollingIoProcessor来处理客户端的读写和关闭操作。在AbstractPollingIoAcceptor这个抽象类中主要实现了一些底层并且核心的初始化工作，具体的业务实现需要继承它的抽象方法来实现。AbstractPollingIoAccept(IoSessionConfig
sessionConfig, Class<? EXTENDS IoProcessor<S\>\>
processorClass),从构造函数的参数可以看出，DefaultSocketSessionConfig类是IoSessionConfig的一个默认实现类，而传递一个IoProcessor的class，说明这个接口会有一些不同的实现类来处理数据，通过反射来或者这个接口的具体实现类的实现。但是在这个抽象类的构造方法中没有任何实现，而是调用了另一个它的构造方法AbstractPollingIoAcceptor(IoSessionConfig
sessionConfig, Executor executor, IoProcessor<S\> processor,boolean
createdProcessor)，在这个构造器中，多了一个Executor和createdProcessor参数，但是在前面的构造函数中，设置的这两个参数分别为null何false,然后在这个构造方法中又调用了它的父级构造方法AbstractIoAcceptor(IoSessionConfig
sessionConfig, Executor
executor)，然后又在这个构造方法中调用了AbstractIoAcceptor的父级构造方法AbstractIoService(IoSessionConfig
sessionConfig, Executor
executor)，要注意的一点是，sessionConfig参数已经在NioSocketAcceptor中给了一个默认的实现，而executor是一个null值。然后在这个AbstractIoService构造器中会初始化一些基础操作：  

1，初始化一个IoServiceListenerSupport监听器，这个监听器主要用来触发通信的事件(listeners
= new IoServiceListenerSupport(this))。  

2，然后这个监听器会添加一个IoServiceListener监听器，这个监听器主要用来监听关联到IoService的事件，这个监听器也实现了EventListener，也就是说，这个监听器实际上是一个事件监听器的实现类，管理事件的actived,idle,deactivated,created,destroyed的几个状态，然后托管到IoServiceListenerSupport中  
3.将默认的sessionConfig赋值给AbstractIoService的sessionConfig  
4，接着会初始化一个mina自定义的ExceptionMonitor的异常实例。  

5，然后会有一个if判断executor是否为空，如果为空，在会使用Executors.newCachedThreadPool()线程池初始化，然后将一个全局变量createdExecutor的值设置为true,这个变量在AbstractPollingAcceptor中被传递过，肯定有用。至于为什么要用缓存线程池，在AbstractIoService类中的ExecuteWorker方法中有代码executor.execute(new
NamePreservingRunnable(worker,
actualThreadName))，而NamePreservingRunnable是一个改变worker线程的名字的线程，这样做的目的，肯定是线程池中线程到的数量控制肯定在其它地方做了，因此在这里使用缓存线程池更好一些。  

到这里，AbstractIoService初始化完毕，然后开始初始化AbstractIoAcceptor类，这个类主要管理SocketAddress实例的增删改查操作，初始化的几个全局变量集合有：  
1.List<SocketAddress\> defaultLocalAddresses  
2.List<SocketAddress\> unmodifiableDefaultLocalAddresses  
3.Set<SocketAddress\> boundAddresses  
4.Object bindLock  

值得注意的是在操作这几个变量时，方法中都是同步或者加锁处理的，也就是说，这个抽象类的方法操作是线程安全的。  

AbstractIoAcceptor初始化完毕之后，就开始初始化AbstractPollingIoAcceptor抽象类，这个类的功能在上面已经解释过了，主要来看看具体有哪些变量或者方法：  

1，初始化一个Semaphore，它只初始化了一个信号量，是用来在创建selector以前阻塞通信的一个锁，这也是一个线程安全操作。  
2.然后会初始化下面的变量：

    private final Queue<AcceptorOperationFuture> registerQueue = new ConcurrentLinkedQueue<AcceptorOperationFuture>();

    private final Queue<AcceptorOperationFuture> cancelQueue = new ConcurrentLinkedQueue<AcceptorOperationFuture>();

    private final Map<SocketAddress, H> boundHandles = Collections.synchronizedMap(new HashMap<SocketAddress, H>());

    private final ServiceOperationFuture disposalFuture = new ServiceOperationFuture();

可以看出这个类是一个通信的核心实现类，主要用来阻塞通信，将一些等待返回值的线程放入队列中，AcceptorOperationFuture是个线程安全的阻塞类，在这个类中，默认等待的socket数量默认是50个。  
3.在构造器中调用了init()方法来初始化selector(selector =
Selector.open()),也就是说，在这里终于开始监听客户端的连接请求  

终于又回到了NioSocketAcceptor类了，在这里父级构造器初始化完毕之后，会再初始化sesionConfig的默认值。  

好了，终于NioSocketAcceptor类初始化完毕了，回顾上面的分析，主要是初始化了sessionConfig的默认值，注册事件监听器并且托管，管理socketAddress集合，初始化连接通道。
