Title: git 比较两个版本号的差异
Date: 2014-06-22 09:18
Author: chengz
Category: 每天进步一点点
Tags: git
Slug: git-diff-two-version

比较两个版本号的差异
--------------------

    # 前面是旧版本,后面是新版本
    git diff f58c2b52a4b..ad1585dbd85

    # 比较两个分支
    git diff master..test

比较结果

    diff --git a/a b/a
    index 924d01b..a378768 100644
    --- a/a
    +++ b/a
    @@ -2,4 +2,5 @@ package com.zhch.example.jsoup;

     public class Info {
         public static String url = "https://www.itouzi.com";
    +    public int age = 3;
     }

带加号的 + public int age = 3; 表示多了一行.  
上面这条命令只显示两个分支间的差异，如果你想找出‘master’,‘test’的共有
父分支和'test'分支之间的差异，你用3个‘.'来取代前面的两个'.' 。

    git diff master...test

### 查看当前的工作目录与另外一个分支的差别，你可以用下面的命令执行:

    git diff test

### 如果两个版本有很多文件不同, 只查看一个文件b

    git diff f58c2b52a4..ca6ae63074 -- b

### 统计一下有哪些文件被改动，可以使用‘--stat' 参数

    git diff f58c2b52a4..ca6ae63074 --stat

参考:  
[比较提交 - Git Diff](http://gitbook.liuhui998.com/3_5.html)
