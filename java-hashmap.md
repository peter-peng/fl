Title: java HashMap类描述的翻译
Date: 2013-11-22 01:17
Author: fuqiang
Category: 技术流
Slug: java-hashmap%e7%b1%bb%e6%8f%8f%e8%bf%b0%e7%9a%84%e7%bf%bb%e8%af%91

Hash
table是Map接口的基础实现类。这个类实现了接口定义的所有可选的map操作,并且这个类的键值都可以为null.(HashMap和Hashtable神似，但是HashMap不是同步的，并且键值可以为null).这个类不保证映射的顺序；尤其是不保证按常常量时间执行。

建设hash函数正确的散列了容器中的元素，这个类则提供常量时间性能的基础操作(get和put)。Iteration
over collection views requires time proportional to the "capacity" of
the HashMap instance (the number of buckets) plus its size (the number
of key-value
mappings)。因此，如果迭代性能很重要，则设置初始化容器的容量太高(或者载入因子太低)都是非常重要的。

HashMap的实例有两个参数会影响性能：初始容量和载入因子，容量是hash
table中容器的数量，并且初始化容量时hash
table同时被创建。载入因子是计算在它的容量自动增加以前hash
table允许有多满。当hash table中的容量超过载入因子乘积的容量时，hash
table会被重新进行hash(也就是说，内部数据结重建)，这也就会使改变后的hash
table容量接近于两倍没有改变以前的容器容量大小。

通常，默认的载入因子(0.75)在时间和空间花费上提供了一个折中，较高的值会较少空间但是提高了查询性能。当设置集合的初始容量时，见习考虑载入因子的大小。这样做是为了最小化重建hash的操作数量。如果初始容量超过了最大的实体数量，则载入因子会分割数量，并且不会重建hash值。

如果在一个HashMap实例中需要存储许多的mappings，那么设置一个适当大小的容量来初始化，这样做比使用默认的容量初始化更有效率，因为减少了重建数据结构的性能开销。

注意这个实现类不是同步的。如果多个线程同步访问一个hash
map，其中的至少一个线程修改了这个map的结构，那么它必须在这个实例以外被同步。(结构化的修改意味着添加或者修改一个或者多个mappings,通过一个key来修改对应的值不是结构化的改变)这是通常在一些对象上同步来封装到这个map中。

如果没有这样的对象存在，map会被Collections.synchronizedMap方法封装。为了防止未知的非同步访问这个map,在创建时封装这个map是最好的做法。

返回这个类的所有“集合视图方法”的迭代器会立刻给出错误：如果在某一时刻迭代器被创建之后被修改了结构，那么如果不是使用迭代器的remove方法删除元素，会抛出ConcurrentModificationException异常。因此，对于同步修改，迭代器会快速清楚的给出错误，而不是在将来某个时刻产生危险的，不确定性的行为。

注意迭代器的即时错误行为并不能确保总是有效，一般而言，不能保证存在非同步的同步修改。即时错误迭代器抛出ConcurrentModificationException基于一个服务模型原则。因此，依赖迭代器的同步来写程序时一种错误的方法。即时错误迭代器的特性应当只是用来查询潜在的bug.

终于翻译完了，翻译的很撇脚，但是觉得翻译完之后，又理解了一些HashMap的原理，觉得还不深入，下一次翻译HashTable的描述，然后对比HashMap写一个差异文章


