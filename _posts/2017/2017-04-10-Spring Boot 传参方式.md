---
layout: post
title: Spring Boot 传参方式
category: 编程 
tags: [编程]
---
>第一段必须是描述，作为摘要显示



最近在搞Spring Boot的项目，把传参方式总结一下。网上也参考一些文章，总结的很不错，这里借鉴一下。

## 注解

### @RequestParam

这个注解用来绑定单个请求数据，既可以是url中的参数，也可以是表单提交的参数和上传的文件。

Form 表单

![这里写图片描述](http://img.blog.csdn.net/20170419164616408?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


URL中传参

![这里写图片描述](http://img.blog.csdn.net/20170419165104118?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

不能处理JSON格式的请求。

![这里写图片描述](http://img.blog.csdn.net/20170419164940053?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### @PathVariable

这个注解可以将URL中的占位符参数绑定到控制器处理方法的入参。

![这里写图片描述](http://img.blog.csdn.net/20170419165317326?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

以上2种注解的使用方式参考[Spring Boot Web项目之参数绑定](http://www.cnblogs.com/nicekk/p/6072130.html)，讲解的很详细，大部分情况都考虑到了。

### @RequestBody
这个注解是传JSON对象用的，参考[ @RequestBody的正确使用方法](http://blog.csdn.net/li954644351/article/details/51160229)。不能通过Form表单、URL传参。

![这里写图片描述](http://img.blog.csdn.net/20170419174259147?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## Form-data
 传送form表单数据，可以不用注解，直接传参，参数名字要一样。

![这里写图片描述](http://img.blog.csdn.net/20170419162649006?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这种传参方式不能处理JSON参数请求。

![这里写图片描述](http://img.blog.csdn.net/20170419164335140?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)