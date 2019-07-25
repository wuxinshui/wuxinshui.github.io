---
layout: post
title: Intellij IDEA-SSH executable-Native
category: Intellij IDEA 
tags: [Intellij IDEA]
---

>Connecting to gitlab using PuTTY generated SSH key in IDEA

## 背景

项目开发中，使用Gitlab搭建git服务，做代码的版本管理，一开始是使用https的请求方式，后来运维的同事整合了一下服务，https的请求方式不行了，只能用ssh的方法获取代码。https的请求方式，IDEA配置一下git的安装路径即可。切换成SSH就不行了。

![这里写图片描述](http://img.blog.csdn.net/20171101214133120?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20171101214315057?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

网上也找了很多，大部分是关于设置的：`SSH executable:Native`。

这里记录一下详细的配置。

实战环境：

-**System**：Windows7 Professional Service Pack 1
-**IDEA**:IntelliJ IDEA 2016.3.4

## 解决方案

### 安装PuTTY

[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)是一个Telnet、SSH、rlogin、纯TCP以及串行接口连接软件。[PuTTY Download Page](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 



安装后的目录如下：

![这里写图片描述](http://img.blog.csdn.net/20171101215232141?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- **PuTTY:** [putty.exe](http://the.earth.li/~sgtatham/putty/latest/x86/putty.exe) (or by [FTP](ftp://ftp.chiark.greenend.org.uk/users/sgtatham/putty-latest/x86/putty.exe))

  The SSH and Telnet client itself.

- **Plink:** [plink.exe](http://the.earth.li/~sgtatham/putty/latest/x86/plink.exe) (or by [FTP](ftp://ftp.chiark.greenend.org.uk/users/sgtatham/putty-latest/x86/plink.exe))

  A command-line interface to the PuTTY back ends.

- **Pageant:** [pageant.exe](http://the.earth.li/~sgtatham/putty/latest/x86/pageant.exe) (or by [FTP](ftp://ftp.chiark.greenend.org.uk/users/sgtatham/putty-latest/x86/pageant.exe))

  An SSH authentication agent for PuTTY, PSCP, PSFTP, and Plink.

- **PuTTYgen:** [puttygen.exe](http://the.earth.li/~sgtatham/putty/latest/x86/puttygen.exe) (or by [FTP](ftp://ftp.chiark.greenend.org.uk/users/sgtatham/putty-latest/x86/puttygen.exe))

  An RSA and DSA key generation utility.

### 产生公钥私钥

![这里写图片描述](http://img.blog.csdn.net/20171101215905362?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


![这里写图片描述](http://img.blog.csdn.net/20171101220102261?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 保存密钥

保存生成的私钥到用户目录下面的`.ssh`目录下。已经有的密钥放到这个目录下面也行。


![这里写图片描述](http://img.blog.csdn.net/20171101220238649?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 配置环境变量

- **GIT_HOME:** `C:\Program Files\Git`
- **GIT_SSH:** `C:\Program Files (x86)\PuTTY\plink.exe`
- **Path:** `%Path%;%GIT_HOME%\bin`

环境变量配置以后，需要**重启**电脑。

### 启动pageant

启动pageant，添加刚刚保存的ppk文件。

![这里写图片描述](http://img.blog.csdn.net/20171101221127255?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 设置Native

接下来修改一下IDEA的配置:`SSH executable-Native`

尝试从git服务器获取代码。。。

![这里写图片描述](http://img.blog.csdn.net/20171101221425688?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

OK。。。

## 坑

### 坑一 Git不需要重新安装


![这里写图片描述](http://img.blog.csdn.net/20171101221652928?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

参考的教程里说需要重新安装Git，并设置为`Use(Tortoise)Plink`。本实战是默认安装为`Use OpenSSH`，也没有重新安装设置SSH executable。

### 坑二 加载正确的PPK

本实战加载的是自定义目录下面的私钥文件`ssh.ppk`,可能部分环境需要加载用户目录`.ssh`下的ppk文件。

### 坑三 重启电脑

环境变量配置好以后一定要重启电脑。

## 参考

[Connecting to github using PuTTY generated SSH key in Windows 7](https://superuser.com/questions/378354/connecting-to-github-using-putty-generated-ssh-key-in-windows-7)