---
layout: post
title: Spring Boot Redis 数据缓存
category: Spring Boot 
tags: [Spring Boot]
---
>Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。

## 前言

之前都是在Spring MVC中使用Redis，这里记录在Spring Boot中使用Redis作为数据缓存的过程。参考[Spring Boot集成Spring Data Jpa](http://blog.csdn.net/RickyIT/article/details/73178856),搭建项目。

## 启动Redis

本次是在Docker中部署Redis,从`https://hub.docker.com/`中直接pull官方的redis镜像，启动redis。

![这里写图片描述](http://img.blog.csdn.net/20170613110301459?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

使用`RedisDesktopManager`测试一下。。。

![这里写图片描述](http://img.blog.csdn.net/20170613110404723?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

连接成功了。接下来看看Spring Boot中怎么使用Redis。

## 集成

### POM文件

```--xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

### Redis配置
`application.properties`
```
# REDIS (RedisProperties)
# Redis数据库索引（默认为0）
spring.redis.database=0  
# Redis服务器地址
spring.redis.host=192.168.99.100
# Redis服务器连接端口
spring.redis.port=6379  
# Redis服务器连接密码（默认为空）
spring.redis.password=
# 连接池最大连接数（使用负值表示没有限制）
spring.redis.pool.max-active=8  
# 连接池最大阻塞等待时间（使用负值表示没有限制）
spring.redis.pool.max-wait=-1  
# 连接池中的最大空闲连接
spring.redis.pool.max-idle=8  
# 连接池中的最小空闲连接
spring.redis.pool.min-idle=0  
# 连接超时时间（毫秒）
spring.redis.timeout=0  
```


### RedisCacheConfig类

```
@Configuration
@EnableCaching
public class RedisCacheConfig extends CachingConfigurerSupport {
	@Bean
	public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory factory) {
		RedisTemplate<String, String> template = new RedisTemplate<>();
		template.setConnectionFactory(factory);
		Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
		ObjectMapper om = new ObjectMapper();
		om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
		om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
		jackson2JsonRedisSerializer.setObjectMapper(om);
		template.setValueSerializer(jackson2JsonRedisSerializer);
		template.afterPropertiesSet();
		return template;
	}

	@SuppressWarnings("rawtypes")
	@Bean
	public CacheManager cacheManager(RedisTemplate redisTemplate) {
		RedisCacheManager rcm = new RedisCacheManager(redisTemplate);
		//设置缓存过期时间,默认是0l
		rcm.setDefaultExpiration(60);//秒
		return rcm;
	}

}
```

## 使用

```--java
	@RequestMapping(value = "/search", method = RequestMethod.POST)
	@Cacheable(value = "search")
	public ModelMap search() {
		List<User> userList = userService.selectAll();
		LOGGER.info("search....");
		return result(Constant.SUCCESS_CODE, Constant.SUCCESS_MSG, userList);
	}
```


## 测试

#### 1. PostMan发起请求

![这里写图片描述](http://img.blog.csdn.net/20170614144719384?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
#### 2. 后台接受请求

![这里写图片描述](http://img.blog.csdn.net/20170614144737743?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170614144806665?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
#### 3. 响应

![这里写图片描述](http://img.blog.csdn.net/20170614145315776?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
#### 4. 缓存

使用RedisDesktopManager 查看缓存的结果。

![这里写图片描述](http://img.blog.csdn.net/20170614144832702?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

下次请求，会直接从redis返回结果。不会再请求数据库，接口响应的时间大大缩短。

![这里写图片描述](http://img.blog.csdn.net/20170614145432992?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

