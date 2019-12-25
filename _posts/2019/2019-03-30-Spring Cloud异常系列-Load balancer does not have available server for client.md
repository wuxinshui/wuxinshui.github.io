---
layout: post
title: Spring Cloud异常系列-Load balancer does not have available server for client
category: SpringCloud 
tags: [SpringCloud]
---

>Load balancer does not have available server for client

## 异常信息

![](https://oscimg.oschina.net/oscnet/41b0ef47c4d3c97f0bdb896279d2e300815.jpg)

## 解决方案

汇总网上的一些解决方案，供参考。

### 确认包引用

看下工程的依赖里，是否有eureka的包。

```
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
```

### 确认application-name

确认下 服务的名称是否一致。

- Feign Client中请求的服务名称

![](https://oscimg.oschina.net/oscnet/7f7c1153a2c5b75a78b21cd8cf110b05181.jpg)
- Eureka中注册的服务名

![](https://oscimg.oschina.net/oscnet/007d540caa5aeee07d6ce40eb4db7cc61ed.jpg)
- 服务提供者的application-name

![](https://oscimg.oschina.net/oscnet/3666152aaf10e0dcb6ba695eacaa308653a.jpg)


### 确认fetch-registry


![](https://oscimg.oschina.net/oscnet/0edc25865e15336db2e593dc7639aa33509.jpg)

fetch-registry 默认是true，实践中，由于项目配置是copy过来的，没注意到这项配置。导致consumer请求provider拿不到注册信息。

![](https://oscimg.oschina.net/oscnet/5820c8c6c5eb20bddc437e3310406cb483a.jpg)

`fetch-registry: true`，改下配置，重启。

![](https://oscimg.oschina.net/oscnet/bdf2f4e7c7dffd16e9bbd3134672f5284ea.jpg)

## 参考
[Feign报错Caused by: com.netflix.client.ClientException: Load balancer does not have available server for client](http://www.cnblogs.com/hellxz/p/8920378.html)

[解决SpringCloud的bug的神经历（持续更新中...）](https://www.jianshu.com/p/434062d7aef5)

[成功解决com.netflix.client.ClientException: Load balancer does not have available server for client](http://yayihouse.com/yayishuwu/chapter/1288)