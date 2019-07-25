---
layout: post
title: Docker：启动Redis镜像
category: 编程 
tags: [编程]
---
>在Docker中部署Redis。


## 复制Redis文件

`cp /root/redis-3.2.9/src/redis-server /usr/local/bin`

`cp /root/redis-3.2.9/src/redis-cli /usr/local/bin`

## 复制配置文件

`mkdir /etc/redis`

`cp /root/redis-3.2.9/redis.conf /etc/redis/6379.conf`

![这里写图片描述](http://img.blog.csdn.net/20170602145031954?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 创建数据目录

`mkdir /var/redis`

`mkdir /var/redis/6379`

![这里写图片描述](http://img.blog.csdn.net/20170602145108842?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 复制初始化脚本

`cp /root/redis-3.2.9/utils/redis_init_script /etc/init.d/redis_6379`

![这里写图片描述](http://img.blog.csdn.net/20170602145134971?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 编辑初始化脚本

` vi /etc/init.d/redis_6379`

![这里写图片描述](http://img.blog.csdn.net/20170602145205331?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 编辑配置文件

![这里写图片描述](http://img.blog.csdn.net/20170602145237077?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170602145259020?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170602145321645?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170602145349953?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170602145429547?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


## 启动

![这里写图片描述](http://img.blog.csdn.net/20170614160246007?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


## 参考

[Installing Redis more properly](https://redis.io/topics/quickstart)

[service redis does not support chkconfig 的解决办法](http://www.quyu.net/info/405.html)