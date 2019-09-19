---
title: git 的各种常用操作
date: 2019-09-08 17:26:11
tags: git
---

接触了 GitHub 各种版本仓库，学会 git 的各种操作便显得尤为重要。首先自己上网查找 [git](https://git-scm.com) 的下载流程。当鼠标右键出现如下图箭头所示：
![](/images/gitOperate/rightClick.png)
则代表已经安装成功！

# git 查看版本操作

在每一次进行 `commit` 操作后，Git 会保存每一次提交的记录。使用 `git log` 进行查看。（若信息太多，按 q 键退出）
![](/images/gitOperate/gitLog.png)

如果嫌输出信息太多，看得眼花缭乱的，可以试试加上--pretty=oneline 参数。
![](/images/gitOperate/gitLogParameter.png)

# git 回退版本操作
使用 `git reset --hard HEAD^` 回退到上一个版本。上上一个版本就是 `HEAD^^`， n个版本为 `HEAD~n`。

# git 恢复版本

使用 `git reflog`各个版本的 `commit id`，（下图开头的数字即为 id）
![](/images/gitOperate/gitReflog.png)
然后使用 ` git reset --hard id ` ，回到指定的版本。

# git 撤销修改
使用 `git checkout -- file` 可将工作区的文件修改全部撤销，撤销到暂存区（若有）一样的状态，否则撤销到版本库一样的状态。

使用 `git reset HEAD file` 可将暂缓区的文件撤销，重新放回工作区。