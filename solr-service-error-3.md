Title: solr服务器报错解决三
Date: 2014-02-18 19:22
Author: suhong
Category: 技术流
Tags: lucene, solr
Slug: solr-service-error-3

继上次报错后服务器很久都处于正常状态，今天查看solr管理发现又报错getNext()...Communications
link
failure,而且这次不止一个core报这样的错，直接登录服务器查看log4j打印的日志:

    1366030244 [http-bio-8080-exec-8770] ERROR org.apache.solr.handler.dataimport.JdbcDataSource – Ignoring Error when closing connection java.sql.SQLException: Streaming result set com.mysql.jdbc.RowDataDynamic@5a82a36f is still active. No statements may be issued when any streaming result sets are open and in use on a given connection. Ensure that you have called .close() on any active streaming result sets before attempting more queries.

还是未能正常关闭数据库连接所致。执行导入数据查看发现导入数据操作非常缓慢，查看服务器内存发现内存已被占满，solr服务器管理界面中内存的显示也很不乐观，正常不执行导入数据情况下solr能够再使用分配的内存还有1G多，可是现在能使用内存非常少，内存小会影响导入数据的速度，这也就出现了指定时间间隔内不能更新完指定core，多个core更新会更加消耗内存，最后导致mysql连接报错。solr服务器运行期间会将一些缓存，常用的数据等写到内存中，长时间运行下，一些缓存信息写入内存增加了内存开销，导入数据时也会消耗相应内存，而服务器内存又不怎\>么大，最终导致出现问题，这种情况下最好能够相应增大服务器内存，重启服务器后，这几天服务器一直都运行正常。
