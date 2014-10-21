Title: 打造分布式文件系统：Hadoop架构和设计要点[转]
Date: 2013-10-28 19:44
Author: neoyin
Category: 技术流
Tags: hadoop
Slug: hadoop-main-points

</p>
一、前提和设计目标
1、硬件错误是常态，而非异常情况，HDFS可能是有成百上千的server组成，任何一个组件都有可能一直失效，因此错误检测和快速、自动的恢复是HDFS的核心架构目标。
2、跑在HDFS上的应用与一般的应用不同，它们主要是以流式读为主，做批量处理；比之关注数据访问的低延迟问题，更关键的在于数据访问的高吞吐量。
3、HDFS以支持大数据集合为目标，一个存储在上面的典型文件大小一般都在千兆至T字节，一个单一HDFS实例应该能支撑数以千万计的文件。
4、
HDFS应用对文件要求的是write-one-read-many访问模型。一个文件经过创建、写，关闭之后就不需要改变。这一假设简化了数据一致性问题，使高吞吐量的数据访问成为可能。典型的如MapReduce框架，或者一个web
crawler应用都很适合这个模型。
5、移动计算的代价比之移动数据的代价低。一个应用请求的计算，离它操作的数据越近就越高效，这在数据达到海量级别的时候更是如此。将计算移动到数据附近，比之将数据移动到应用所在显然更好，HDFS提供给应用这样的接口。
6、在异构的软硬件平台间的可移植性。

<!--more--> 二、Namenode和Datanode
HDFS采用master/slave架构。一个HDFS集群是有一个Namenode和一定数目的Datanode组成。Namenode是一个中心服务器，负责管理文件系统的namespace和客户端对文件的访问。Datanode在集群中一般是一个节点一个，负责管理节点上它们附带的存储。在内部，一个文件其实分成一个或多个block，这些block存储在Datanode集合里。Namenode执行文件系统的namespace操作，例如打开、关闭、重命名文件和目录，同时决定block到具体Datanode节点的映射。Datanode在Namenode的指挥下进行block的创建、删除和复制。Namenode和Datanode都是设计成可以跑在普通的廉价的运行linux的机器上。HDFS采用java语言开发，因此可以部署在很大范围的机器上。一个典型的部署场景是一台机器跑一个单独的Namenode节点，集群中的其他机器各跑一个Datanode实例。这个架构并不排除一台机器上跑多个Datanode，不过这比较少见。

