---
layout: post
title: Feign-手动创建FeignClient
category: SpringCloud 
tags: [SpringCloud]
---

>Feign跨网络请求服务。

## 前言
在[Feign-请求不同注册中心的服务](https://my.oschina.net/wuxinshui/blog/3058490)中，提到，如果需要请求不同注册中心的服务，可以设置`@FeignClient`的`url`属性。

这种做法有个缺点，需要服务消费者，配置各个环境的`url`。

如果服务提供方`url`变更，需要通知到服务消费者，如果消费者很多，变更通知也是件麻烦事。

基于java的封装特性，作为独立的服务提供者，如果能封装`url`，岂不是更好？

## 解决方案
### 服务提供者

```
//@FeignClient(name = "feign-provider")
public interface CustomizeFeignApi {

    @RequestMapping(value = "/customize", method = RequestMethod.GET)
    String queryCustomize();
}
```

`@FeignClient`，不需要。加上的话，会自动创建对应bean，达不到手动创建的目的。

自定义配置

```
@Configuration
@Import(FeignClientsConfiguration.class)
public class FeignClientConfig {
    @Bean
    public CustomizeFeignApi customizeFeignApi(Contract contract, Decoder decoder, Encoder encoder) {
        return Feign.builder().contract(contract).encoder(encoder).decoder(decoder).target(CustomizeFeignApi.class, "http://localhost:8004");
    }

}
```

`@Configuration`：需要被消费者应用扫描到。

`@Import(FeignClientsConfiguration.class)`：如果消费者上下文不存在`Contract`, `Decoder`, `Encoder`，由`FeignClientsConfiguration`提供默认bean。

以上，可封装为`jar`，上传到公司nexus私服，有消费者下载使用。

![](https://oscimg.oschina.net/oscnet/6732c4c0da2ed2f6e16796cc9ea54e426ca.jpg)

## 服务消费者

依赖上文的jar。
```
		<!--服务提供者-->
		<dependency>
			<groupId>com.wxs.springcloud</groupId>
			<artifactId>springcloud-sample-feign-provider-spi</artifactId>
			<version>0.0.1-SNAPSHOT</version>
		</dependency>
```

注入使用。

```
@RestController
public class CustomizeFeignTestController {
    @Autowired
    private CustomizeFeignApi customizeFeignApi;

    @GetMapping("/customize")
    public String query() {
        return customizeFeignApi.queryCustomize();
    }
}
```
启动类，添加扫描。
`@ComponentScan("com.wxs.feign.provider.api")
`

验证

![](https://oscimg.oschina.net/oscnet/87b6acef16af301c27bff6d34e9debf9269.jpg)

## 遇到的问题

`Method not annotated with HTTP method type (ex. GET, POST)`

```
Caused by: java.lang.IllegalStateException: Method queryCustomize not annotated with HTTP method type (ex. GET, POST)
	at feign.Util.checkState(Util.java:128) ~[feign-core-9.5.0.jar:na]
	at feign.Contract$BaseContract.parseAndValidateMetadata(Contract.java:97) ~[feign-core-9.5.0.jar:na]
	at feign.Contract$BaseContract.parseAndValidatateMetadata(Contract.java:64) ~[feign-core-9.5.0.jar:na]
	at feign.ReflectiveFeign$ParseHandlersByName.apply(ReflectiveFeign.java:146) ~[feign-core-9.5.0.jar:na]
	at feign.ReflectiveFeign.newInstance(ReflectiveFeign.java:53) ~[feign-core-9.5.0.jar:na]
	at feign.Feign$Builder.target(Feign.java:218) ~[feign-core-9.5.0.jar:na]
	at feign.Feign$Builder.target(Feign.java:214) ~[feign-core-9.5.0.jar:na]
	at com.wxs.feign.consumer.config.FeignClientConfig.customizeFeignApi(FeignClientConfig.java:38) ~[classes/:na]
```
`feign.Feign.Builder`默认提供的是：`new Contract.Default()`。

需要指定`Contract`为`SpringMvcContract`。

`Feign.builder().contract(new SpringMvcContract()).target(CustomizeFeignApi.class, "http://localhost:8004")`

## 参考
[Creating Feign Clients Manually](https://cloud.spring.io/spring-cloud-netflix/multi/multi_spring-cloud-feign.html)

[Feign: Method not annotated with HTTP method type (ex. GET, POST)](https://github.com/spring-cloud/spring-cloud-netflix/issues/760)
