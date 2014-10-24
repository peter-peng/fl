Title: solr服务器报错解决二
Date: 2014-02-18 19:21
Author: suhong
Category: 技术流
Tags: lucene, solr
Slug: solr-service-error-2

查看solr管理页面logging时发现报错：

    1366030227 [http-bio-8080-exec-8770] ERROR org.apache.solr.handler.dataimport.EntityProcessorBase – getNext() failed for query 'select ...(SQL查询语句)':org.apache.solr.handler.dataimport.DataImportHandlerException:com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure o-8080-exec-8770] ERROR org.apache.solr.handler.dataimport.DocBuilder – Exception while processing: account document : SolrInputDocument(fields: []):org.apache.solr.handler.dataimport.DataImportHandlerException:com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure The last packet successfully received from the server was 2 milliseconds ago. The last packet sent successfully to the server was 750,660 milliseconds ago.

主要就是Communications link
failure的原因，这是mysql连接出了问题，google下发现解释的最多的是DB的该连接已经断开而程序端还在使用该连接，将DB的连接等待超时时间增大即可，可我分析我这里的情况并不是需要改DB的wait\_timeout参数，因为DB的参数没有改动过，而我的solr服务器之前没有这样的报错，于是仔细查看服务器记录的日志发现还有如下报错：

    1366030244 [http-bio-8080-exec-8770] ERROR org.apache.solr.handler.dataimport.JdbcDataSource – Ignoring Error when closing connection java.sql.SQLException: Streaming result set com.mysql.jdbc.RowDataDynamic@5a82a36f is still active. No statements may be issued when any streaming result sets are open and in use on a given connection. Ensure that you have called .close() on any active streaming result sets before attempting more queries.

查看solr源码发现这是由于statements未能正常关闭引起,solr中的mysql连接未能正常关闭，分析发现原因是由于几个core全量更新时间间隔太小导致。在服务器端引入了定时操作，用于在夜间对数据进行全量更新，不久前将几个core更新时间改变了，使更新时间间隔变小，而引起报错的core全量更新所用的时间要大于这个时间间隔，也就是说该core更新还未完成另一个core的更新请求就发起了，使得该core未能正常更新成功，mysql连接也未断开。将core间的更新时间间隔改大，重新部署服务器后，几天下来不再报错，ok一切又正常了。
