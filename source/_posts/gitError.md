---
title: 记录一下常见到的 git 错误
date: 2019-09-09 17:26:11
tags: git
---
# 1. 由于本地先进行修改，但是需要拉去远程分支时，就会出现一下错误

    error: Your local changes to the following files would be overwritten by merge

解决办法： git 三连


    Git stash     //暂存当前正在进行的工作。
    Git pull
    Git stash pop //合并暂存的代码  相当于 git stash apply & git stash drop