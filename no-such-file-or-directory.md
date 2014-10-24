Title: no such file or directory
Date: 2013-12-28 17:25
Author: chengz
Category: 每天进步一点点
Slug: no-such-file-or-directory


有一些32位的程序执行时, 会报错 no such file or directory,
是因为系统缺少一些32位 相关的包, 安装下面这个库可以解决问题 sudo apt-get
install ia32-libs
