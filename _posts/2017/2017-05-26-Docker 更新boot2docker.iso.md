---
layout: post
title: Docker 更新boot2docker.iso
category: Docker 
tags: [Docker]
---
>Windows7 安装Docker 更新boot2docker.iso

## 前言

最近需要研究一下redis，想着用docker来部署更好，还能搞个集群。然后去docker官网看了一下，当前最新版本的`Docker Community Edition for Windows`只能运行在windows10上，我还在用windows7，只能去下载之前的DockerToolbox。

## 启动运行

参照之前写的博客[windows7 安装docker](http://blog.csdn.net/rickyit/article/details/52104753),点击`Docker Quickstart Terminal`图标启动，不能正常启动。

![这里写图片描述](http://img.blog.csdn.net/20170526181213933?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`Docker Quickstart Terminal`启动后会复制`C:\Users\Administrator\.docker\machine\cache`下的镜像`boot2docker.iso`到`C:\Users\Administrator\.docker\machine\machines\default`下面。

检测到默认的镜像不是最新版本的，需要到[https://github.com/boot2docker/boot2docker/releases](https://github.com/boot2docker/boot2docker/releases)下载最新的，并复制到`C:\Users\Administrator\.docker\machine\cache`目录下。

![这里写图片描述](http://img.blog.csdn.net/20170526181412344?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

比较烦恼的是[boot2docker.iso](https://github.com/boot2docker/boot2docker/releases/download/v17.05.0-ce/boot2docker.iso)下载需要访问Github，用Chrome下载很慢，得一个小时。


翻了一下墙，重新点击`Docker Quickstart Terminal`图标，会自动下载最新版本的`boot2docker.iso`，速度很快啊。。。

![这里写图片描述](http://img.blog.csdn.net/20170526181127121?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

下载成功了。。。。

![这里写图片描述](http://img.blog.csdn.net/20170526181348840?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


docker启动成功。

![这里写图片描述](http://img.blog.csdn.net/20170526185509033?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)