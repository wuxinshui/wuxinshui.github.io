---
layout: post
title: 基础服务系列-Windows10 安装Docker
category: 基础服务 
tags: [基础服务]
---

>Windows10 安装Docker

## 前言
作为基础服务系列的一部分，记录下Windows10平台，Docker的安装过程。安装过程和之前写过[windows7 安装docker](https://my.oschina.net/wuxinshui/blog/847558)差不多，使用上还是有细微区别的。

## 检查

检查自己系统版本：

![](https://oscimg.oschina.net/oscnet/45f5d2afe1c6b60f419b8dc0d0595a63188.jpg)

![](https://oscimg.oschina.net/oscnet/291d800658bce5105ccb4e33bef6e882354.jpg)

一般是默认开启的。如果没有，请参考[如何启用Windows 10客户端Hyper-V](https://www.sysgeek.cn/enable-windows-10-client-hyper-v/)

## 下载
[官网](https://www.docker.com/docker-windows)

![](https://oscimg.oschina.net/oscnet/5f6d549b8166eeda9301db69cd1c2b0c9d4.jpg)

注意自己的Windows版本。

下载太慢或者不能下载的话看[这里](https://pan.baidu.com/s/1gZgcZWJckhIsq775E8AP-A)

## 安装

下载后的文件

![](https://oscimg.oschina.net/oscnet/1d1f157ba0953860fd8577050798b87836d.jpg)

双击安装，默认安装就行。

## 启动

![](https://oscimg.oschina.net/oscnet/ee612ba1c2b76ec4bae6cff2684dea6dd72.jpg)

![](https://oscimg.oschina.net/oscnet/495cf24af4c6f66b134446fbf3f7bf12eaf.jpg)

## docker检测

打开Windwos powershell。

`docker --version`

![](https://oscimg.oschina.net/oscnet/faaa72e1a3dcfc395a26abb57dbd6d039ab.jpg)

`docker run hello-world`

运行的时候可能会遇到下面的问题

```
nable to find image 'hello-world:latest' locally
C:\Program Files\Docker\Docker\Resources\bin\docker.exe: Error response from daemon: Get https://registry-1.docker.io/v2/library/hello-world/manifests/latest: unauthorized: incorrect username or password.
See 'C:\Program Files\Docker\Docker\Resources\bin\docker.exe run --help'.
```

![](https://oscimg.oschina.net/oscnet/78aef9c25ceb582ad33969b1d016a4f7b3e.jpg)

登录的时候用用户名，别用邮箱地址。

![](https://oscimg.oschina.net/oscnet/8c6f8607932fb423f0b1f76f9933518c1d9.jpg)

![](https://oscimg.oschina.net/oscnet/cab0a92d1d30737cacb456f1661de29e86e.jpg)

成功了。。

## Hyper-V

![](https://oscimg.oschina.net/oscnet/c26fc6e7c5703263540fb425f5f41880993.jpg)

## 区别

Windows7平台，需要安装`DockerToolbox.exe`,安装的时候会安装Git，如果本地已经安装，会先卸载。