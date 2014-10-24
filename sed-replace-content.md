Title: sed替换文件内容
Date: 2014-07-12 04:48
Author: chengz
Category: 每天进步一点点
Tags: linux,sed
Slug: sed-replace-content

### 把文件中的http替成ftp

    sed -i "s/http/ftp/g"  filename

### 不区分大小写

     sed -i "s/http/ftp/ig"  filename
