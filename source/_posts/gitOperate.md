---
title: git 的各种常用操作
date: 2019-09-08 17:26:11
tags: git
---

接触了 GitHub 各种版本仓库，学会 git 的各种操作便显得尤为重要。首先自己上网查找 [git](https://git-scm.com) 的下载流程。当鼠标右键出现如下图箭头所示：
![](/images/gitOperate/rightClick.png)
则代表已经安装成功！

# 设置 ssh key
现在很多情况下，都是使用 ssh链接 下拉项目，所以需要设置ssh key 让 github 识别这是你的账号。

首先使用查看 `$ cd ~/.ssh` 查看用户主目录下是否有 .ssh 文件夹，如果有，查看有没有 `id_rsa`和`id_rsa.pub`这两个文件。如果没有，则执行下面的语句生成ssh key。

    $ ssh-keygen -t rsa -C "youremail"

生成之后，复制 `id_rsa.pub` 文件的内容（公钥，windows下 执行 clip < ~/.ssh/id_rsa.pub ，可将公钥复制至剪切板），然后登陆github 账号，进入 `setting --> ssh key`，然后复制公钥。
![](/images/gitOperate/sshKey.png)
![](/images/gitOperate/setSSHkey.png)

到这里已经设置好了ssh key，但是需要注意，如果 `.ssh`文件夹中没有 `known_hosts`文件的话， 在git push提交的时候可能会出现以下的问题

    Are you sure you want to continue connecting (yes/no)? //输入yes，回车

然后.ssh 文件夹下就会自动生成 known_hosts 文件了。


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

# git 关联远程分支
 选择你要连接的远程仓库，找到 Clone or download， 可直接复制项目地址

    $ git remote add origin git@github.com:huajielee/gitHandle.git

第一次推送至远程分支，`git push -u origin master` 加上 -u 参数

# 创建与删除分支
创建分支：

    $ git checkout -b dev 创建并切换分支
    相当于
    $ git branch dev
    $ git checkout dev

合并分支

    $ git merge dev 将dev 的工作成功合并到当前分支

删除分支

    $ git branch -D dev

# 存储分支

当在某个分支开发，做了修改还没commit的，需要切换到其他分支开发的时候，可以使用 `git stash` 将修改的文件进行暂时‘存储’，通过 `git stash list`查看存储列表。

当返回该分支继续工作时，有以下两种方法：

    $ git stash apply 恢复文件
    $ git stash drop 删除stash list列表的记录

或者

    $ git stash pop 恢复并删除stash list 列表记录

也可通过 `$ git stash apply stash@{n}` 找回对应id的文件。

# git cherry-pick

    `$ git cherry-pick commit-id` 可将该commit的消息拉取到该分支

# 远程仓库改名

这种情况适用于本地和远程的代码没得任何问题，就是远程仓库改了个名称，直接在本地修改远程仓库地址即可：

    git remote set-url origin newAddress

另外还可以先删除，然后添加地址：

    git remote rm origin
    git remote add origin newAddress

# 修改本地分支名称

    git branch -m oldBranchName newBranchName

（修改分支名称总共分三步：

一  git branch -m old_branch new_branch   修改本地分支

二  git push origin :old_branch  删除远程分支

三  git push --set-upstream origin new_branch 新增远程分支 ）