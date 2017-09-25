---
layout: post
title: Spring Boot 集成RabbitMQ
category: Spring Boot 
tags: [Spring Boot]
---

>RabbitMQ is an open source multi-protocol messaging broker.

## 前言

参照官方[Messaging with RabbitMQ](https://spring.io/guides/gs/messaging-rabbitmq/)，记录在实战中的一些坑。

## 搭建RabbitMQ服务

本文使用Docker搭建MQ服务。Docker部署服务，快捷、方便。

### 安装镜像

参照[docker 安装ubuntu](http://blog.csdn.net/rickyit/article/details/52105527)安装镜像

![这里写图片描述](http://img.blog.csdn.net/20170926001733852?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170926001814836?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 启动镜像

` docker run -d -p 15672:15672 -p 5672:5672 rabbitmq:3-management`

![这里写图片描述](http://img.blog.csdn.net/20170926001506330?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这里要映射2个端口：15672是Web管理界面的端口；5672是MQ访问的端口。

### Web管理界面

`http://192.168.99.100:15672/`

`guest/guest`

![这里写图片描述](http://img.blog.csdn.net/20170926002041439?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170926002107644?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

RabbitMQ服务部署好了。。。

## 集成

### 环境

`IntelliJ IDEA 2016.3.4`

```
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.1.RELEASE</version>
    </parent>
```

```
        <java.version>1.8</java.version>

```


### maven依赖
```
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
    </dependencies>

```

## 代码实现

### 配置

![这里写图片描述](http://img.blog.csdn.net/20170926002300203?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`AmqpInitConfig`

```
@Configuration
@ConditionalOnProperty(prefix = "spring.rabbitmq",name = "enable", matchIfMissing = false)
public class AmqpInitConfig {

    final static String queueName="spring.boot";

    @Bean
    public Queue queue(){
        return new Queue(queueName,false);
    }

    @Bean
    public TopicExchange exchange(){
        return new TopicExchange("spring.boot.exchange");
    }

    @Bean
    public Binding binding(TopicExchange exchange,Queue queue){
        return BindingBuilder.bind(queue).to(exchange).with(queueName+".key");
    }

    @Bean
    public SimpleMessageListenerContainer container(ConnectionFactory connectionFactory, MessageListenerAdapter listenerAdapter){
        SimpleMessageListenerContainer container=new SimpleMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);
        container.setMessageListener(listenerAdapter);
        container.addQueueNames(queueName);
        return container;
    }

    @Bean
    public MessageListenerAdapter listenerAdapter(Receiver receiver){
        return new MessageListenerAdapter(receiver,"receiveMessage");
    }
}
```

### 接受消息
`Receiver`
```
@Component
public class Receiver {
    CountDownLatch latch = new CountDownLatch(1);

    public void receiveMessage(String message) {
        System.out.println("Received <" + message + ">");
        latch.countDown();
    }

    public CountDownLatch getLatch() {
        return latch;
    }
}
```

### 发送消息
`Runner `
```
@Component
public class Runner implements CommandLineRunner {
    private final RabbitTemplate rabbitTemplate;
    private final Receiver receiver;
    private final ConfigurableApplicationContext context;

    public Runner(Receiver receiver, RabbitTemplate rabbitTemplate,
                  ConfigurableApplicationContext context) {
        this.receiver = receiver;
        this.rabbitTemplate = rabbitTemplate;
        this.context = context;
    }

    @Override
    public void run(String... args) throws Exception {
        System.out.println("Sending message...");
        rabbitTemplate.convertAndSend("spring.boot.exchange","spring.boot.key", "Hello from RabbitMQ!");
        receiver.getLatch().await(  10000, TimeUnit.MILLISECONDS);
        context.close();
    }

}
```

## 启动
`AmqpApplication`
```
@SpringBootApplication
@ComponentScan(basePackages = "com.wxs.amqp")
public class AmqpApplication {
    public static void main(String[] args) {
        SpringApplication.run(AmqpApplication.class,args);
    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170925235911721?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 踩过的坑

### 坑一

发送消息需要制定exchange，如果不指定，不会发送消息。

![这里写图片描述](http://img.blog.csdn.net/20170926000622962?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 参考

[Messaging with RabbitMQ](https://spring.io/guides/gs/messaging-rabbitmq/)
[How to use this image Running the daemon](https://hub.docker.com/_/rabbitmq/)