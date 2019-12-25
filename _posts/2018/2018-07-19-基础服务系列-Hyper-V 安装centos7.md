---
layout: post
title: 基础服务系列-Hyper-V 安装centos7
category: 基础服务 
tags: [基础服务]
---
 
>Hyper-V 安装centos7

## 前言

`docker for windows`在Windows10 上安装，各种问题，打算放弃了。。。

### 问题1

  ![](https://oscimg.oschina.net/oscnet/4d66927b0eb115bc65eb5dd3c36c69af36a.jpg)
  
  可能是没有分配IP地址
  
  ![](https://oscimg.oschina.net/oscnet/3d5c60a87d888d12bf4ad51613f229420cd.jpg)
  
  尝试切换虚拟交换机也不行。。。
  
  ![](https://oscimg.oschina.net/oscnet/6e991c7f0e25e6580a0768b7dc44876316b.jpg)
  
  打算放弃治疗了。。。。
  
  在 Hyper-V 安装centos7。

## 下载

[官网下载](https://www.centos.org/download/)

![](https://oscimg.oschina.net/oscnet/74fdfb5db42103633da79181d47f9b81e2d.jpg)

有3个版本，楼主用的是DVD版本。

## 安装

### 新建虚拟机

![](https://oscimg.oschina.net/oscnet/163cc169606545893fbafdf7d08521b49e6.jpg)

![](https://oscimg.oschina.net/oscnet/115585ccd765fd6d2adbb6dde89df9c2c16.jpg)

为啥。。。。看下面

![](https://oscimg.oschina.net/oscnet/c803ac58cf902763fe95ec0e55e37d64313.jpg)


如果不小心勾选了。。

先关闭虚拟机，点开设置

![](https://oscimg.oschina.net/oscnet/55e64ac0d7aaad3d6e0548429071c025732.jpg)

## 启动

![](https://oscimg.oschina.net/oscnet/48d7f22983f35476d3a215479102a01292f.jpg)

![](https://oscimg.oschina.net/oscnet/732c4d204b8b04ee4c50d5a476c4785f72a.jpg)

![](https://oscimg.oschina.net/oscnet/61e91e9db38d04a35f9e152672d191847b2.jpg)

![](https://oscimg.oschina.net/oscnet/53118d06d87cb4f2d4c1c1dd35190eeeb07.jpg)

![](https://oscimg.oschina.net/oscnet/e5724fdd55b3b446efef59f0aadba81a25c.jpg)

右下角的`continue`，可能会看不到，需要调节下显示器像素

![](https://oscimg.oschina.net/oscnet/92593eb626e8c7d28ee2f3782636ab3636b.jpg)

![](https://oscimg.oschina.net/oscnet/1ba29e60de3ac68e2d74d98386e0f4d4089.jpg)

![](https://oscimg.oschina.net/oscnet/9c79bdccff44bfa0c5551e8f5266275803d.jpg)

![](https://oscimg.oschina.net/oscnet/dee5a8edb42b699b1dd94e23ecd69c62860.jpg)


## 网络

![](https://oscimg.oschina.net/oscnet/c535b564fa9395f68f0e6e545f8687db56c.jpg)

![](https://oscimg.oschina.net/oscnet/1bf8830ce7b82399a6ac6758ca00b4d4dd4.jpg)

切换下交换网络。。。即可

![](https://oscimg.oschina.net/oscnet/143cc8cd46b14bd2931bdf797b5d088f8b2.jpg)

## 环境配置

安装JDK，参考博文[基础服务系列-centos7 安装JDK](https://my.oschina.net/wuxinshui/blog/1827946)

![](https://oscimg.oschina.net/oscnet/156f804a8a92816d59faeb55b7135a69809.jpg)


over....

