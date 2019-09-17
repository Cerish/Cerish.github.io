---
title: 利用 hexo + github 搭建属于自己的个人网站
date: 2019-09-08 17:26:11
tags: hexo
---

# 前言

作为前端小白的我，在之前项目中遇到问题，一直都是 百度--解决--完事。在一次偶然的机会接触到 [Markdown](http://www.markdown.cn/)，知道博客也是有Markdown 语法生成。再加上看到过有大佬的项目为 https:username/github.io/username。两者一结合，那我也生成一个自己的博客吧！虽然百度上有很多大佬写的教程，但是自己也做个记录，当做知识点记载吧。

<!-- more -->

# 正文

首先需要拥有 node.js 和 git 账号，自行百度下载。

## 1.安装全局 hexo-cli

新建一个文件夹来存放生成的 [Hexo](https://hexo.io/) 程序文件，例如 Hexo-Blog。 右键打开 Git Bash Here，使用npm 安装 Hexo博客程序。

    $ npm install -g hexo-cli

## 2.初始化项目与本地预览

    $ hexo init # 初始化
    $ hexo s # 启动预览

访问 `http://localhost:4000`，出现 Hexo 默认页面，本地博客安装成功！
![hexoCli](/images/hexoCli.png)

Tips: 如果出现页面加载不出来可能是端口被占用了。Ctrl+C 关闭服务器，运行 hexo server -p 5000 更改端口号后重试。

## 3.部署 Hexo 到 GitHub Pages

### 3.1 首先安装 hexo-deployer-git：

    $ npm install hexo-deployer-git --save

### 3.2 然后修改 _config.yml 文件末尾的 Deployment 部分，修改成如下：

    deploy:
      type: git
      repository: git@github.com:用户名/用户名.github.io.git
      branch: master

完成后运行 hexo d 将网站上传部署到 GitHub Pages。这时访问我们的 GitHub 域名 `https://用户名.github.io` 就可以看到 Hexo 网站了。

## 4. 开始使用
### 4.1 发布文章
进入博客所在目录，右键打开 Git Bash Here，创建博文：

    $ hexo new "Blog name"

然后 source 文件夹中会出现一个 Blog name.md 文件，就可以使用 Markdown 编辑器在该文件中撰写文章了。

写完后运行下面代码将文章渲染并部署到 GitHub Pages 上完成发布。以后每次发布文章都是这两条命令。

    $ hexo g   # 生成页面
    $ hexo d   # 部署发布
    或者使用
    $ hexo g -d

### 4.2 常用命令
    hexo new "name"       # 新建文章
    hexo new page "name"  # 新建页面
    hexo g                # 生成页面
    hexo d                # 部署
    hexo g -d             # 生成页面并部署
    hexo s                # 本地预览
    hexo clean            # 清除缓存和已生成的静态文件
    hexo help             # 帮助

## 5. 更换 themes

### 5.1 挑选主题

在搭建完个人博客后，会发现原生的主题默认为 `landscape`，你可以在[Hexo themes](https://hexo.io/themes/)挑选自己喜欢的主题模版。
### 5.2 下载主题

挑选完喜欢的主题后，进入博主的GitHub，选择该主题样式的仓库（名字一般为hexo-theme-name），下拉到md文档说明，根据博主的介绍下载主题。如若没有介绍文档说明，则在博客目录下，右键打开Git Bash Here，执行

    $ git clone https://github.com/[githubName]/hexo-theme-name.git themes/name  (githubName为博主的github名称，name为主题名称)

进入根目录下的 `themes`文件夹下，你会发现多了一个主题名称的文件夹，此时主题下载完毕。

### 5.3 更改配置

在主题目录下找到_config.yml，

    theme: landscape ---> name (你下载主题的名称)

此时主题已经更改完成，但目前博客的信息还是原有博主的，需要进入 `themes/name/_config.yml` 目录下更改个人信息。到此，主题更换已经大功告成！