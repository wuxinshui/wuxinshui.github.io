---
layout: post
title: Spring Boot集成CKFinder-优化
category: Spring Boot 
tags: [Spring Boot]
---

>Spring Boot集成CKFinder-优化，对一些配置做一下优化，使配置更加灵活。

## 优化一：ckfinder.xml
`ckfinder.xml`文件中，`baseDir`、`baseURL`需要配置固定路径。把这2个配置放到`application.properties`中。这样就灵活多了。

### application.properties

```
#磁盘存储路径
ckeditor.storage.image.path=D:\\data\\file\\image\\
#访问路径
ckeditor.access.image.url=http://localhost:8180
```


### Servlet注册

新建`CKFinderServletConfig`，代替原来的`ImageBrowseServlet`。Spring Boot支持Servlet的注入，参考[示例](http://www.cnblogs.com/java-zhao/p/5775103.html)。

```java
@Configuration
public class CKFinderServletConfig {

    @Value("${ckeditor.storage.image.path}")
    private String baseDir;
    @Value("${ckeditor.access.image.url}")
    private String baseURL;

    @Bean
    public ServletRegistrationBean connectCKFinder(){
        ServletRegistrationBean registrationBean=new ServletRegistrationBean(new ConnectorServlet(),"/ckfinder/core/connector/java/connector.java");
        registrationBean.addInitParameter("XMLConfig","classpath:/static/ckfinder.xml");
        registrationBean.addInitParameter("debug","false");
        registrationBean.addInitParameter("configuration","com.wxs.ckeditor.config.CKFinderConfig");
        //初始化ckfinder.xml 配置
        registrationBean.addInitParameter("baseDir",baseDir);
        registrationBean.addInitParameter("baseURL",baseURL);
        return registrationBean;
    }

}
```

### CKFinderConfig

`CKFinderConfig`修改对`baseDir`、`baseURL`的处理。

![这里写图片描述](http://img.blog.csdn.net/20170423124115166?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`/public/image/`是在`WebMvcConfig`中配置的访问URL。


### FilesController

上传`FilesController`中也得做一下修改。

![这里写图片描述](http://img.blog.csdn.net/20170423124348435?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

`/images/`这一层目录，是在点击`浏览服务器`时候CKFinder创建的。


## 优化二：ImageBrowseServlet

`ImageBrowseServlet`上文已经做了优化，Servlet用注入的方式更灵活，可以很方便的初始化一些参数。参考类`CKFinderServletConfig`。


