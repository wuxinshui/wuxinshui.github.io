---
layout: post
title: 基础服务系列-Docker 安装MongoDB
category: 基础服务 
tags: [基础服务]
---


>MongoDB is a free and open-source cross-platform document-oriented database program.

## 下载镜像

`docker pull mongo`

![](https://oscimg.oschina.net/oscnet/up-57ce78b2843720353315f443c48542687eb.png)

![](https://oscimg.oschina.net/oscnet/up-14c5dc89e2e856ef6dce3b722f10a024336.png)

## 启动

`docker run --name test-mongo -p 27017:27017 -d mongo:latest`

![](https://oscimg.oschina.net/oscnet/up-7743f078a58a6f084af10fabbb6b628b624.png)

## 下载web管理界面镜像

`docker pull mongo-express`

![](https://oscimg.oschina.net/oscnet/up-14c5dc89e2e856ef6dce3b722f10a024336.png)

## 关联Mongo容器

```docker run --link test-mongo:mongo -p 8082:8081 mongo-express```

![](https://oscimg.oschina.net/oscnet/up-f9996763389bf01cd9a6374581e60420925.png)

## Web访问

浏览器访问web管理界面，`http://192.168.119.254:8082/`

![](https://oscimg.oschina.net/oscnet/up-577d000b3a91dd785a1b2b763f4ae9945bc.png)

## 应用程序访问

### 依赖

```
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
     </dependency>
```

### 配置

```
server.port=9091
# log
log4j.category.org.springframework.data.mongodb=DEBUG
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %40.40c:%4L - %m%n
spring.data.mongodb.uri=mongodb://192.168.119.254:27017/test
```

### 启动

![](https://oscimg.oschina.net/oscnet/up-5f750617d012be873b669d50c607cc9fc2a.png)

### 新增数据

![](https://oscimg.oschina.net/oscnet/up-5513a815507d11199478fb0c21accd1e26c.png)

Mongo Express 新增数据库test

![](https://oscimg.oschina.net/oscnet/up-13a70a8d35447a6d6bef457f0f5a5248f57.png)

新增的数据

![](https://oscimg.oschina.net/oscnet/up-2c52387a2bea199679e7881056c58cd6c17.png)

### 查询数据

`http://localhost:9091/customer/query?name=Wuxinshui`

![](https://oscimg.oschina.net/oscnet/up-bf47707f8cd95f8fc89684beff923b01def.png)


## 参考

[mongo-express-docker](https://github.com/mongo-express/mongo-express-docker)

[mongo](https://hub.docker.com/_/mongo?tab=description)

[10. MongoDB support](https://docs.spring.io/spring-data/mongodb/docs/2.2.3.RELEASE/reference/html/#mongo.core)