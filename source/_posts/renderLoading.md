---
title: vue渲染首屏的loading
date: 2019-09-08 17:26:11
tags: vue
---

在GitHub下载了大佬的项目，运行时发现 vue 项目加载是有个loading的图案，于是，乡下人就开始了好奇的探索。

最后发现，原理就是在 index.html 写一个与 载体 #app 同级的div包裹的内容， 然后配合router.afterEach() 对 该div进行 隐藏（如 display: none）。

该方法适用于项目比较大，首屏加载时间长的项目。