---
title: git clone 速度慢解决
date: 2019-10-08 12:26:11
tags: git
---

在用 git 下载源码时，速度很多时候都是几kb的速度，遇到大点的文件，就需要下很久。

解决方法1： 

用记事本打开 hosts 文件，

Windows文件的存在位置于

    C:\Windows\System32\drivers\etc\hosts

linux/ mac os 文件存在于

    sudo vim /etc/hosts

在文件的末尾添加


    151.101.72.249 http://global-ssl.fastly.Net
    192.30.253.112 http://github.com

重启电脑，速度将会加快。

解决方法2：

由于某种原因，国内访问国外网站会比较慢，执行下面的语句，开启sock5代理， 速度会快很多。

    git config --global http.proxy 'socks5://127.0.0.1:1080' 
    git config --global https.proxy 'socks5://127.0.0.1:1080'

到此完毕。
