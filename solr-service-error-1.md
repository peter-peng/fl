Title: solr服务器报错解决一
Date: 2014-02-18 19:19
Author: suhong
Category: 技术流
Tags: lucene, solr
Slug: solr-service-error-1

早上打开solr服务器管理界面查看运行正常，但是查看logging时，发现有报错信息：

    ERROR - 2014-01-21 01:40:01.566; org.apache.solr.common.SolrException; Full Import failed:java.lang.RuntimeException: java.lang.RuntimeException: org.apache.solr.handler.dataimport.DataImportHandlerException: Unable to execute query: select ...<定义的查询SQL语句> Processing Document # 1

在solr管理后台界面中看到的报错信息只有类似于以上的报错信息，只能看到是全量更新索引时报错，却不知道引起报错的根本原因，于是登录服务器查看其记录的日志信息，在日志中找到如下的一行关键报错信息：
Caused by: java.sql.SQLException: Error writing file '/tmp/MYsVYAUO'
(Errcode: 28)
MySQL的报错，这是由于执行SQL语句读取时会生成临时文件存放在/tmp目录下，而/tmp目录空间不足导致，增加/tmp目录大小或将一些占用空间的无用文件删除给足空间即可。
