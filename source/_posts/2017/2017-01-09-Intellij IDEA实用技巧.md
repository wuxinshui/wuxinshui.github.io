---
layout: post
title: Intellij IDEA实用技巧
category: Intellij IDEA 
tags: [Intellij IDEA]
---

>IDEA 全称 IntelliJ IDEA，是java语言开发的集成环境，IntelliJ在业界被公认为最好的java开发工具之一

本文总结了一些实用的技巧。

## 1.隐藏指定文件
iml文件是项目文件，IDEA会自动生成*.iml文件。

![这里写图片描述](https://img.blog.csdn.net/20170109145619202?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](https://img.blog.csdn.net/20170109134531045?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这种文件实际项目中是用不到的，会出现在Version Control的文件列表里。

![这里写图片描述](https://img.blog.csdn.net/20170109145244794?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 解决方法

将要隐藏的文件名后缀添加到`Ignore files and folders`

![这里写图片描述](https://img.blog.csdn.net/20170109145847671?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`iml`文件隐藏了。

![这里写图片描述](https://img.blog.csdn.net/20170109145834628?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


按照这种方法还能隐藏`.idea`文件夹。

## 2.提交代码到版本服务器

直接从IDEA中提交项目到远程仓库，这里以CSDN的远程仓库为例。

### 1.create git repository

![这里写图片描述](https://img.blog.csdn.net/20170113101820963?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 2.选择要提交的项目

![这里写图片描述](https://img.blog.csdn.net/20170113102110314?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 3.git  创建成功
![这里写图片描述](https://img.blog.csdn.net/20170113102318340?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 3.在远程仓库创建项目

![这里写图片描述](https://img.blog.csdn.net/20170113102730933?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 4.IDEA设置远程仓库

![这里写图片描述](https://img.blog.csdn.net/20170113103229986?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

URL中填写复制的远程仓库地址：

`https://code.csdn.net/RickyIT/test-web.git`

![这里写图片描述](https://img.blog.csdn.net/20170113103243612?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](https://img.blog.csdn.net/20170113103506849?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 5.先更新再提交
在远程仓库新建的项目有多余的文件README.md，需要先更新一下，才能提交到远程仓库。否则push的时候会报错。
`10:37	Push rejected: F:\Learn\test-web: push to origin/master was rejected`

1. Update

![这里写图片描述](https://img.blog.csdn.net/20170113104212093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
2. Add
3. Commit Directory

### 6.push

![这里写图片描述](https://img.blog.csdn.net/20170113104328694?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

操作日志

```
10:14	Push successful: D:\Git\spring-jpa-web: pushed 2 commits to origin/master

10:35	3 files committed: test-web demo

10:37	Push rejected: F:\Learn\test-web: push to origin/master was rejected

10:46	Push successful: F:\Learn\test-web: pushed 2 commits to origin/master
```

## 注释行自动缩进

在IDEA中添加注释行的话，是不会缩进的，看起来不整齐，(⊙o⊙)…，强迫症了。

![这里写图片描述](https://img.blog.csdn.net/20170624130221209?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

参考[intellij idea 注释行如何自动缩进?](https://www.zhihu.com/question/35486841)

![这里写图片描述](https://img.blog.csdn.net/20170624130419078?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`Line comment at first column
Block comment at first column`复选框去掉就OK了。


实操下来，这个变更需要重启IDEA。

![这里写图片描述](https://img.blog.csdn.net/20170624130522792?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](https://img.blog.csdn.net/20170624130611558?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`Ctrl+/`

![这里写图片描述](https://img.blog.csdn.net/20170624131213246?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


新的技巧持续更新中。。。
