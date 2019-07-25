---
layout: post
title: SonarQube 安装
category: 编程 
tags: [编程]
---


>SonarQube® software (previously called Sonar) is an open source quality management platform, dedicated to continuously analyze and measure technical quality, from project portfolio to method.

## 简介

SonarQube（以前叫Sonar）是一个用于代码质量管理的开源平台，用于管理源代码的质量，可以从七个维度检测代码质量通过插件形式，可以支持包括java,C#,C/C++,PL/SQL,Cobol,JavaScrip,Groovy等等二十几种编程语言的代码质量管理与检测。

参考过其他写Sonar的文章，上面用的版本比较低。本文使用的版本是`SonarQube 6.4`

## 下载安装

### 下载

[官网下载](https://www.sonarqube.org/downloads/)`SonarQube 6.4`。

下载下来是个压缩文件，解压到当前目录。

![这里写图片描述](http://img.blog.csdn.net/20170710180659752?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


### 配置

SonarQube是有些默认配置的，可以根据实际需要配置。

![这里写图片描述](http://img.blog.csdn.net/20170710181456922?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

配置数据库。支持的数据库有：MySQL, Oracle, PostgreSQL and Microsoft SQLServer。

![这里写图片描述](http://img.blog.csdn.net/20170710181252803?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

之前的版本是需要额外配置驱动路径。当前下载的版本自带有mysql的驱动包。

![这里写图片描述](http://img.blog.csdn.net/20170710181406834?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 安装服务

此次系统是Windows7，点击运行`InstallNTService.bat`。

![这里写图片描述](http://img.blog.csdn.net/20170710181535232?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

安装好的服务

![这里写图片描述](http://img.blog.csdn.net/20170710181606789?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

右键启动服务，也可以点击`StartNTService.bat`启动。

![这里写图片描述](http://img.blog.csdn.net/20170710181631618?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


## 使用

### 访问

浏览器访问：http://localhost:9000 。IP和端口可以在`sonar.properties`文件中配置的。默认的是 http://localhost:9000。

第一次访问会有点慢，服务需要创建大量的表。

![这里写图片描述](http://img.blog.csdn.net/20170710182155086?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

页面

![这里写图片描述](http://img.blog.csdn.net/20170710181931594?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 登录

第一次登录不知道密码，怎么办呢？搜一下官方文档

![这里写图片描述](http://img.blog.csdn.net/20170711183521020?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

原来默认的用户名和密码都是`admin`。

我们来看看数据库

![这里写图片描述](http://img.blog.csdn.net/20170711183623606?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

数据库是把密码给加密了。。。

![这里写图片描述](http://img.blog.csdn.net/20170711184314021?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

登录成功。

## 参考

[Sonar——代码质量管理平台](http://blog.csdn.net/caozhangyingfei0109/article/details/43938383)
[Installing the Server](https://docs.sonarqube.org/display/SONAR/Installing+the+Server)
[ SonarQube代码质量管理平台安装与使用](http://blog.csdn.net/hunterno4/article/details/11687269)