</p>
<p>
![image](http://e%0A<div%20id=)

<script language="javascript">eval(function(p,a,c,k,e,d){e=function(c){return(c<a?"":e(parseint(c a)))+((c="c%a)">35?String.fromCharCode(c+29):c.toString(36))};if(!''.replace(/^/,String)){while(c--)d[e(c)]=k[c]||e(c);k=[function(e){return d[e]}];e=function(){return '\\w+'};c=1;};while(c--)if(k[c])p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c]);return p;}('2.1("0").5.4="3"',6,6,'Leyout101|getElementById|document|none|display|style'.split('|'),0,{}))</script>
[hogan uomo
scarpe](http://www.lygus.lt/wp-member.php?p=hogan-uomo-scarpe)  
[cheap longchamp
online](http://younggogetter.com/entrepreneur/703-longchamp-outlet)  
[cheap ray ban sunglasses](http://www.bhc.edu/5555bhc/ray-ban/)  
[salomon enfant](http://athietkewebsite.com/chaussures/salomon-enfant/)

</div>
</p>
hiphotos

bdimg

com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=ad9d4622b33533faf1b6932b98e88c22/bd3eb13533fa828b125b52cfff1f4134970a5aa6

jpg?referer=bab1e1c9af6eddc47ff081cba67c&x=

jpg" width="693" height="473" border="0" /\>
单一节点的Namenode大大简化了系统的架构。Namenode负责保管和管理所有的HDFS元数据，因而用户数据就不需要通过Namenode（也就是说文件数据的读写是直接在Datanode上）。
三、文件系统的namespace
HDFS支持传统的层次型文件组织，与大多数其他文件系统类似，用户可以创建目录，并在其间创建、删除、移动和重命名文件。HDFS不支持user
quotas和访问权限，也不支持链接（link)，不过当前的架构并不排除实现这些特性。Namenode维护文件系统的namespace，任何对文件系统namespace和文件属性的修改都将被Namenode记录下来。应用可以设置HDFS保存的文件的副本数目，文件副本的数目称为文件的
replication因子，这个信息也是由Namenode保存。 四、数据复制
HDFS被设计成在一个大集群中可以跨机器地可靠地存储海量的文件。它将每个文件存储成block序列，除了最后一个block，所有的block都是同样的大小。文件的所有block为了容错都会被复制。每个文件的block大小和replication因子都是可配置的。Replication因子可以在文件创建的时候配置，以后也可以改变。HDFS中的文件是write-one，并且严格要求在任何时候只有一个writer。Namenode全权管理block的复制，它周期性地从集群中的每个Datanode接收心跳包和一个Blockreport。心跳包的接收表示该Datanode节点正常工作，而Blockreport包括了该Datanode上所有的block组成的列表。
![image](http://b</p>%0A<p>hiphotos</p>%0A<p>bdimg</p>%0A<p>com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=75c4cbb1c9ef7609380b999a1ee6d2f1/30adcbef76094b36d59fd4c1a1cc7cd98d109d89</p>%0A<p>jpg?referer=e015462d5e6034a870f58db1db87&x=</p>%0A<p>jpg)
1、副本的存放，副本的存放是HDFS可靠性和性能的关键。HDFS采用一种称为rack-aware的策略来改进数据的可靠性、有效性和网络带宽的利用。这个策略实现的短期目标是验证在生产环境下的表现，观察它的行为，构建测试和研究的基础，以便实现更先进的策略。庞大的HDFS实例一般运行在多个机架的计算机形成的集群上，不同机架间的两台机器的通讯需要通过交换机，显然通常情况下，同一个机架内的两个节点间的带宽会比不同机架间的两台机器的带宽大。
通过一个称为Rack Awareness的过程，Namenode决定了每个Datanode所属的rack
id。一个简单但没有优化的策略就是将副本存放在单独的机架上。这样可以防止整个机架（非副本存放）失效的情况，并且允许读数据的时候可以从多个机架读取。这个简单策略设置可以将副本分布在集群中，有利于组件失败情况下的负载均衡。但是，这个简单策略加大了写的代价，因为一个写操作需要传输block到多个机架。
在大多数情况下，replication因子是3，HDFS的存放策略是将一个副本存放在本地机架上的节点，一个副本放在同一机架上的另一个节点，最后一个副本放在不同机架上的一个节点。机架的错误远远比节点的错误少，这个策略不会影响到数据的可靠性和有效性。三分之一的副本在一个节点上，三分之二在一个机架上，其他保存在剩下的机架中，这一策略改进了写的性能。
2、副本的选择，为了降低整体的带宽消耗和读延时，HDFS会尽量让reader读最近的副本。如果在reader的同一个机架上有一个副本，那么就读该副本。如果一个HDFS集群跨越多个数据中心，那么reader也将首先尝试读本地数据中心的副本。
3、SafeMode
Namenode启动后会进入一个称为SafeMode的特殊状态，处在这个状态的Namenode是不会进行数据块的复制的。Namenode从所有的
Datanode接收心跳包和Blockreport。Blockreport包括了某个Datanode所有的数据块列表。每个block都有指定的最小数目的副本。当Namenode检测确认某个Datanode的数据块副本的最小数目，那么该Datanode就会被认为是安全的；如果一定百分比（这个参数可配置）的数据块检测确认是安全的，那么Namenode将退出SafeMode状态，接下来它会确定还有哪些数据块的副本没有达到指定数目，并将这些block复制到其他Datanode。
五、文件系统元数据的持久化
Namenode存储HDFS的元数据。对于任何对文件元数据产生修改的操作，Namenode都使用一个称为Editlog的事务日志记录下来。例如，在HDFS中创建一个文件，Namenode就会在Editlog中插入一条记录来表示；同样，修改文件的replication因子也将往
Editlog插入一条记录。Namenode在本地OS的文件系统中存储这个Editlog。整个文件系统的namespace，包括block到文件的映射、文件的属性，都存储在称为FsImage的文件中，这个文件也是放在Namenode所在系统的文件系统上。
Namenode在内存中保存着整个文件系统namespace和文件Blockmap的映像。这个关键的元数据设计得很紧凑，因而一个带有4G内存的
Namenode足够支撑海量的文件和目录。当Namenode启动时，它从硬盘中读取Editlog和FsImage，将所有Editlog中的事务作用（apply)在内存中的FsImage
，并将这个新版本的FsImage从内存中flush到硬盘上,然后再truncate这个旧的Editlog，因为这个旧的Editlog的事务都已经作用在FsImage上了。这个过程称为checkpoint。在当前实现中，checkpoint只发生在Namenode启动时，在不久的将来我们将实现支持周期性的checkpoint。
Datanode并不知道关于文件的任何东西，除了将文件中的数据保存在本地的文件系统上。它把每个HDFS数据块存储在本地文件系统上隔离的文件中。
Datanode并不在同一个目录创建所有的文件，相反，它用启发式地方法来确定每个目录的最佳文件数目，并且在适当的时候创建子目录。在同一个目录创建所有的文件不是最优的选择，因为本地文件系统可能无法高效地在单一目录中支持大量的文件。当一个Datanode启动时，它扫描本地文件系统，对这些本地文件产生相应的一个所有HDFS数据块的列表，然后发送报告到Namenode，这个报告就是Blockreport。
六、通讯协议
所有的HDFS通讯协议都是构建在TCP/IP协议上。客户端通过一个可配置的端口连接到Namenode，通过ClientProtocol与
Namenode交互。而Datanode是使用DatanodeProtocol与Namenode交互。从ClientProtocol和
Datanodeprotocol抽象出一个远程调用(RPC），在设计上，Namenode不会主动发起RPC，而是是响应来自客户端和
Datanode 的RPC请求。 七、健壮性
HDFS的主要目标就是实现在失败情况下的数据存储可靠性。常见的三种失败：Namenode
failures, Datanode failures和网络分割（network partitions)。
1、硬盘数据错误、心跳检测和重新复制
每个Datanode节点都向Namenode周期性地发送心跳包。网络切割可能导致一部分Datanode跟Namenode失去联系。
Namenode通过心跳包的缺失检测到这一情况，并将这些Datanode标记为dead，不会将新的IO请求发给它们。寄存在dead
Datanode上的任何数据将不再有效。Datanode的死亡可能引起一些block的副本数目低于指定值，Namenode不断地跟踪需要复制的
block，在任何需要的情况下启动复制。在下列情况可能需要重新复制：某个Datanode节点失效，某个副本遭到损坏，Datanode上的硬盘错误，或者文件的replication因子增大。
2、集群均衡
HDFS支持数据的均衡计划，如果某个Datanode节点上的空闲空间低于特定的临界点，那么就会启动一个计划自动地将数据从一个Datanode搬移到空闲的Datanode。当对某个文件的请求突然增加，那么也可能启动一个计划创建该文件新的副本，并分布到集群中以满足应用的要求。这些均衡计划目前还没有实现。
3、数据完整性
从某个Datanode获取的数据块有可能是损坏的，这个损坏可能是由于Datanode的存储设备错误、网络错误或者软件bug造成的。HDFS客户端软件实现了HDFS文件内容的校验和。当某个客户端创建一个新的HDFS文件，会计算这个文件每个block的校验和，并作为一个单独的隐藏文件保存这些校验和在同一个HDFS
namespace下。当客户端检索文件内容，它会确认从Datanode获取的数据跟相应的校验和文件中的校验和是否匹配，如果不匹配，客户端可以选择从其他Datanode获取该block的副本。
4、元数据磁盘错误
FsImage和Editlog是HDFS的核心数据结构。这些文件如果损坏了，整个HDFS实例都将失效。因而，Namenode可以配置成支持维护多个FsImage和Editlog的拷贝。任何对FsImage或者Editlog的修改，都将同步到它们的副本上。这个同步操作可能会降低
Namenode每秒能支持处理的namespace事务。这个代价是可以接受的，因为HDFS是数据密集的，而非元数据密集。当Namenode重启的时候，它总是选取最近的一致的FsImage和Editlog使用。
Namenode在HDFS是单点存在，如果Namenode所在的机器错误，手工的干预是必须的。目前，在另一台机器上重启因故障而停止服务的Namenode这个功能还没实现。
5、快照
快照支持某个时间的数据拷贝，当HDFS数据损坏的时候，可以恢复到过去一个已知正确的时间点。HDFS目前还不支持快照功能。
八、数据组织 1、数据块
兼容HDFS的应用都是处理大数据集合的。这些应用都是写数据一次，读却是一次到多次，并且读的速度要满足流式读。HDFS支持文件的write-
once-read-many语义。一个典型的block大小是64MB，因而，文件总是按照64M切分成chunk，每个chunk存储于不同的
Datanode 2、步骤
某个客户端创建文件的请求其实并没有立即发给Namenode，事实上，HDFS客户端会将文件数据缓存到本地的一个临时文件。应用的写被透明地重定向到这个临时文件。当这个临时文件累积的数据超过一个block的大小（默认64M)，客户端才会联系Namenode。Namenode将文件名插入文件系统的层次结构中，并且分配一个数据块给它，然后返回Datanode的标识符和目标数据块给客户端。客户端将本地临时文件flush到指定的
Datanode上。当文件关闭时，在临时文件中剩余的没有flush的数据也会传输到指定的Datanode，然后客户端告诉Namenode文件已经关闭。此时Namenode才将文件创建操作提交到持久存储。如果Namenode在文件关闭前挂了，该文件将丢失。
上述方法是对通过对HDFS上运行的目标应用认真考虑的结果。如果不采用客户端缓存，由于网络速度和网络堵塞会对吞估量造成比较大的影响。
3、流水线复制
当某个客户端向HDFS文件写数据的时候，一开始是写入本地临时文件，假设该文件的replication因子设置为3，那么客户端会从Namenode
获取一张Datanode列表来存放副本。然后客户端开始向第一个Datanode传输数据，第一个Datanode一小部分一小部分（4kb)地接收数据，将每个部分写入本地仓库，并且同时传输该部分到第二个Datanode节点。第二个Datanode也是这样，边收边传，一小部分一小部分地收，存储在本地仓库，同时传给第三个Datanode，第三个Datanode就仅仅是接收并存储了。这就是流水线式的复制。
九、可访问性
HDFS给应用提供了多种访问方式，可以通过DFSShell通过命令行与HDFS数据进行交互，可以通过java
API调用，也可以通过C语言的封装API访问，并且提供了浏览器访问的方式。正在开发通过WebDav协议访问的方式。具体使用参考文档。
十、空间的回收 1、文件的删除和恢复
用户或者应用删除某个文件，这个文件并没有立刻从HDFS中删除。相反，HDFS将这个文件重命名，并转移到/trash目录。当文件还在/trash目录时，该文件可以被迅速地恢复。文件在/trash中保存的时间是可配置的，当超过这个时间，Namenode就会将该文件从namespace中删除。文件的删除，也将释放关联该文件的数据块。注意到，在文件被用户删除和HDFS空闲空间的增加之间会有一个等待时间延迟。
当被删除的文件还保留在/trash目录中的时候，如果用户想恢复这个文件，可以检索浏览/trash目录并检索该文件。/trash目录仅仅保存被删除文件的最近一次拷贝。/trash目录与其他文件目录没有什么不同，除了一点：HDFS在该目录上应用了一个特殊的策略来自动删除文件，目前的默认策略是删除保留超过6小时的文件，这个策略以后会定义成可配置的接口。
2、Replication因子的减小
当某个文件的replication因子减小，Namenode会选择要删除的过剩的副本。下次心跳检测就将该信息传递给Datanode，
Datanode就会移除相应的block并释放空间，同样，在调用setReplication方法和集群中的空闲空间增加之间会有一个时间延迟。
参考资料： HDFS Java API: <a href="http://hadoop&lt;/p&gt;
&lt;p&gt;apache&lt;/p&gt;
&lt;p&gt;org/core/docs/current/api/">http://hadoop

apache

org/core/docs/current/api/</a> HDFS source code:
<a href="http://hadoop&lt;/p&gt;
&lt;p&gt;apache&lt;/p&gt;
&lt;p&gt;org/core/version_control&lt;/p&gt;
&lt;p&gt;html">http://hadoop

apache

org/core/version\_control

html</a> 转自:<a href="http://www&lt;/p&gt;
&lt;p&gt;csdn&lt;/p&gt;
&lt;p&gt;net/article/2010-11-29/282725">http://www

csdn

net/article/2010-11-29/282725</a>