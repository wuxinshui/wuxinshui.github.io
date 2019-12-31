---
layout: post
title: 基础服务系列-安装TensorFlow Serving
category: 基础服务 
tags: [基础服务]
---


>TensorFlow Serving is a flexible, high-performance serving system for machine learning models, designed for production environments. 

## 下载镜像

`docker pull tensorflow/serving`

![](https://oscimg.oschina.net/oscnet/up-fe2954125551d4960d6b856e8c01b86d04a.png)

网络原因，可能会导致timeout，多尝试几次。

## clone代码

`git clone https://github.com/tensorflow/serving`

![](https://oscimg.oschina.net/oscnet/up-0dbdb132d32d809c2e5d184dd15a6b1cef2.png)


## 启动镜像

```
docker run -t --rm -p 8501:8501 \
    -v "/root/tf-serving/serving/tensorflow_serving/servables/tensorflow/testdata/saved_model_half_plus_two_cpu:/models/half_plus_two" \
    -e MODEL_NAME=half_plus_two \
    tensorflow/serving &_model_half_plus_two_cpu:/models/half_plus_two" \
    -e MODEL_NAME=half_plus_two \
    tensorflow/serving &
```

启动后。。。

![](https://oscimg.oschina.net/oscnet/up-914eae1c88965782063c3ddb6c1292ab3b0.png)

## 测试

```
curl -d '{"instances": [1.0, 2.0, 5.0]}' \
    -X POST http://localhost:8501/v1/models/half_plus_two:predict
```
测试成功。。。

![](https://oscimg.oschina.net/oscnet/up-00153fd9f9959dd512d27c18c0eca0c72f4.png)

## 遇到的问题

```
No versions of servable half_plus_two found under base path /models/half_plus_two
```

![](https://oscimg.oschina.net/oscnet/up-76034ecec3db44330af3c8808f4e1fcb459.png)

### 解决方案

创建目录：`/models/half_plus_two`

![](https://oscimg.oschina.net/oscnet/up-8d9f27e53c1bf3984c041ab4c45c0cf0571.png)

