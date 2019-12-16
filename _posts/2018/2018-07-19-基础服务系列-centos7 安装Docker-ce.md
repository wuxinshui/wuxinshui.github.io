---
layout: post
title: 基础服务系列-centos7 安装Docker-ce
category: 基础服务 
tags: [基础服务]
---
    
## 前言

本教程是在centos7 平台上安装的。参考官网[Get Docker CE for CentOS](https://docs.docker.com/install/linux/docker-ce/centos/#install-docker-ce-1),利用repository安装docker不成功。
下面是使用下载好的rpm包来安装docker。

## 环境信息

![](https://oscimg.oschina.net/oscnet/3ad7b1803ad775915708a668f72be4d48cc.jpg)

## 下载

[点这里下载](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/)

![](https://oscimg.oschina.net/oscnet/a252c6435659481b802e9875c1aee7160d1.jpg)

![](https://oscimg.oschina.net/oscnet/621eb4cfc91eeb121f734d86575c4ef49fc.jpg)

## 安装

`sudo yum install docker-ce-17.12.1.ce-1.el7.centos.x86_64.rpm`

![](https://oscimg.oschina.net/oscnet/abdbe6e4af2bcf97dedffbb522415635234.jpg)

![](https://oscimg.oschina.net/oscnet/50a0b724301dcdacb28f13159d1d4c9a1b0.jpg)

## 启动

`sudo systemctl start docker`

![](https://oscimg.oschina.net/oscnet/36a120ea64f38dabd11145502b4f43f9767.jpg)

![](https://oscimg.oschina.net/oscnet/28d61d9b749fe2bf6a5a867e31f1a7789f3.jpg)

![](https://oscimg.oschina.net/oscnet/67424df0fc8cdd4d3d2fa13e903815b07ae.jpg)

## 失败的安装。。。。

### 安装18版rpm

` sudo yum install docker-ce-18.03.0.ce-1.el7.centos.x86_64.rpm`

![](https://oscimg.oschina.net/oscnet/d814b468c7b55648228866cedfe9056babf.jpg)

![](https://oscimg.oschina.net/oscnet/787796d8f21cb6a7b40ccbcc7c74b5d2285.jpg)

### 在线安装17版本

参考官网在线安装17版本。

[Get Docker CE for CentOS](https://docs.docker.com/install/linux/docker-ce/centos/#install-docker-ce-1)

#### 删除已安装

`sudo yum remove docker docker-common docker-selinux docker-engine`

![](https://oscimg.oschina.net/oscnet/df57edf206d8f2fe83accf29b0c60ebc555.jpg)

`sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate     docker-logrotate docker-selinux docker-engine-selinux docker-engine`

![](https://oscimg.oschina.net/oscnet/96bc20ad1a514ffaf4463b2793b51d9bbd6.jpg)

#### 查看repository docker版本

`yum list docker-ce --showduplicates | sort -r`

![](https://oscimg.oschina.net/oscnet/170b233da8d40af75e35da5f3a3c3cb4105.jpg)

选择17年最后的稳定版本

`docker-ce.x86_64 17.12.1.ce-1.el7.centos docker-ce-stable`

![](https://oscimg.oschina.net/oscnet/9a88ddd30813b9ed645cf6d0d9dfa7ae644.jpg)

#### 安装

`sudo yum install docker-ce-17.12.1.ce-1.el7.centos`

![](https://oscimg.oschina.net/oscnet/2571dfc057bb0e1c043f122a8f4ff107abf.jpg)

![](https://oscimg.oschina.net/oscnet/c598ee8947fc39f81a4d7eec8a037e6aef9.jpg)

/(ㄒoㄒ)/~~   可能是网络原因吧。。。。    