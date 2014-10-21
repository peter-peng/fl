Title: Apache mina 基础类分析（二）：AbstractIoService
Date: 2013-12-15 23:55
Author: fuqiang
Category: 技术流
Tags: mina
Slug: apache-mina-basic-abstractioservice

    AbstractIoService:
    /*Base implementation of IoService.
    An instance of IoService contains an Executor which will handle the incoming events.
    */

上面这段英文是这个抽象类的描述，大概意思是说它是IoService的一个实现类，IoService的实例包含了一个Executor来处理进入的事件。  
很显然，这个类管理一个事件线程池。这个类实现了对service的id的管理功能。  

在这个类中默认实现了一个IoServiceListener监听器，每创建一个IoService,都会通过这个默认实例来更新IO的最后读写时间以及吞吐量的计算时间。同时还会初始化一个默认的过滤器链构造器IoFilterChainBuilder、IoSessionDataStructureFactory/IoServicesStatistics实例。过滤器前面已经说过了，IoSessionDataStructureFactory是一个定义存储session数据结构的接口，实现这个类，可以自定义session的数据结构。IoServiceStatistics是一个关于session的所有信息的存储类.

AbstractIoService(IoSessionConfig sessionConfig, Executor
executor)：在这个方法中，首先初始化了一个IoServiceListener实例来管理IO事件，并且如果executor为空，则实例化一个缓存线程池。  
public final void dispose(boolean
awaitTermination):这个方法是final的，因此只能被调用，而不能被继承，在分析IoService时说过，这个方法的参数设置为true有可能会导致死锁，现在来看看，为什么会导致死锁：

    public final void dispose(boolean awaitTermination) {
    if (disposed) {
    return;
    }

    synchronized (disposalLock) {
    if (!disposing) {
    disposing = true;

    try {
    dispose0();
    } catch (Exception e) {
    ExceptionMonitor.getInstance().exceptionCaught(e);
    }
    }
    }

    if (createdExecutor) {
    ExecutorService e = (ExecutorService) executor;
    e.shutdownNow();
    if (awaitTermination) {

    //Thread.currentThread().setName();

    try {
    LOGGER.debug("awaitTermination on {} called by thread=[{}]", this, Thread.currentThread().getName());
    e.awaitTermination(Integer.MAX_VALUE, TimeUnit.SECONDS);
    LOGGER.debug("awaitTermination on {} finished", this);
    } catch (InterruptedException e1) {
    LOGGER.warn("awaitTermination on [{}] was interrupted", this);
    // Restore the interrupted status
    Thread.currentThread().interrupt();
    }
    }
    }
    disposed = true;
    }

从上面的方法中可以看出来，加锁是为了改变disposing的值，以及调用抽象方法dispose0(),而一切的罪魁祸首就是在dispose0造成的，但是这个dispose0方法并没有在这个类中实现，而是在AbstractPollingIoAcceptor类中实现，因此这里先不分析dispose0方法了，往下看，有e.awaitTermination方法，这个方法的意思是阻塞，直到所有的任务被完成，也就是说，我在前面已经修改了一些参数的值了，但是任然有一些任务在使用没有修改以前的数据，导致当其它任务请求旧的值时一直得不到，这样便一直阻塞造成死锁了。  
其它的方法不用再分析了，都是比较简单理解的
