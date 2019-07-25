---
title: 基础服务系列-Centos7 安装Python3.7
date: 2019-07-21 14:25:51
tags: python3
---

>Python 3.7.0 is the newest major release of the Python language, and it contains many new features and optimizations.



## 简介

以[官方centos镜像](https://hub.docker.com/_/centos)为基础，安装[Python3.7.0](https://www.python.org/downloads/release/python-370/)。

## 更换yum源
`curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo 
`

![](https://oscimg.oschina.net/oscnet/cc9689a1b64c6b5eb1d3a9716088648cd96.jpg)

## 安装wget
`yum install wget`

![](https://oscimg.oschina.net/oscnet/119434070d70addc972b005e4817e5b7ac2.jpg)

![](https://oscimg.oschina.net/oscnet/5a9cbb535d2f456b183aa277a4047139e49.jpg)

## 下载

![](https://oscimg.oschina.net/oscnet/f10710910cbb3cd74c675bd81b4418cd804.jpg)

`wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz`

![](https://oscimg.oschina.net/oscnet/e142b91ec25f09460f474185aee572a94f3.jpg)

![](https://oscimg.oschina.net/oscnet/52e823cb94e6f7fc115c9ceafb25fa3c746.jpg)

## 安装

1. 解压

	`tar -xvf Python-3.7.0.tgz`

2. 移动

	`mv Python-3.7.0 /usr/local`
	![](https://oscimg.oschina.net/oscnet/535064ac0ddee72871c88998d9c0ebddef3.jpg)
	

3. 执行

	`./configure`

	![](https://oscimg.oschina.net/oscnet/2ba735ee815c893adc3923f039f16e57739.jpg)
	
	`configure: error: no acceptable C compiler found in $PATH`
	
	`yum install gcc`
	
	![](https://oscimg.oschina.net/oscnet/d599c8e4f47523ff7bf12a4c63112a6b38c.jpg)
	
	`./configure`
	
	![](https://oscimg.oschina.net/oscnet/66348b1a186e6b78c5be4735fab3ae810cb.jpg)

4. 编译

	`yum install make`
	
	![](https://oscimg.oschina.net/oscnet/8a43e2534a763fd115c38845d2a63a38b71.jpg)
	
	`make`
	
	![](https://oscimg.oschina.net/oscnet/56e834c7f8b16ef8303aaf96ac9c7053a17.jpg)
	
	
5. 安装
	
	`make install`
	
	![](https://oscimg.oschina.net/oscnet/de9fa32ad23d0d0f44c95c20ba296b6f030.jpg)
	
	`zipimport.ZipImportError: can't decompress data; zlib not available`
	`yum install zlib* -y`
	
	![](https://oscimg.oschina.net/oscnet/e5c92ebcc44dbf88b0fbac73e80531db81a.jpg)
	
	`ModuleNotFoundError: No module named '_ctypes'`
	
	`yum install libffi-devel -y`
	
	![](https://oscimg.oschina.net/oscnet/7c7e18d784c03e91db0c55033a161a3d939.jpg)
	
	`make install	`
	
	![](https://oscimg.oschina.net/oscnet/0fa343f20f3a9d232080f5d57e4e221fdfc.jpg)
	
6. 建立软连接

	`ln -s /usr/local/Python-3.7.0/python /usr/bin/python3`
	
7. 验证

	![](https://oscimg.oschina.net/oscnet/64e5c21967edf74fe4ffedf61130d486a20.jpg)
	
	
## 保存镜像

![](https://oscimg.oschina.net/oscnet/9cc0ff06412ef533c3685084299903dd8c1.jpg)

![](https://oscimg.oschina.net/oscnet/a9ebf872cf20f2a097ef4af7b659496a5a9.jpg)
## 参考

[CentOS7安装Python3.7](https://blog.csdn.net/six66hao/article/details/80985641)

[Centos7- wget未找到命令，there are no enabled repos 解决办法](https://yq.aliyun.com/articles/655751)

[Install Python PIP on CentOS7](https://linuxhint.com/install-python-pip-centos7/)