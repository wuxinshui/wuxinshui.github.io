---
layout: post
title: Java BigDecimal开方
category: 编程 
tags: [编程]
---


>一般开平方使用的是Math中的静态方法`Math.sqrt(double a)`，涉及到金融计算的时候，`Math.sqrt(double a)`精度就不够了。金融领域的计算，用的都是BigDecimal类型。然而BigDecimal对于数字开平方没提供有效的方法。
 
 这里整理了一些网上提供的方案。


## 前言
一般开平方使用的是Math中的静态方法`Math.sqrt(double a)`，涉及到金融计算的时候，`Math.sqrt(double a)`精度就不够了。金融领域的计算，用的都是BigDecimal类型。然而BigDecimal对于数字开平方没提供有效的方法。

这里整理了一些网上提供的方案。

## 解决方案

### 方案一

[Java 牛顿迭代法求大数开方](http://kugwzk.info/index.php/archives/1662)

![这里写图片描述](http://img.blog.csdn.net/20170921140717996?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 方案二

[纪念我的无聊——BigDecimal平方根的实现](http://blog.csdn.net/zhuzeitou/article/details/5011119)

实现代码有点多。

### 方案三

 [BigDecimal怎么开方？](http://www.debugease.com/j2se/898449.html)

![这里写图片描述](http://img.blog.csdn.net/20170921140755576?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

和方案1类似。

### 使用方案

参考以上方案，采用方案一。

```java
   /**
     * 标准差σ=sqrt(s^2)
     * 结果精度：scale
     * 牛顿迭代法求大数开方
     *
     * @param x
     * @param scale
     * @return
     */
    public static BigDecimal standardDeviation(BigDecimal[] x, int scale) {
        //方差
        BigDecimal variance = variance(x, scale);
        BigDecimal base2 = BigDecimal.valueOf(2.0);
        //计算精度
        int precision = 100;
        MathContext mc = new MathContext(precision, RoundingMode.HALF_UP);
        BigDecimal deviation = variance;
        int cnt = 0;
        while (cnt < 100) {
            deviation = (deviation.add(variance.divide(deviation, mc))).divide(base2, mc);
            cnt++;
        }
        deviation = deviation.setScale(scale, BigDecimal.ROUND_HALF_UP);
        return deviation;
    }
```



欢迎吐槽，提供更好的解决方案。







