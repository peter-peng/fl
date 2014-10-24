Title: solr Multicore实现单服务器索引分片搜索
Date: 2013-12-10 20:57
Author: suhong
Category: 技术流
Tags: solr
Slug: solr-multicore-single-machine-index-search

今天先记录下solr的Multicore实现多个分片索引的查询，之后再记录solr的分布式，使用solr版本1.4。  

solr的Multicore是一个很好的功能，其目地是一个solr实例实现多个搜索应用。我们现在用的就是一个solr服务器上使用Multicore实现多个搜索的应用，各个应用之间相互独立，互不干扰。关于Multicore的使用可以上wiki上查看或者查看solr包下example/multicore的实现，solr的wiki还是很给力的，功能详细讲解也详细。  

当solr中的索引过大时可以考虑将索引拆分成若干个索引，solr的分布式就是这样做的，当然它将索引分到了各个不同的服务器上，可我这里只有一个服务器啊，没关系，可以利用solr的Multicore来实现。我将索引建到了core0和core1上，想要访问core0将查询结果合并返回，http://localhost:8983/solr/core0/select？q=an，配置core0的solrconfig.xml，加入如下参数：

~~~~ {.html name="code"}
<requestHandler name="/shard" class="solr.SearchHandler" />          <!-- 1.这里用来建立一个shard路径的查询请求，你可以根据情况设置成你需要的-->
<requestHandler name="standard" class="solr.StandardRequestHandler" default="true">
    <lst name="defaults">
      <str name="shards.qt">/shard</str>               <!-- 2.这个参数是上面1中的配置 -->
      <str name="shards">localhost:8983/solr/core0,localhost:8983/solr/core1</str>   <!-- 3.这里指明shard存在core0和core1中，将会请求core0和core1 -->
    </lst>
</requestHandler>
~~~~

配置shards.qt的参数是为了查询请求时shards将请求转到访问core0/shard和core1/shard，所以也要配置注释1中name为shard的requestHandler。可能你会不明白了，solr中查询不是有默认的select查询路径吗，为什么还要配置注释2,如果不配置不是就访问core0/select和core1/select进行查询了吗？不错，确实是这样的，如果不配置注释2,默认将会请求core0/select和core1/select，但是core0/select又访问到了core0/select，这不是形成了死循环嘛，如果你有一个core3的文件夹，而在core3下的solrconfig.xml中配置了

~~~~ {.html name="code"}
<requestHandler name="standard" class="solr.StandardRequestHandler" default="true">
    <lst name="defaults">
      <str name="shards">localhost:8983/solr/core0,localhost:8983/solr/core1</str>
    </lst>
</requestHandler>
~~~~

使访问http://localhost:8983/solr/core3/select？q=an时查询core0和core1,ok这样不需要配置上面的注释2,访问core3/select时查询了core0/select和core1/select。所以访问core0查询时在core0下的solrconfig.xml中需要加入上面的注释2和注释1（一个requestHandler）,在core1中的solrconfig.xml中也配置上注释1.  
在这里还遇到了一个问题就是：

~~~~ {.html name="code"}
<requestHandler name="/shard" class="solr.SearchHandler">
    <lst name="defaults">
      <int name="rows">20</int>
      <str name="df">namePinyin</str>
      <str name="fl">name</str>
    </lst>
</requestHandler>
~~~~

我的shard之前配置为上面这段，结果查询时报错，调试源码才发现，原来在这个shard的查询中查询结果需要返回keyField字段，也就是你在schema.xml配置文件中配置的accountId的字段，而我这里设置fl返回了name字段，而没有返回accountId字段，所以报错，你可以返回accountId和name，或者全部返回。
