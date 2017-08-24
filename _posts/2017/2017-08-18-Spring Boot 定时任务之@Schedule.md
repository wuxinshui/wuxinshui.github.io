---
layout: post
title: Spring Boot 定时任务之@Schedule
category: Spring Boot 
tags: [Spring Boot]
---

>The @Scheduled annotation can be added to a method along with trigger metadata.

## 概念

项目经常会用到定时任务，实现定时任务的方式有很多种，参考[Spring定时任务的几种实现](http://gong1208.iteye.com/blog/1773177)。在Spring框架中，实现定时任务很简单。常用的实现方式是使用注解`@Schedule`。

`@Schedule` 常用来实现简单的定时任务。例如凌晨1点跑批，每1小时更新订单状态等。

## 代码
[官网](https://spring.io/guides/gs/scheduling-tasks/)给出简单的例子。

具体的任务类

```
@Component
public class ScheduleTask {
    @Scheduled(cron = "0/1 * * * * ?")
    public void printSay() {
        System.out.println("This is a say method!"+new Date());
    }
}
```
Application：
```
@SpringBootApplication
@EnableScheduling
public class Application {

    public static void main(String[] args) throws Exception {
        SpringApplication.run(Application.class);
    }
}
```

## 结果

![这里写图片描述](http://img.blog.csdn.net/20170824231550018?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

注意表达式一定要正确，不然结果出不来的。

## 分析

`@Scheduled`注解必须要有，可以指定cron表达式，当然也可以指定其他选项。看源码：

![这里写图片描述](http://img.blog.csdn.net/20170821233616773?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

@EnableScheduling 确保后台任务进程被创建。

## 参考
[34.4.2 The @Scheduled annotation](https://docs.spring.io/spring/docs/current/spring-framework-reference/html/scheduling.html)
[Spring定时任务的几种实现](http://gong1208.iteye.com/blog/1773177)
[Scheduling Tasks](https://spring.io/guides/gs/scheduling-tasks/)
[ springBoot中@Scheduled执行原理解析](http://blog.csdn.net/gaodebao1/article/details/51789225)
