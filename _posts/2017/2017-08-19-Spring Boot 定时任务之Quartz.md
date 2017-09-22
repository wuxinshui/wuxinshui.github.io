---
layout: post
title: Spring Boot 定时任务之Quartz
category: Spring Boot 
tags: [Spring Boot]
---

>Quartz is a richly featured, open source job scheduling library that can be integrated within virtually any Java application。

## 前言

当定时任务愈加复杂时，使用Spring注解[@Schedule](http://blog.csdn.net/rickyit/article/details/77284064)已经不能满足业务需要。
`Quartz`的优点：

- 	丰富的Job操作API；
- 	支持多种配置；
- 	Spring Boot无缝集成；
- 	支持持久化；
- 	支持集群。

Quartz还支持开源，是一个功能丰富的开源作业调度库，可以集成到几乎任何Java应用程序中。

## 核心概念

- Job接口，实现类写具体的业务逻辑。
- JobDetail 封装Job详细信息，如Job分组、名称、执行Job的类。
- Trigger 触发器，记录触发器分组、名称、定时表达式。
- CronExpression 定时表达式，封装在Trigger中。
- Schedule 调度器。Schedule动态操作Job。如新增、更新、暂停、启动、删除、立即执行等。JobDetail 、Trigger 需要注册到Schedule中。

## 集成

Spring Boot 对集成Quartz提供了很好的支持，只需要在`pom`文件中添加以下依赖即可。
```
        <!--quartz-->
        <!-- 该依赖必加，里面有spring对schedule的支持 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>4.1.6.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.quartz-scheduler</groupId>
            <artifactId>quartz-jobs</artifactId>
            <version>2.2.1</version>
        </dependency>
        <dependency>
            <groupId>org.quartz-scheduler</groupId>
            <artifactId>quartz</artifactId>
            <version>2.2.1</version>
        </dependency>
```

## 代码实现

完整实现如下：
```
//定义一个Job，必须实现org.quartz.Job 接口
public class JobTest implements Job {
    @Override
    public void execute(JobExecutionContext context) throws JobExecutionException {
        System.out.println("This is a test!!!" + new Date());
    }

    public static void main(String[] args) {
        try {

            //1.实例化并开始一个调度器Schedule
            // Grab the Scheduler instance from the Factory
            Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
            // and start it off
            scheduler.start();

            //2.添加触发器
            // define the job and tie it to our MyJob class
            JobDetail jobDetail = newJob(JobTest.class).withIdentity("job1", "group1").build();
            // Trigger the job to run now, and then repeat every 40 seconds forever
            Trigger trigger = newTrigger().withIdentity("trigger1", "group1").startNow().withSchedule(simpleSchedule().withIntervalInSeconds(40).repeatForever()).build();
            // Tell quartz to schedule the job using our trigger
            scheduler.scheduleJob(jobDetail, trigger);
        } catch (SchedulerException e) {
            e.printStackTrace();
        }
    }
}
```

## 结果

![这里写图片描述](http://img.blog.csdn.net/20170824235920044?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 参照
[quartz-scheduler](http://www.quartz-scheduler.org/)



