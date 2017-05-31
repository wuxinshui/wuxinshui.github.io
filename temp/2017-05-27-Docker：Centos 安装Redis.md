---
layout: post
title: Docker：Centos 安装Redis
category: Docker 
tags: [Docker]
---
>利用Docker部署redis很方便，还能快速实现集群部署。

## 前言

想研究一下Redis，感觉利用docker部署redis很方便，还能快速实现集群部署。Redis For Widows也有，不太喜欢在Windows部署服务。所以研究一下怎么利用Docker来部署Redis。

## 安装Docker

参考[ windows7 安装docker](http://blog.csdn.net/RickyIT/article/details/52104753)、[Docker 更新boot2docker.iso](http://blog.csdn.net/RickyIT/article/details/72772552)安装Docker。

## 安装Centos

参考[docker 安装ubuntu](http://blog.csdn.net/rickyit/article/details/52105527)在docker中安装Centos。

![这里写图片描述](http://img.blog.csdn.net/20170531094917022?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 安装Redis

参考[官方教程](https://redis.io/download)，安装Redis。

Download, extract and compile Redis with:
```
$ wget http://download.redis.io/releases/redis-3.2.9.tar.gz
$ tar xzf redis-3.2.9.tar.gz
$ cd redis-3.2.9
$ make
```
The binaries that are now compiled are available in the src directory. Run Redis with:
```
$ src/redis-server
```

下面是根据以上教程的实操。

### 安装wget

`yum -y install wget`

![这里写图片描述](http://img.blog.csdn.net/20170531095313837?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 下载

`wget http://download.redis.io/releases/redis-3.2.9.tar.gz`

![这里写图片描述](http://img.blog.csdn.net/20170531095446932?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 解压

`tar xzf redis-3.2.9.tar.gz`

![这里写图片描述](http://img.blog.csdn.net/20170531095553526?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 安装make
上图可以看到，官方提供的centos镜像中是没有make工具的，需要安装一下。

`yum -y install gcc automake autoconf libtool make`

![这里写图片描述](http://img.blog.csdn.net/20170531095713324?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`yum install gcc gcc-c++`

![这里写图片描述](http://img.blog.csdn.net/20170531095803731?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170531095822294?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


### make

![这里写图片描述](http://img.blog.csdn.net/20170531102344759?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

出现下面那就是编译成功了。。。

![这里写图片描述](http://img.blog.csdn.net/20170531102410820?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

提示让运行`make test`,实际运行时是报错的。

![这里写图片描述](http://img.blog.csdn.net/20170531102552307?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

网上查的，这些问题不大，可以直接启动redis。

### 启动

`./redis-server`

![这里写图片描述](http://img.blog.csdn.net/20170531102714606?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

启动成功了。。。

## 保存镜像

`ctrl+c`退出，保存镜像。

![这里写图片描述](http://img.blog.csdn.net/20170531102821904?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## Push镜像

把保存的镜像发布到私人空间，下次可以pull下来用。

![这里写图片描述](http://img.blog.csdn.net/20170531103614780?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

push成功了。。。

![这里写图片描述](http://img.blog.csdn.net/20170531103729828?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

