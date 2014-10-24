Title: apache mina 基础类研究(一):IoService方法详解
Date: 2013-12-15 23:16
Author: fuqiang
Category: 技术流
Slug: apache-mina-basic-ioService

IoService:  
/\*Base interface for all IoAcceptor and IoConnector that provide I/O
service and manage IoSession.  
\*/  

上面的英文是这个接口的描述，大概意思是：这是个一IoAcceptor和IoConnector的基础接口，它提供了一个I/O服务，并且管理着每一个连接的IoSession.很显然，实现这个接口的类肯定就是操作I/O以及管理网络连接的功能。  
TransportMetaData
getTransportMetadata();这个方法返回一个运行的service的元数据，TransportMetaData主要提供service的一些基础配置的信息，比如协议名称，提供者名称，SocketAddress的类型等。  
void addListener(IoServiceListener
listener);这个方法用来添加一个service的监听器，用来监听跟这个service关联的所有事件。IoServiceListener接口继承了EventListener接口，主要提供了五个方法，分别是：  

1.serviceActivated:当IoService激活一个新的service时会被调用，也就是说，如果有新的来接，则会调用此方法  

2.serviceIdle:service空闲时调用(我猜可能是用来唤起其它任务线程的，等后面研究到具体实现了看看是不是这样的)  

3.serviceDeativated:service无效时调用此方法，这应该是一个连接关闭时调用的  

4.sessionCreated:当IoService创建一个新session时调用此方法(我猜应该是在创建session之后，会记录这个session的一些信息放入内存，比如超时时间，目的地等，研究到具体实现后再看是否是这样)  

5.sessionDestroyed:IoService销毁一个session后被调用，在这个方法中会执行一些清理session缓存的任务  

所以从这五个方法不难看出，IoServiceListener是用来监听session的生命周期的一个接口  
removeListener(IoServiceListener listener)很显然这是移除监听器  
boolean
isDisposing():如果调用了dispose()方法，则返回true,注意，当所有的的被关联的资源都被释放之后这个方法将返回true  
boolean
isDisposed():如果当前的processor的所有资源被释放，则返回true,这个方法的意思应该是说当连接被断开后则返回true  
void
dispose():释放被service分配的一些资源。请注意，调用这个方法时，也许会阻塞一些被这个service管理的session  
void
dispose(awaitTermination):这个方法和上面的方法基本上一样，但是如果传递的参数为true,则有可能会导致思索，因为设置为true时，会阻塞session，直到底层的ExecutorService被终止。（至于为什么会造成死锁，等到研究到具体实现的时候再说吧）  
IoHandler
getHandler():返回句柄，这个句柄会处理所有被这个service管理的连接  
Map<Long,IoSession\>
getManagedSessions():返回当前被service管理的所有session集合，map的key是IoSession的id  
IoFilterChainBuilder
getFilterChainBuilder():这个接口会构建所有IoSession的IoFilterChain，它会被service创建，默认值是空的
