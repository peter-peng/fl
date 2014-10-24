Title: Solr facet、group、fq
Date: 2014-03-02 17:53
Author: suhong
Category: 技术流
Tags: solr
Slug: solr-facet-group-fq

#### 一、Facet
facet是什么呢？你可以这样理解，当你在电商网站购物时，搜索笔记本，出现了很多品牌、厂商、价格区间等分组的信息，facet就可以根据这写文档的这些公共属性的维度进行分组统计。

solr主要有一下几种facet：
1. 普通facet，比如根据字段中厂商信息、品牌等分组统计 facet.field=品牌

2. 区间范围facet，比如根据价格区间等分组统计 可以使用facet.query=price:[\*
TO 500]这种方式，也可以使用facet.range facet.range=price
f.price.facet.range.start=0 //表示起始位置 f.price.facet.range.end=500
//表示结束位置 f.price.facet.range.gap=100 //表示间隔范围
range范围是左闭右开[start, end)，例如上面的例子就是分别对区间[0,
100)，[100, 200)，[200, 300)...的统计

3. 日期facet，对日期分组统计 facet.date=ptime
facet.date.start=2013-01-01T00:00:00Z facet.date.end=NOW
facet.date.gap=%2B1MONTH
gap设置的时候可以用+-/等符号，但是需要转义才能用。

4. multi-select facet 这是solr4的新特性，举例说明，如下面facet查询：
q=mainquery&fq=status:public&fq=doctype:pdf&facet=on&facet.field=doctype
查询中将doctype为pdf的文档过滤出来，也就是说查询结果都是doctype为pfd的文档，那么doctype为别的类型的文档统计数都是0.为了对doctype域实现multi-select
facet，客户端界面想要显示doctype的分组统计，使用如下：
q=mainquery&fq=status:public&fq={!tag=dt}doctype:pdf&facet=on&facet.field={!ex=dt}doctype.
这时分组统计信息中fq=doctype:pdf的信息没有被应用。

5. Pivot facet 这也是solr4的新特性，如： facet.pivot=popularity,grade
这是对popularity和grade进行二维分组统计。

#### 二：group solr
group也是一种分组统计的实现，类似于关系型数据库的group，和facet的区别是：facet只统计有多少个分组，每个分组是什么和有多少对应文档，却不知道具体的每个文档，若要查询每个文档，需要进一步查询；group是对该域的各个值进行分组统计，可以统计出该域有多少个不同的值，可以用于字段的去重，可以根据group.limit=n的值取每个group的对应前n条记录。

#### 三：fq
fq是过滤查询，例如上面使用facet知道了分组信息，要在某个分组中进行查找，就可以使用fq。
fq主要有两种实现方式： a、fq=popularity:[10 TO \*]& fq=section:0
b、fq=+popularity:[10 TO \*] +section:0
如果popularity和section的过滤是相互独立的，那么就用a，如果popularity和section的过滤是经常一起使用的，那么就使用b。
