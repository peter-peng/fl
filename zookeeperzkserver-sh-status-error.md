Title: zookeeper使用命令zkServer.sh status时出现错误提示问题
Date: 2013-12-01 19:52
Author: fuqiang
Category: 技术流
Slug: zookeeperzkserver-sh-status-error

[![快照9](http://www.floatinglife.cn/wp-content/uploads/2013/12/快照9.png)](http://www.floatinglife.cn/wp-content/uploads/2013/12/快照9.png)

如上图所示，我在启动zookeeper后，进程已经存在了，但是查看状态时，却报这个错误，后来经过查资料，说是要在zkServer.sh的一段代码中加-q
1(是1不是英文l)，可是我的这个文件中已经存在了，如下图所示

[![快照8](http://www.floatinglife.cn/wp-content/uploads/2013/12/快照8.png)](http://www.floatinglife.cn/wp-content/uploads/2013/12/快照8.png)

于是，我把-q
1去掉后尝试，居然成功了，但是不知道是什么原因，我用的是3.4.2版本

<!--more-->

[![快照13](http://www.floatinglife.cn/wp-content/uploads/2013/12/快照13.png)](http://www.floatinglife.cn/wp-content/uploads/2013/12/快照13.png)
