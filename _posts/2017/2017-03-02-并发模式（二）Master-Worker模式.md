---
layout: post
title: 并发模式（二）Master-Worker模式
category: 并发 
tags: [并发]
---
>Master-Worker模式是一种使用多线程进行数据处理的结构。多个Worker进程协作处理用户请求，Master进程负责维护Worker进程，并整合最终处理结果。

## 概念

Master-Worker模式是常用的并行模式之一。系统有两类进程协作工作：Master进程和Worker进程。Master进程负责接收和分配任务，Worker进程负责处理子任务。当所有的Worker进程将子任务完成以后，将结果返回给Master进程，由Master进程做归纳和总结。

## 工作示意图

![](/assets/images/Master-Worker工作示意图.png)

## 模式结构图

Master维护任务队列、Worker进程队列、子任务结果集
![](/assets/images/Master-Worker模式结构图.jpg)

## 代码实现

Master-Worker模式简易实现

`Master`

```java
public class Master{
	//任务队列
	protected Queue<Object> taskQueue=new ConcurrentLinkedQueue<>();
	//子任务结果集
	protected Map<String ,Object>  resultMap=new HashMap<>();
	//worker进程队列
	protected Map<String ,Thread> threadMap=new HashMap<>();

	//判断所有的子任务是否都结束
	public boolean isComeplete(){
		for (Map.Entry<String,Thread> entry:threadMap.entrySet()){
			if (entry.getValue().getState()!= Thread.State.TERMINATED){
				return false;
			}
		}
		return true;
	}

	public Master(Worker worker,int countWorker){
		worker.setWorkQueue(taskQueue);
		worker.setResultMap(resultMap);
		for (int i=0;i<countWorker;i++){
			threadMap.put(Integer.toString(i),new Thread(worker,Integer.toString(i)));
		}
	}

	//提交一个子任务
	public void submit(Object o){
		taskQueue.add(o);
	}

	//返回子任务结果集
	public Map<String ,Object> getResultMap(){
		return resultMap;
	}

	//开始运行所有的worker进程
	public void execute(){
		for (Map.Entry<String,Thread> entry:threadMap.entrySet()){
			entry.getValue().start();
		}
	}
}
```

`Worker`

```java
public class Worker implements Runnable{
	//子任务队列，用于取得任务
	protected Queue<Object> workQueue;

	//子任务结果集
	protected Map<String,Object> resultMap;

	public void setWorkQueue(Queue<Object> workQueue) {
		this.workQueue = workQueue;
	}

	public void setResultMap(Map<String, Object> resultMap) {
		this.resultMap = resultMap;
	}

	//子任务的处理逻辑，在子类中实现具体逻辑
	public Object handle(Object input){
		return input;
	}

	@Override
	public void run() {
		while (true){
			Object input=workQueue.poll();
			if (input==null) break;;
			Object re=handle(input);

			resultMap.put(Integer.toString(input.hashCode()),re);
		}
	}
}
```

## 实战

计算100以内的数字的立方和

`CubePlusWorker`

```java
public class CubePlusWorker extends Worker {
	//子任务具体实现逻辑
	@Override
	public Object handle(Object input) {
		Integer integer=Integer.valueOf(input.toString());
		return integer*integer*integer;
	}
}
```

`Main`

```java
public class Main {
	public static void main(String[] args) {
		long s=System.nanoTime();
		CubePlusWorker cube = new CubePlusWorker();
		Master master = new Master(cube, 10);
		//分解为100个子任务
		for (int i = 1; i <= 100; i++) {
			master.submit(i);
		}
		//执行子任务
		master.execute();
		Integer result = 0;
		System.out.println("每个子任务的执行结果是：");
		for (Map.Entry<String, Object> entry : master.getResultMap().entrySet()) {
			System.out.println(entry.getKey() + " : " + entry.getValue());
			result += Integer.valueOf(entry.getValue().toString());
		}
		System.out.println("开始汇总计算结果：");
		if (result != 0 && master.isComeplete()) {
			System.out.println("The SUM is : " + result);
		}
		System.out.println("Cost time :"+(System.nanoTime()-s)+"ns");
	}
}
```



执行结果：

`开始汇总计算结果：
The SUM is : 17236269
Cost time :3900697ns`

不使用Master-Worker模式的程序执行时间要比这个快很多。



## 实战优化

优化计算代码。不需要等待所有Worker都执行完，即可开始计算最终结果。

```java
public class Main {
	public static void main(String[] args) {
		long s = System.nanoTime();
		Master master=new Master(new CubePlusWorker(),10);
		for(int i=1;i<=100;i++){
			master.submit(i);
		}
		master.execute();
		Map<String,Object> resultMap=master.getResultMap();
		int result=0;
		while (resultMap.size()>0 || !master.isComeplete()){
			Set<String> keys=resultMap.keySet();
			String key=null;
			for (String k:keys){
				key=k;
				break;
			}
			Integer i=null;
			if (key!=null){
				i= (Integer) resultMap.get(key);
			}
			//最终结果
			if (i!=null){
				result+=i;
			}
			//移除已被计算过的
			if (key!=null){
				resultMap.remove(key);
			}
		}
		System.out.println("The SUM is : " + result);
		System.out.println("Cost time :" + (System.nanoTime() - s) + "ns");
	}
}
```


Master-Worker模式是一种将串行任务并行化的方法，被分解的子任务在系统中可以被并行处理。同时，Master进程不需要等待所有子任务都完成，就可以根据已有的部分结果集计算最终结果。