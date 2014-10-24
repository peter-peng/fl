Title: solr TermsComponent实现对热词的统计
Date: 2013-12-29 23:22
Author: suhong
Category: 技术流
Tags: solr
Slug: solr-termscomponent-hot-word-statistics


通过solr获取热门标签，之前实现suggest功能时调试代码发现solr在建立索引时就将一些term的统计信息保存了下来，现在要实现热词的统计也就是要获得term的统计信息，google后发现其实就是要获取term
frequency然后排序，solr为我们提供了这样一个接口，只需要配置后就可以实现。但是由于之前查询资料的时候将TermsComponent和TermVectorComponent两个模块搞混，以至于使用TermVectorComponent实现走了很多弯路，分段获取TermVector的df，排序，获取top50写入文件，获取时先查看缓存，没有则读取文件获取，完成时又详细看了下TermsComponent，才发现原来通过TermsComponent可以直接获取，下面就简单记录下TermsComponent的实现：
在solrconfig.xml中配置：

~~~~ {.html}
<!-- A request handler for demonstrating the terms component -->
<requestHandler name="/terms" class="solr.SearchHandler" startup="lazy">
  <lst name="defaults">
    <bool name="terms">true</bool>
    <bool name="distrib">false</bool>
  </lst>
  <arr name="components">
    <str>terms</str>
  </arr>
</requestHandler>
~~~~

只需要配置上面内容，就可以实现term的简单访问：
http://localhost:8983/solr/tag\_core/terms?wt=xml&terms.fl=Tag
