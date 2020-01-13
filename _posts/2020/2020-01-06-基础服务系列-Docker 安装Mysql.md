---
layout: post
title: 基础服务系列-Docker 安装Mysql
category: 基础服务 
tags: [基础服务]
---


>MySQL is the world's most popular open source database. 

## 下载

选择合适的tag

![](https://oscimg.oschina.net/oscnet/up-0167879f1c527840d4e24f8233ec8ec266f.png)

`docker pull mysql:5.6`

![](https://oscimg.oschina.net/oscnet/up-95ae356a0f171dfaaa53199f55c5f6d1410.png)

## 启动

`docker run -itd --name mysql-test -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.6`

![](https://oscimg.oschina.net/oscnet/up-c95979290119ec34a910bc1f7dff6901332.png)

## 连接

![](https://oscimg.oschina.net/oscnet/up-19eb4aec5d415721996f34b859251df006b.png)

## 查看数据库版本

`SELECT VERSION();`

![](https://oscimg.oschina.net/oscnet/up-a8ff9fc90b4a819c242447441bedbb3426d.png)

## 参考

[Docker Official Images](https://hub.docker.com/_/mysql?tab=description)

[8.2.1.17 LIMIT Query Optimization](https://dev.mysql.com/doc/refman/5.7/en/limit-optimization.html)



