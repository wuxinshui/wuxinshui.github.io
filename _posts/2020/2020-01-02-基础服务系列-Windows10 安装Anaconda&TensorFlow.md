---
layout: post
title: 基础服务系列-Windows10 安装Anaconda&TensorFlow
category: 基础服务 
tags: [基础服务]
---

>The open-source Anaconda Distribution is the easiest way to perform Python/R data science and machine learning on Linux, Windows, and Mac OS X. 

## 下载

[官网下载](https://www.anaconda.com/distribution/)

![](https://oscimg.oschina.net/oscnet/up-166ee733611058092521ea4e33bebfca67c.png)


## 安装

双击默认安装

![](https://oscimg.oschina.net/oscnet/up-44cbd6272ce3f2c0868129f6ec3b8b37253.png)

![](https://oscimg.oschina.net/oscnet/up-1197ff0f7a0d5757d7288c653d56eabd0df.png)



## 验证

![](https://oscimg.oschina.net/oscnet/up-bcee5bbf647c9cf1b6ddf8602e12106408e.png)

`conda info`

![](https://oscimg.oschina.net/oscnet/up-203159c029a15450191ba4d6074c38d1934.png)

## 创建Python Env

`conda create -n tfenv`

![](https://oscimg.oschina.net/oscnet/up-e9b3aa99489e10ed2095ad52281e6384795.png)

`activate tfenv`

![](https://oscimg.oschina.net/oscnet/up-e8295450c25345fad6f21a65006fe26f875.png)

## Install Tensorflow

` conda install tensorflow`

![](https://oscimg.oschina.net/oscnet/up-ffab4fea0027ac499866c699500f06472f0.png)

![](https://oscimg.oschina.net/oscnet/up-2c5c819867af42661380ce51259f3a228f3.png)

## Verifying the Installation

`import tensorflow as tf`

![](https://oscimg.oschina.net/oscnet/up-ead833e577077de591f0e27a842a4a6ae09.png)

没有错误，安装正常。

## 安装数据科学三剑客

- pandas
- numpy
- matplotlib

### Install pandas

Pandas是一个开源的，BSD许可的库，为Python编程语言提供高性能，易于使用的数据结构和数据分析工具。

`conda install pandas`

![](https://oscimg.oschina.net/oscnet/up-b2ec8859d5c31a6c3e651f5c183d1d8959b.png)

![](https://oscimg.oschina.net/oscnet/up-cf9a83b29ebe13341ef0ce8d9c28ce6adfe.png)

### Verifying the Installation

`import pandas as pd`

![](https://oscimg.oschina.net/oscnet/up-f6451e036d36161c50a30dc40e153808cfc.png)


### Install numpy

NumPy是Python中的一个运算速度非常快的一个数学库，它非常重视数组。

它允许你在Python中进行向量和矩阵计算，并且由于许多底层函数实际上是用C编写的，因此你可以体验在原生Python中永远无法体验到的速度。

`conda install numpy`

![](https://oscimg.oschina.net/oscnet/up-f657fbbe564c1cb980d5e6232cef17d74c4.png)

### Verifying the Installation

`import numpy as np`

![](https://oscimg.oschina.net/oscnet/up-986cef1fa1a15976a8ba8fb1bdfd8415973.png)



### Install matplotlib

Matplotlib是一个Python 2D绘图库，可以生成各种硬拷贝格式和跨平台交互式环境的出版物质量数据。

`conda install matplotlib`

![](https://oscimg.oschina.net/oscnet/up-86d1b70fab0de92bd0be6aca37d5b5c8400.png)

### Verifying the Installation

`import matplotlib as mlb`

![](https://oscimg.oschina.net/oscnet/up-35c65cf989998ef22848f01fb97467dcbfe.png)


### Install scikit-learn 

scikit-learn 是基于 Python 语言的机器学习工具。

- 简单高效的数据挖掘和数据分析工具
- 可供大家在各种环境中重复使用
- 建立在 NumPy ，SciPy 和 matplotlib 上
- 开源，可商业使用 - BSD许可证

`conda install -c anaconda scikit-learn`

![](https://oscimg.oschina.net/oscnet/up-7b049bf1a25081c86bef8f69261db9fce1e.png)

### Verifying the Installation

`import sklearn as sn`

![](https://oscimg.oschina.net/oscnet/up-bcb1316f93bb5461e3dbc52cef7b1ae226b.png)


## 参考

[Installation with Anaconda](https://stackabuse.com/installing-tensorflow-on-windows/)



