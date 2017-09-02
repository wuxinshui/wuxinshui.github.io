---
layout: post
title: Spring Boot集成Quartz-动态任务管理
category: Spring Boot 
tags: [Spring Boot]
---

>  Quartz提供了一组丰富的API，来管理Job。

## 前言

当定时任务越来越多时，集中管理Job越有必要。Quartz提供了一组丰富的API，来管理Job。

[Spring Boot 定时任务之Quartz](http://blog.csdn.net/rickyit/article/details/77543418)中讲了Spring Boot怎么集成quartz，这里结合实际业务，参考网上一些经验，总结一下集成的一些坑。


## 动态任务管理

下面以官方的例子来说明一下这些常用的API。

定义Job实现类

```
public class ColorJob implements Job {

	public ColorJob() {
		// Instances of Job must have a public no-argument constructor.
	}

	public void execute(JobExecutionContext context)
			throws JobExecutionException {

		JobDataMap data = context.getMergedJobDataMap();
		System.out.println("someProp = " + data.getString("someProp"));
	}

}
```

定义`JobDetail `和`Trigger `。

```
// Define job instance
JobDetail job1 = newJob(ColorJob.class)
    .withIdentity("job1", "group1")
    .build();

// Define a Trigger that will fire "now", and not repeat
Trigger trigger1 = newTrigger()
    .withIdentity("trigger1", "group1")
    .startNow()
    .build();
```

### 新建

把Job添加到调度器sched中。

```
// Add the the job to the scheduler's store
sched.add(job1, trigger1);

```

### 更新

```
TriggerKey triggerKey = TriggerKey.triggerKey("job1", "group1");
// store, and set overwrite flag to 'true'  
scheduler.addJob(job1, true);
// tell the scheduler to remove the old trigger with the given key, and put the new one in its place
scheduler.rescheduleJob(triggerKey, trigger1);
```

### 删除

```
JobKey jobKey = JobKey.jobKey("job1", "group1");
scheduler.deleteJob(jobKey);
```

### 暂停

```
JobKey jobKey = JobKey.jobKey("job1", "group1");
scheduler.pauseJob(jobKey);
```

### 恢复

```
JobKey jobKey = JobKey.jobKey("job1", "group1");
scheduler.resumeJob(jobKey);
```

### 立即执行

```
JobKey jobKey = JobKey.jobKey("job1", "group1");
scheduler.triggerJob(jobKey);
```

对Job的动态管理实际就是对调度器`schedule`中的JobDetail做动态操作。增删改逻辑不想其他业务逻辑一样，直接操作数据库。它是先操作`schedule`中的JobDetail，然后根据JobKey或者TriggerKey同步更新数据库的Job。

实际开发中常用的也就是这几种操作，quartz还有其他多种操作。如暂停所有的Job、打断正在执行的Job等。感兴趣的同学可以参考官网API:[org.quartz.core.QuartzScheduler](http://www.quartz-scheduler.org/api/2.2.1/index.html)。

## 加载Job

要实现Job的动态管理，Job必须要持久化到数据库。Spring 容器在启动的时候，从数据库加载所有的Job。

实现接口`CommandLineRunner`。定义初始化操作。

```
   @Override
    public void run(String... strings) throws Exception {
        //查询数据库中的Job
        List<JobInfo> jobInfoList = jobInfoMapper.selectAll();
        List<JobInfoVo> jobInfoVoList = BeanUtils.copyList(jobInfoList, JobInfoVo.class);

        //根据数据库中Job的状态同步scheduler中的状态。
        for (int i = 0; i < jobInfoVoList.size(); i++) {
            JobInfoVo jobVo = jobInfoVoList.get(i);
            Class jobClass = Class.forName(jobVo.getJobClass());

            JobKey jobKey = JobKey.jobKey(jobVo.getJobName(), jobVo.getJobGroup());
            TriggerKey triggerKey = TriggerKey.triggerKey(jobVo.getTriggerName(), jobVo.getTriggerGroup());
            JobDetail job1 = newJob(jobClass)
                    .withIdentity(jobKey)
                    .storeDurably()
                    .build();
            Trigger trigger = newTrigger().withSchedule(CronScheduleBuilder.cronSchedule(jobVo.getCronExpression()))
                    .withIdentity(triggerKey)
                    .build();

            JobStatus jobStatus = JobStatus.valueOf(jobVo.getJobStatus());
            switch (jobStatus) {
                case RUNNING:
                    scheduler.scheduleJob(job1, trigger);
                    break;
                case PAUSE:
                    scheduler.scheduleJob(job1, trigger);
                    scheduler.pauseJob(jobKey);
            }
        }
        //添加Job监听器
        QuartzJobListener quartzJobListener = new QuartzJobListener("quartzListener",jobManagerService);
        scheduler.getListenerManager().addJobListener(quartzJobListener, allJobs());
    }
```

## 坑

下面总结一下，根据项目需要，遇到的一些问题。。

### 坑一：初始化调度器

Spring提供了xml、注解、Java配置、groovy配置实现Bean的创建和注入。通过xml来配置quartz是非常方便的，但是在Spring Boot中不推荐xml文件配置，采用Java配置来实现。

#### 1.使用初始化Job
一开始初始化调度器是参考官网[34.6 Using the Quartz Scheduler](https://docs.spring.io/spring/docs/4.3.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/#scheduling-quartz)来实现Java配置的。

![这里写图片描述](http://img.blog.csdn.net/20170829055053458?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

使用这种方式有个问题：必须创建一个空的Job实现类，来实现Schedule的初始化。

#### 2.SchedulerFactory获取Schedule

```
@Configuration
public class QuartzConfig {

	@Bean(name = "scheduler")
	public Scheduler scheduler(QuartzJobFactory quartzJobFactory) throws Exception {

		SchedulerFactoryBean factoryBean=new SchedulerFactoryBean();
		factoryBean.setJobFactory(quartzJobFactory);
		factoryBean.afterPropertiesSet();
		Scheduler scheduler=factoryBean.getScheduler();
		scheduler.start();
		return scheduler;
	}
}
```

从1->2，是问题的一个解决优化的过程。

### 坑二：Spring Bean不能注入Job的实现类中

`QuartzConfig `中添加以下bean

```
	//解决Job中注入Spring Bean为null的问题
	@Component("quartzJobFactory")
	private class QuartzJobFactory extends AdaptableJobFactory {
		//这个对象Spring会帮我们自动注入进来,也属于Spring技术范畴.
		@Autowired
		private AutowireCapableBeanFactory capableBeanFactory;

		protected Object createJobInstance(TriggerFiredBundle bundle) throws Exception {
			//调用父类的方法
			Object jobInstance = super.createJobInstance(bundle);
			//进行注入,这属于Spring的技术,不清楚的可以查看Spring的API.
			capableBeanFactory.autowireBean(jobInstance);
			return jobInstance;
		}
	}
```

Job的实现类中添加以下代码：

```
	protected void springBeanAutowiringSupport() {
		SpringBeanAutowiringSupport.processInjectionBasedOnCurrentContext(this);
	}
```

### 坑三：保存Job执行记录

保存执行记录也有2种设计方案。

#### 方案1：每个Job执行时保存

Job在执行时，调用业务bean中的方法，保存执行记录。

![这里写图片描述](http://img.blog.csdn.net/20170902114636929?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 方案2：添加监听器JobListenerSupport

`Quartz`提供的监听器有`JobListener`和`TriggerListener`，这里我们使用`JobListener`，该监听器接口有三个方法：

![这里写图片描述](http://img.blog.csdn.net/20170902115539479?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

实现接口`JobListener`，定义监听业务。

![这里写图片描述](http://img.blog.csdn.net/20170902120140915?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

添加监听器到调度器`schedule`中。

`        scheduler.getListenerManager().addJobListener(quartzJobListener, allJobs());
`

为了减少对Job的侵入性，建议在监听器中做业务逻辑的相关操作。Job只负责执行即可，不负责业务逻辑处理。

### 坑四：Job监听器中注入业务Bean

坑二是在Job中注入业务bean，发现在JobListenerSupport中注入Bean也是为null。之前的填坑方法不管用了。。。

可以通过构造方法来注入。

![这里写图片描述](http://img.blog.csdn.net/20170902121432505?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`        QuartzJobListener quartzJobListener = new QuartzJobListener("quartzListener",jobManagerService);
        scheduler.getListenerManager().addJobListener(quartzJobListener, allJobs());`

### 坑五：Job并发

quartz中的Job并发有2种情况
1. 间隔时间小于执行时间，上一个任务还未完成，开始执行新的任务；
2. 同一个Job，有相同的触发时间。

第二种情况设计上可以避免，cron定时表达式，能满足大部分情况下的使用，完全没有必要创建2个触发时间相同的Job。

第一种情况下的并发，可以使用注解`@DisallowConcurrentExecution`

![这里写图片描述](http://img.blog.csdn.net/20170902130628448?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

加上注解以后，如果第一次执行未完成，第二次会等待直到第一次完成。


## 参考

[ Spring Boot配置方式](http://blog.csdn.net/webzhuce/article/details/54564019)
[34.6 Using the Quartz Scheduler](https://docs.spring.io/spring/docs/4.3.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/#scheduling-quartz)

参考的东西太多了，都是一些优秀的博客。。。

```
1.校验定时表达式

http://blog.csdn.net/lisheng19870305/article/details/39553925
http://blog.csdn.net/ukulelepku/article/details/54310035
https://stackoverflow.com/questions/2362985/verifying-a-cron-expression-is-valid-in-java
https://gist.github.com/andrew-templeton/ae4126a8efe219b796a3
https://codereview.stackexchange.com/questions/63363/cron-expression-validator-for-apache-quartz

2.Job中注入Spring Bean为Null

http://blog.csdn.net/jackylovesjava/article/details/50044271
http://blog.csdn.net/colorandsong/article/details/40392117
http://bbs.csdn.net/topics/392182530
http://www.cnblogs.com/feiqihang/p/5358100.html
http://blog.csdn.net/colorandsong/article/details/40392117
http://www.cnblogs.com/feiqihang/p/5358100.html
http://blog.csdn.net/educast/article/details/73331398
http://blog.csdn.net/gui66497/article/details/53589476
https://zhidao.baidu.com/question/984255639590451619.html
http://www.cnblogs.com/daxin/p/3608320.html
https://www.zhihu.com/question/26850120

3.Quartz日志处理

https://stackoverflow.com/questions/22573824/quartz-schedular-triggers-histoy-need-save-in-data-base-after-firing
https://stackoverflow.com/questions/22479622/how-to-maintain-the-quartz-triggers-history-in-data-base-after-firing-in-java
https://www.quartz-scheduler.net/documentation/faq.html
https://github.com/Flipkart/quartz/blob/master/quartz-plugins/src/main/java/org/quartz/plugins/history/LoggingTriggerHistoryPlugin.java

4.quartz动态任务管理

http://blog.csdn.net/u012907049/article/details/73801122
https://help.aliyun.com/document_detail/43140.html?spm=5176.doc43132.6.696.srgKu4

5.Quartz官网实践

http://www.quartz-scheduler.org/documentation/best-practices.html#listeners-(triggerlistener-joblistener-schedulerlistener


http://www.quartz-scheduler.org/documentation/quartz-2.2.x/cookbook/JobListeners.html

6.quartz任务并发

http://blog.csdn.net/tiantangpw/article/details/41120137
https://segmentfault.com/a/1190000009128328
https://my.oschina.net/blueskyer/blog/325812

7.暂停是否影响当前正在运行的Job。删除？

https://stackoverflow.com/questions/27823159/difference-between-pausejob-and-pausetrigger-in-quartz-scheduler
http://forums.terracotta.org/forums/posts/list/4537.page
https://stackoverflow.com/questions/16173785/quartz-fails-to-delete-a-job-now-what

```