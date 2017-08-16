
>ArrayList是非线程安全的。

## 问题描述

开发中，存在这样的业务逻辑，类似倒金字塔结构，下层数据需要基于上层的数据进行逻辑计算。设计思路是：定义一个全局变量upLayerList，来保存上一层的数据。每一层计算仅需要知道upLayerList就可以，不用关心上一层数据怎么获取。当前层计算完毕后，把结果赋值给upLayerList，留给下一层使用。

## 问题重现

模拟业务逻辑实现：

![这里写图片描述](http://img.blog.csdn.net/20170726133825565?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

实现多线程：

![这里写图片描述](http://img.blog.csdn.net/20170726133853413?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

正常的输出：

`Runner1 : [1, 2, 3]
Runner2 : [4, 5, 6]`

实际输出：

![这里写图片描述](http://img.blog.csdn.net/20170726112746999?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

线程1和线程2修改了彼此的list。

## 解决方案1

使用同步关键字`synchronized`

![这里写图片描述](http://img.blog.csdn.net/20170726113023658?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

线程1和线程2交叉运行输出：

![这里写图片描述](http://img.blog.csdn.net/20170726113207188?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 解决方案2

传值，每一层都传upLayerList。

![这里写图片描述](http://img.blog.csdn.net/20170726115224003?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

实际测试结果：

![这里写图片描述](http://img.blog.csdn.net/20170726115249755?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 其他解决方案

定义变量：`uplayerList = Collections.synchronizedList(new ArrayList<>());`,uplayerList 是线程安全的，但是后面对uplayerList 的操作不是线程安全的。需要对操作加同步关键字`synchronized`。


## 扩展

[ArrayList和Vector以及synchronizedList](http://www.cnblogs.com/yanghuahui/p/3365976.html)

以上是针对实际问题的2种解决方案，欢迎留言指正。