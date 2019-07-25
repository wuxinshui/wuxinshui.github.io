---
layout: post
title: Spring Boot集成CKFinder
category: Spring Boot 
tags: [Spring Boot]
---

>Spring Boot集成CKFinder,实现浏览功能。

## 前言

上一篇记录了[Spring Boot集成CKEditor](http://blog.csdn.net/rickyit/article/details/68068093),这里记录Spring Boot集成CKFinder实现浏览功能，详细的配置可以参考[ckeditor和ckfinder集成详细配置及其优化](http://blog.csdn.net/crown_0726/article/details/45127555)，这里讲在Spring Boot中怎么玩CKFinder。[ckeditor中“浏览服务器”的后台操作](http://www.mamicode.com/info-detail-1172256.html)是自己写代码来实现浏览，界面操作不太友好。CKFinder的浏览界面不错，而且还能定制不同的样式。

![这里写图片描述](http://img.blog.csdn.net/20170412110615985?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 集成

### 1.下载
[下载](https://cksource.com/ckfinder/download)Java 版本的CKFinder，支持Java的最新版本是2.6.2.1，3.x的CKFinder没有提供Java实现。下载后是压缩文件：【ckfinder_java_2.6.2.1.zip】


### 2.解压
#### 1.ZIP解压

ZIP解压后的目录：

![这里写图片描述](http://img.blog.csdn.net/20170412171816423?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`_source`:源代码；
`CKFinderJava-2.6.2.1.war`:war包，可以直接在Tomcat下面运行。

#### 2.War包解压
解压War包：

![这里写图片描述](http://img.blog.csdn.net/20170412172012754?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

直接把ckfinder文件夹拷贝到static目录下面，和集成CKEditor一样。

![这里写图片描述](http://img.blog.csdn.net/20170330185144648?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 3.配置

#### 1.资源路径配置

`WebMvcConfig`中添加配置，把CKFinder添加到Spring Boot的资源路径中，定义访问路径，便于访问。

`registry.addResourceHandler("/ckfinder/**").addResourceLocations("classpath:/static/ckfinder/");`

#### 2.Maven依赖配置

`CKFinderJava-2.6.2.1\WEB-INF\lib`下是用到的jar，`pom`中配置主要的jar包。

```
        <!--CKFinder start-->
        <dependency>
            <groupId>com.ckfinder</groupId>
            <artifactId>CKFinder</artifactId>
            <version>2.6.2.1</version>
        </dependency>
        <dependency>
            <groupId>net.coobird</groupId>
            <artifactId>thumbnailator</artifactId>
            <version>0.4.8</version>
        </dependency>
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.2.2</version>
        </dependency>
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.0.1</version>
        </dependency>
        <!--CKFinder end-->
```

这是CKFinder的简单集成，下面来说说具体怎么玩。

## 功能实现

CKFinder集成以后，浏览功能还不能使用。

### 1.配置路径

在CKEditor的配置文件`config.js`中配置访问路径。参考[官网给出的例子](http://docs.cksource.com/CKFinder_2.x/Developers_Guide/Java/CKEditor_Integration)

`    config.filebrowserImageBrowseUrl= '/ckfinder/ckfinder.html?type=Images'`

不配置`filebrowserImageBrowseUrl`，下面的【浏览服务器】按钮是没有的。

![这里写图片描述](http://img.blog.csdn.net/20170412175301893?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

点击浏览服务器：

![这里写图片描述](http://img.blog.csdn.net/20170412175644989?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`/ckfinder/core/connector/java/connector.java`不可用，找不到。。。

这个路径是CKFinder内置的，目前没找到怎么修改这个路径。不能修改那就实现这个路径。

### 2.内置URL实现

查看`web.xml`文件，看看这个路径是怎么定义的。

![这里写图片描述](http://img.blog.csdn.net/20170412180149106?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

可以看到
`/ckfinder/core/connector/java/connector.java`
是由Servlet`com.ckfinder.connector.ConnectorServlet`来处理的。

这就简单了，写个Servlet来继承它。

![这里写图片描述](http://img.blog.csdn.net/20170412181119089?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`WebCkApplication`中添加扫描Servlet的注解

![这里写图片描述](http://img.blog.csdn.net/20170412181212402?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

URL解决了，试试按钮能不能用。

![这里写图片描述](http://img.blog.csdn.net/20170412181349184?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

恩，还是报错`从服务器读取xml数据出错`，看看log怎么说。

![这里写图片描述](http://img.blog.csdn.net/20170412181728081?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`Configuration`不能正确初始化，在初始化读取xml文件的时候报错。。。

### 3.加载XML文件

#### 1.config.xml文件

从`web.xml`中看到，还需要加载`config.xml`文件。`config.xml`在`\WEB-INF`目录下，复制到classpath下，这里重命名为`ckfinder.xml`。

![这里写图片描述](http://img.blog.csdn.net/20170414161006063?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

启用CKFinder，`enabled`修改为True，默认是false，浏览功能是不能用的。

![这里写图片描述](http://img.blog.csdn.net/20170414162037172?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

配置`baseDir`，如果没配置`baseDir`，点击浏览服务器按钮的时候会在临时目录创建以下目录：

![这里写图片描述](http://img.blog.csdn.net/20170414161646605?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

配置的话，在配置路径下也是有同样的目录结构的。和CKEditor上传的路径一致，这样上传的文件也可以直接浏览到。

#### 2.classpath加载Xml文件

参考[springboto集成ckfinder](https://my.oschina.net/zwolfking/blog/824753)，重写Configuration，从classpath下加载Xml文件。

`DefaultResourceLoader loader = new DefaultResourceLoader();`
`Resource resource = loader.getResource(this.xmlFilePath);`

Servlet中加载配置

```
@WebServlet(urlPatterns = "/ckfinder/core/connector/java/connector.java", initParams = {
		@WebInitParam(name = "XMLConfig", value = "classpath:ckfinder.xml"),
		@WebInitParam(name = "debug", value = "false"),
		@WebInitParam(name = "configuration", value = "com.wxs.ckeditor.config.CKFinderConfig")
})
public class ImageBrowseServlet extends ConnectorServlet {
}
```

到这里，Spring Boot集成CKFinder就完美实现了。

## 优化
使用的话就很简单了，这里不再说了。写一下后期的一些优化，是代码更简洁，维护更方便。

### 1.CKEditor、CKFinder使用同一个存储路径

`ckfinder.xml`中配置以下路径

```
<baseDir>F:\\data\\file\\image\\</baseDir>
<baseURL>http://localhost:8180/public/image/</baseURL>
```

`http://localhost:8180/public/image/`是在`WebMvcConfig`中定义的

![这里写图片描述](http://img.blog.csdn.net/20170414163810165?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

CKFinder返回的URL中会自带`images`

![这里写图片描述](http://img.blog.csdn.net/20170414163929697?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

所以CKEditor用的存储路径和访问路径改为以下：

```
#磁盘存储路径
ckeditor.storage.image.path=F:\\data\\file\\image\\images\\
#访问路径
ckeditor.access.image.url=http://localhost:8180/public/image/images/
```

这样就更好了，CKEditor、CKFinder使用同一个存储路径。


后续会在下个博文里对集成CKFinder做一下详细的优化。

## 参考
[CKEditor与CKFinder学习--整合SpringMVC](http://blog.csdn.net/frankcheng5143/article/details/50907013)

[ckeditor和ckfinder集成详细配置及其优化](http://blog.csdn.net/crown_0726/article/details/45127555)

 [源码](https://github.com/wuxinshui/spring-boot-samples/tree/master/spring-boot-sample-ckeditor)