Title: mysql表字段存在关键字造成保存数据时异常的问题
Date: 2013-11-28 23:52
Author: fuqiang
Category: 技术流
Slug: mysql-keepword-save-error

遇到了一个这样的问题，修改好长时间，无果，遂google，翻山越岭之后，终于发现，原来是因为我的表中存在mysql的关键字造成的，一下是抛出的异常，而错误原因是因为我使用了desc关键字造成的。因此，以后建表的字段时，尽量不要使用单个单词创建，多用组合形式或者驼峰形式

[SNS] 2013-11-28
22:36:44[org.hibernate.util.JDBCExceptionReporter][ERROR]You have an
error in your SQL syntax; check the manual that corresponds to your
MySQL server version for the right syntax to use near 'desc, is\_locked,
creater, create\_time, id) values (1, 6, '11', '1111', 0, 2, '20' at
line 1  
[SNS] 2013-11-28
22:36:44[org.hibernate.event.def.AbstractFlushingEventListener][ERROR]Could
not synchronize database state with session  
org.hibernate.exception.SQLGrammarException: Could not execute JDBC
batch update
