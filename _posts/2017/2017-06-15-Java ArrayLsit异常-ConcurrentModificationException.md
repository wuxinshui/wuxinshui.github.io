---
layout: post
title: Java ArrayLsit异常-ConcurrentModificationException
category: 编程 
tags: [编程]
---
>在操作List集合的时候，习惯用for each循环操作。这次项目中根据业务逻辑需要删除符合条件的元素，元素删除后，继续next操作，抛出了`ConcurrentModificationException`异常。

## 前言

在操作List集合的时候，习惯用for each循环操作。这次项目中根据业务逻辑需要删除符合条件的元素，元素删除后，继续next操作，抛出了`ConcurrentModificationException`异常。下面，重现异常，看看异常是怎么发生的，怎么避免。

## 测试代码

```
public class ConcurrentModificationExceptionList {	
	public static void main(String[] args) {
		List<Integer> list1 = new ArrayList<>();
		list1.add(1);
		list1.add(2);
		list1.add(3);
		list1.add(4);
		for (Integer integer : list1) {
			if (integer == 1) {
				list1.remove(integer);
			}
		}
	}
}
```


## 异常的发生

`ConcurrentModificationException`异常是在这里抛出的。当`modCount != expectedModCount`为true的时候抛出。

![这里写图片描述](http://img.blog.csdn.net/20170615111507305?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


![这里写图片描述](http://img.blog.csdn.net/20170615111221396?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 原因	

上述异常为什么会发生，来看一下源码中的删除动作。

![这里写图片描述](http://img.blog.csdn.net/20170615111633947?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170615111659915?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


在执行删除动作前`modCount`自加1。在下个元素做checkForComodification的时候异常就抛出了。

![这里写图片描述](http://img.blog.csdn.net/20170615111726730?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 异常的解决

![这里写图片描述](http://img.blog.csdn.net/20170615143158795?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

查看源码，`modCount`是在ArrayList的父类AbstractList中定义的，`modCount`记录list被修改的次数。在iterator和实现iterator的list中，进行next(),remove()、previous、set、add操作时，`modCount`的值被意外改变，将抛出异常`ConcurrentModificationException`。关于异常的解决，网上也有很多的方法，参考文末。


既然异常是在iterator和实现iterator的list中发生的，那不使用for each操作，采用for in操作就能避免异常的发生。

代码验证一下

```
		for (int i = 0; i < list1.size(); i++) {
			if (list1.get(i)==1){
				list1.remove(i);
				i--;//指向删除前的上一个元素
			}
		}
```

![这里写图片描述](http://img.blog.csdn.net/20170615150727246?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



看一下源码：

![这里写图片描述](http://img.blog.csdn.net/20170615150718668?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

源码中是没有做`checkForComodification`检查的，也不会发生异常。

## 参考

[Java ConcurrentModificationException异常原因和解决方法](http://www.cnblogs.com/dolphin0520/p/3933551.html)
[集合迭代时对集合进行修改抛ConcurrentModificationException原因的深究以及解决方案](http://blog.csdn.net/izard999/article/details/6708738)
[Java ConcurrentModificationException 异常分析与解决方案](http://www.2cto.com/kf/201403/286536.html)