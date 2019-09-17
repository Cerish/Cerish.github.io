---
title: 多台电脑提交和更新github pages博客
date: 2019-09-09 17:26:11
tags: hexo git
---

# 1. 前言
在上一篇文章 {% post_link hexo-cli %} 已经提到如何创建与更新博客。虽然hexo部署blog到github确实很方便，但不能满足不同时间、不同地点、不同电脑上更新文章的需求。跟随下面几个步骤，可以让你随心所欲写文章。

# 2. 具体步骤

## 2.1 准备工作

首先确保自己已经使用hexo在github搭建好了自己的个人博客，github仓库中如下图显示：
![](/images/hexoBranch/masterBranch.png)

## 2.2 建立 hexo 仓库
在 Github 的 `username.github.io` 仓库上基于 master 建立一个 hexo 分支，并在该仓库->Settings->Branches->Default branch中将默认分支设为 hexo，并将该仓库克隆到本地。

## 2.3 将本地博客的部署文件拷贝进username.github.io文件目录

如题，可删除克隆下来的仓库文件，将本地博客的部署文件拷贝进username.github.io文件目录中去。接下来，将该目录下的文件提交到 hexo 分支，但要注意：

    · 将themes目录以内中的主题的.git目录删除（如果有），因为一个git仓库中不能包含另一个git仓库，提交主题文件夹会失败。
    · 移除 .git方法：
        1. 删除隐藏文件 git
        2.git rm --cache themes/name 从暂存区删除该文件夹
        3. 三步走: -->git add .  -->git commit -m "commit message" -->git push
    · 删除了themes目录中的.git就不能git pull更新主题，如果需要更新主题，则在另一个地方git clone下来该主题的最新版本，然后将内容拷到当前主题目录即可

完成以上操作，hexo分支的文件就可以随时随地修改了。

## 2.4 新电脑操作
至此，你的博客已经可以在其他电脑上进行同步的维护和更新了，方法很简单：

    · 将新电脑的生成的ssh key添加到GitHub账户上
    · 在新电脑上克隆username.github.io仓库的xxx分支到本地，此时本地git仓库处于xxx分支
    · 切换到username.github.io目录，执行npm install(由于仓库有一个.gitignore文件，里面默认是忽略掉 node_modules文件夹的，也就是说仓库的hexo分支并没有存储该目录[也不需要]，所以需要install下)

至此，所以的工作已经完成就绪，其余的只需按照博客的发布与GitHub 拉取操作一直即可。