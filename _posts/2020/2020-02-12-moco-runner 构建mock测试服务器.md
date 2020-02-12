--
layout: post
title: moco-runner 构建mock测试服务器
category: Mysql 
tags: [Mysql]
---

>Moco is an easy setup stub framework.
 
## 下载

[下载最新的JAR](https://repo1.maven.org/maven2/com/github/dreamhead/moco-runner/0.12.0/)

![](https://oscimg.oschina.net/oscnet/up-4d5fd35048d7ae424b752961600fbf7a3d6.png)

这个下载有些慢，可以网盘获取：

链接:https://pan.baidu.com/s/1PzwGCcrWNwdn6R2Cpr11Xw  密码:t8rk



## 简单运行


![](https://oscimg.oschina.net/oscnet/up-89ab2d53fed5b079c60d41b3c708645c860.png)

`java -jar moco-runner-0.12.0-standalone.jar http -p 9090 -c foo.json`


foo.json
```json
[
  {
    "response": {
      "text": "foo"
    }
  }
]
```

![](https://oscimg.oschina.net/oscnet/up-1905c1c076adb5e74f185c58b9d8b01c41d.png)

## Post 

`JSON`配置文件

```json
[
  {
    "request": {
      "text": "foo"
    },
    "response": {
      "text": "bar"
    }
  },
  {
    "request": {
      "uri": "/json",
      "text": {
        "json": "{\"foo\":\"bar\"}"
      }
    },
    "response": {
      "text": "foo"
    }
  }
]
```

请求一：
```
POST http://localhost:9090/json  
Accept: */*  
Cache-Control: no-cache  
  
{"foo":"bar"}
```

响应一：
```
POST http://localhost:9090/json

HTTP/1.1 200 OK
Content-Length: 3
Content-Type: text/plain; charset=utf-8

foo
```

![](https://oscimg.oschina.net/oscnet/up-1714824ecceb7fea79fcd2dc45fe5c5b3be.png)


请求二：
```
GET http://localhost:9090  
Accept: */*  
Cache-Control: no-cache  
  
foo
```

响应二：
```
GET http://localhost:9090

HTTP/1.1 200 OK
Content-Length: 3
Content-Type: text/plain; charset=utf-8

bar
```

## Bar启动

### 目录结构

![](https://oscimg.oschina.net/oscnet/up-45d31747318685ff75b972cbb5276a456c1.png)

### 启动命令
```
@echo off

java -jar [moco-runner-0.12.0-standalone.jar](moco-runner-0.12.0-standalone.jar) http -p 7890-c moco_data.json

echo server start success! port is 7890
pause
```

### 启动

![](https://oscimg.oschina.net/oscnet/up-822696d10fd82801562b6db4a53333c2e7d.png)



[更多用法参考](https://github.com/dreamhead/moco/blob/master/moco-doc/apis.md#request)