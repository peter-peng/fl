Title: 35个实用Linux Find命令
Date: 2013-11-23 13:30
Author: neoyin
Category: 技术流
Tags: find, linux
Slug: linux-find-command

Linux Find 命令是Linux
系统中最常用也是最重要的命令之一,Find命令基于用户输入参数和条件匹配搜索本地目录和文件.可以根据类型,权限,用户,用户组,时间,大小或者可能的其它条件.
通过这篇文件我们分享每天Linux
命令:通过实用例子来使用Find命令的一些经验.这篇文件中我们会把这35个Find命令分成5个部分,从基本逐步推进介绍给大家.  
1. **Part I**: 通过文件name查找.  
2. **Part II**: 通过文件属性权限查找.  
3. **Part III**: 通过文件所用者和用户组查找.  
4. **Part IV**: 通过时间查找文件或文件夹.  
5. **Part V**: 通过大小查找文件或文件夹.

#### <!--more-->

#### 1. 在当前目录根据文件名查找

    #在当前目录查找文件名为tecmint.txt的文件或文件夹
    # find . -name tecmint.txt
    ./tecmint.txt

#### 2. 查找/home目录下文件或文件夹

    #查找/home目录下名为tecmint.txt 的文件
    # find /home -name tecmint.txt
    /home/tecmint.txt

#### 3.查找文件并忽略大小写

    #查找文件名为tecmint.txt的文件(包含大小写)
    # find /home -iname tecmint.txt
    ./tecmint.txt
    ./Tecmint.txt

#### 4. 根据名字查找目录

    #查找根目录下名为Tecmint的目录
    # find / -type d -name Tecmint
    /Tecmint

#### 5. 查找php文件

    #查找当前目录下名为tecmint.php的文件
     find . -type f -name tecmint.php
    ./tecmint.php

#### 6. 在目录下查找所有php文件

    # find . -type f -name "*.php"
    ./tecmint.php
    ./login.php
    ./index.php

#### 7. 查找权限为777的文件

    #查找目录下所有777的文件
    # find . -type f -perm 0777 -print

#### 8. 查找权限不为777的文件

    # find / -type f ! -perm 777

#### 9. Find SGID Files with 644 Permissions Find all the

**SGID bit** files whose permissions set to **644**.

    # find / -perm 2644

#### 10. Find Sticky Bit Files with 551 Permissions Find all the

**Sticky Bit** set files whose permission are **551**.

    # find / -perm 1551

#### 11. 查找 SUID 文件

    # find / -perm /u=s

#### 12. 查找 SGID 文件

    # find / -perm /g+s

#### 13. 查找只读文件

    # find / -perm /u=r

#### 14. 查找可执行文件

    # find / -perm /a=x

#### 15. 查找权限为777文件并修改为644

    # find / -type f -perm 0777 -print -exec chmod 644 {} \;

#### 16. 查找权限为777的目录并修改为755

    # find / -type d -perm 777 -print -exec chmod 755 {} \;

#### 17. 查找并删除一个文件

    # find . -type f -name "tecmint.txt" -exec rm -f {} \;

#### 18.查找并删除多个文件

    # find . -type f -name "*.txt" -exec rm -f {} \;
    OR
    # find . -type f -name "*.mp3" -exec rm -f {} \;

#### 19. 查找空文件

    # find /tmp -type f -empty

#### 20.查找空目录

    # find /tmp -type d -empty

#### 21. 查找隐藏文件

    # find /tmp -type f -name ".*" 

#### 22. 基于用户查找

    # find / -user root -name tecmint.txt

#### 23.

    # find /home -user tecmint

#### 24. 根据用户组查找

    # find /home -group developer

#### 25.根据用户特殊查找

    # find /home -user tecmint -iname "*.txt"

#### 26.查找过去50天修改过的文件

    # find / -mtime 50

#### 27. 查找过去50天访问过的文件

    # find / -atime 50

#### 28. 查找过去50-100天内修改过的文件

    #To find all the files which are modified more than 50 days back and less than 100 days.
    # find / -mtime +50 –mtime -100

#### 29.查找１个小时以前修改过的文件

    #To find all the files which are changed in last 1 hour. 
    #find / -cmin -60

#### 30.查找过去１个小时之内修改过的文件

    #To find all the files which are modified in last 1 hour. 
    #find / -mmin -60

#### 31. 查找１个小时以前修改过的文件

    #To find all the files which are accessed in last 1 hour
    find / -amin -60

#### 32. 查找大小为50M的文件

    # find / -size 50M

#### 33. 查找大小为 50MB – 100MB的文件

    # find / -size +50M -size -100M

#### 34. 查找大小为100Mb的文件并删除

    # find / -size +100M -exec rm -rf {} \;

#### 35. Find Specific Files and Delete

    # find / -type f -name *.mp3 -size +10M -exec rm {} \;

[http://www.tecmint.com/35-practical-examples-of-linux-find-command/](http://www.tecmint.com/35-practical-examples-of-linux-find-command/)
