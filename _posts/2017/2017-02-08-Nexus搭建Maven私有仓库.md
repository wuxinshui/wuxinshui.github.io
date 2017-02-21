---
layout: post
title: Nexus搭建Maven私有仓库
category: Maven 
tags: [Maven]
---

此次搭建是在Windows平台上操作的，搭建之前需要先安装JDK。

## 前言

>Nexus Repository Manager is a Java application that requires a Java Runtime Environment. When you run Nexus Repository Manager, you are running a server application with a web-based user interface. The application itself runs with the Eclipse Jetty servlet container and Apache Karaf OSGi-container.

此次搭建是在Windows平台上操作的，搭建之前需要先安装JDK。


## 1.下载
最新版是nexus-3.2.0-01-win64.zip，可以从[官网下载](https://www.sonatype.com/download-oss-sonatype)。

## 2.注册服务

运行以下命令，注册为服务。
`nexus.exe /install Nexus Service`

![这里写图片描述](http://img.blog.csdn.net/20170208150746891?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

配置成功。

![这里写图片描述](http://img.blog.csdn.net/20170208151007548?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 3.启动服务

![这里写图片描述](http://img.blog.csdn.net/20170208151048916?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170208151114792?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


## 4.访问

![这里写图片描述](http://img.blog.csdn.net/20170208151210909?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 5.登录

用户名:admin；
密码：admin123

![这里写图片描述](http://img.blog.csdn.net/20170208152019694?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170208152616813?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

没有Maven相关的jar包。

![这里写图片描述](http://img.blog.csdn.net/20170210100637842?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 6.使用

### 使用maven-public仓库

#### maven配置

![这里写图片描述](http://img.blog.csdn.net/20170208163145982?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`C:\Users\Administrator\.m2\settings.xml`中添加以下配置

```
		<mirror>
			<!--This sends everything else to /public -->
			<id>nexus</id>
			<mirrorOf>central</mirrorOf>
			<url>http://localhost:8081/repository/maven-public/</url>
		</mirror>
```

#### package

在工程目录下，运行命令：`mvn clean package` 

![这里写图片描述](http://img.blog.csdn.net/20170208162653068?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

开始从私服下载jar包，快了很多。

私服中增加的jar包。

![这里写图片描述](http://img.blog.csdn.net/20170208163647323?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### deploy

用IntelliJ IDEA，远程部署jar到私服。

#### 1. `settings.xml`配置用户名和密码

一定要是`C:\Users\用户名\.m2\settings.xml`下面的settings.xml，IDEA在deploy的时候会在这个路径下面查找远程私服的用户名和密码。
```
		<server>  
			<id>snapshots</id>  
			<username>admin</username>  
			<password>admin123</password>  
		</server>
```
#### 2. `pom.xml`文件配置

```
    <distributionManagement>
        <snapshotRepository>
            <id>snapshots</id>
            <url>http://localhost:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
```

#### 3. deploy

运行命令`mvn clean -Dmaven.test.skip=true deploy`

![这里写图片描述](http://img.blog.csdn.net/20170210111055045?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


#### 4. 成功

![这里写图片描述](http://img.blog.csdn.net/20170210105738684?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170210111314373?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 依赖使用

项目使用上传到私服的jar，运行命令`mvn clean package`

![这里写图片描述](http://img.blog.csdn.net/20170210120000208?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


更多命令可以参考[官方文档](https://books.sonatype.com/nexus-book/reference3/install.html#installation-archive)。