---
layout: post
title: Java非法字符 'ufeff'
category: 编程 
tags: [编程]
---
>utf-8+bom比utf-8多了三个字节前缀：0xEF0xBB0xBF，有这三个字节前缀的文本或字符串，程序可以自动判断它为utf-8格式，并按照utf-8格式来解析文本或字符串。

## 前言

开发过程中，在启动Spring Boot的时候，遇到这样的问题：

`Error:(1, 1) java: 非法字符: '\ufeff'`

运行`mvn compile`也是报同样的错误。感觉好奇怪啊，仔细看看对应的行没啥问题啊。我用的工具是`IntelliJ IDEA 2016.3(64)`，同样的代码在Eclipse中是没问题的，Spring Boot正常启动。

## 问题

在启动服务的时候报错

![这里写图片描述](http://img.blog.csdn.net/20170719111642357?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 解决方案

下载个文本工具，我用的是`Notepad++`.打开刚刚出问题的文件看右下角：

![这里写图片描述](http://img.blog.csdn.net/20170719112024504?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

提示当前编码格式是`UTF-8-BOM`。菜单栏中也可以看。

![这里写图片描述](http://img.blog.csdn.net/20170719114144193?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

把当前的内容全部剪切，在菜单栏切换编码格式。

![这里写图片描述](http://img.blog.csdn.net/20170719111936327?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

格式切换完后，把内容粘贴一下就OK。

![这里写图片描述](http://img.blog.csdn.net/20170719114055027?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


## 扩展

> UTF-8 不需要 BOM，尽管 Unicode 标准允许在 UTF-8 中使用 BOM。
所以不含 BOM 的 UTF-8 才是标准形式，在 UTF-8 文件中放置 BOM 主要是微软的习惯（顺便提一下：把带有 BOM 的小端序 UTF-16 称作「Unicode」而又不详细说明，这也是微软的习惯）。
BOM（byte order mark）是为 UTF-16 和 UTF-32 准备的，用于标记字节序（byte order）。微软在 UTF-8 中使用 BOM 是因为这样可以把 UTF-8 和 ASCII 等编码明确区分开，但这样的文件在 Windows 之外的操作系统里会带来问题。

## 参考

[UTF8最好不要带BOM，附许多经典评论](http://www.cnblogs.com/findumars/p/3620078.html)

[「带 BOM 的 UTF-8」和「无 BOM 的 UTF-8」有什么区别？网页代码一般使用哪个？](https://www.zhihu.com/question/20167122)