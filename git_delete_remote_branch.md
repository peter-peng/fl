Title: git删除远程分支abc
Date: 2014-06-14 10:12
Author: chengz
Category: 每天进步一点点
Tags: git
Slug: git_delete_remote_branch

删除远程分支abc

    git push origin :abc
    git push origin --delete abc

把一个文件换回最新commit

    git checkout filename 
    git checkout -- filename    # 如果刚好有个分支与文件同名, 用这个命令

回退一次提交 (revert 只回退指定提交的修改, 如果有三次需要回退要回退三次,
不能直接回退前面第三次)

    git revert 78dyh6e
