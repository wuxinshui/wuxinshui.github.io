---
layout: post
title: Spring Boot集成Spring Data Jpa
category: Spring Boot 
tags: [Spring Boot]
---
>Spring Boot 使用Jpa，很简单，也很方便，这里简单介绍一下。



## 前言
 
 之前写过[spring data jpa 入门](http://blog.csdn.net/rickyit/article/details/54378695),Spring Boot 使用Jpa，很简单，也很方便，这里简单介绍一下。

## 目录结构

[spring data jpa 入门](http://blog.csdn.net/rickyit/article/details/54378695)中已经介绍过jpa怎么玩，直接建立对应的类和包。

![这里写图片描述](http://img.blog.csdn.net/20170612183748095?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)。

## 配置jar包

Spring Boot默认提供的jar是`spring-boot-starter-data-jpa`，它提供了以下关键依赖。

* Hibernate — One of the most popular JPA implementations.
* Spring Data JPA — Makes it easy to implement JPA-based repositories.
* Spring ORMs — Core ORM support from the Spring Framework.

在你的pom文件中直接配置就好。

```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
```

## 配置数据库

Spring Boot 默认提供`tomcat-jdbc`连接池，也可以通过`spring.datasource.type`属性配置其他的连接池。这里使用默认的。`application.properties`配置如下：

```
server.port=9091
# mysql
spring.datasource.url=jdbc:mysql://localhost/redis_test
spring.datasource.username=root
spring.datasource.password=admin
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
# Number of ms to wait before throwing an exception if no connection is available.
spring.datasource.tomcat.max-wait=10000
# Maximum number of active connections that can be allocated from this pool at the same time.
spring.datasource.tomcat.max-active=50
# Validate the connection before borrowing it from the pool.
spring.datasource.tomcat.test-on-borrow=true
```

## 测试

启动，测试一下。

![这里写图片描述](http://img.blog.csdn.net/20170613105423484?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

使用postman联调一下。

![这里写图片描述](http://img.blog.csdn.net/20170613105520906?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

优化一下时间格式。

![这里写图片描述](http://img.blog.csdn.net/20170613144648482?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170613144714686?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

成功了。。。

## 参考

[29.3 JPA and ‘Spring Data’](http://docs.spring.io/spring-boot/docs/1.5.4.RELEASE/reference/htmlsingle/#boot-features-spring-data-jpa-repositories)
[29.1.2 Connection to a production database](http://docs.spring.io/spring-boot/docs/1.5.4.RELEASE/reference/htmlsingle/#boot-features-spring-data-jpa-repositories)