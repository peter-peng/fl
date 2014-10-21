Title: solr shard查询请求时createMainQuery报错
Date: 2013-12-24 20:35
Author: suhong
Category: 每天进步一点点
Slug: solr-shard-error-of-query-createmainquery

昨天又对一个solrcore进行分片操作，结果查询请求时报错：

    [http-8983-2] ERROR org.apache.solr.core.SolrCore – java.lang.NullPointerException
    at org.apache.solr.handler.component.QueryComponent.createMainQuery(QueryComponent.java:759)
    at org.apache.solr.handler.component.QueryComponent.regularDistributedProcess(QueryComponent.java:612)
    at org.apache.solr.handler.component.QueryComponent.distributedProcess(QueryComponent.java:565)
    at org.apache.solr.handler.component.SearchHandler.handleRequestBody(SearchHandler.java:244)
    at org.apache.solr.handler.RequestHandlerBase.handleRequest(RequestHandlerBase.java:135)
    at org.apache.solr.core.SolrCore.execute(SolrCore.java:1859)

google下报错原因，才发现该core中的schema.xml中没有配置uniqueKey，增加uniqueKey后查询正常。
