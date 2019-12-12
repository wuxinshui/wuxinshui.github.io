---
layout: post
title: 基础服务系列-Centos7 安装Node.js10
category: 基础服务 
tags: [基础服务]
---

>Node.js® is a JavaScript runtime built on Chrome's V8 JavaScript engine.


## 简介

以官方centos镜像为基础，安装[Node.js 10](https://nodejs.org/en/download/)。

Unix prerequisites：
- gcc and g++ >= 6.3 or newer, or
- GNU Make 3.81 or newer
- Python (see note above)
	- Python 2.7
	- Python 3.5, 3.6, and 3.7 are experimental.
	
Python3.7 可以参考[基础服务系列-Centos7 安装Python3.7](https://my.oschina.net/wuxinshui/blog/3076911)

## 下载

![](https://oscimg.oschina.net/oscnet/87cfba47c19d3b1c23e0620e08da5fad4b3.jpg)

`wget https://nodejs.org/dist/v10.16.0/node-v10.16.0-linux-x64.tar.xz`

![](https://oscimg.oschina.net/oscnet/63aa444d0f28790a57333da935312b6ff98.jpg)

![](https://oscimg.oschina.net/oscnet/a8ab64cb01e1aee00595ed2ff6886da6b7f.jpg)

## 解压

`tar -xvf node-v10.16.0-linux-x64.tar.xz`

`mv node-v10.16.0-linux-x64 /usr/local/lib/nodejs/node-v10.16.0-linux-x64/`

## 配置环境变量

```
VERSION=v10.16.0
DISTRO=linux-x64
export PATH=/usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin:$PATH
```

![](https://oscimg.oschina.net/oscnet/51cd3f8da0d5c3d6f0979bdcdcadf772596.jpg)

## 验证

`node -v`

![](https://oscimg.oschina.net/oscnet/d7bc0630ed75e7bf79c9532d1807d783191.jpg)

## 参考

[Installation](https://github.com/nodejs/help/wiki/Installation)

[Building Node.js
](https://github.com/nodejs/node/blob/master/BUILDING.md#building-nodejs-on-supported-platforms)

我的博客即将同步至腾讯云+社区，邀请大家一同入驻：https://cloud.tencent.com/developer/support-plan?invite_code=3qr1z8yziack4