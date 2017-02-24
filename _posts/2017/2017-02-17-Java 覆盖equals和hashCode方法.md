---
layout: post
title: Java 覆盖equals和hashCode方法
category: 编程 
tags: [编程]
---

>覆盖equals方法看起来似乎很简单，但是有许多覆盖方式会导致错误，并且后果非常严重，最容易避免这类问题的办法就是不覆盖equals方法。

## 前言
覆盖equals方法看起来似乎很简单，但是有许多覆盖方式会导致错误，并且后果非常严重，最容易避免这类问题的办法就是不覆盖equals方法。

什么时候需要覆盖equals方法？如果类具有自己特有的“逻辑相等”概念（不同于对象等同），而且超类还没有覆盖equals方法以实现期望的行为，这时需要覆盖equals方法。


## 覆盖equals

覆盖equals方法时，必须遵守它的通用约定，如果你违反了它们，就会发现你的程序将表现不正常，甚至奔溃，而且很难找到失败的根源。

### 通用约定

1. 自反性。对于任何非null的引用值x、x，equals(x)必须返回true。
2. 对称性。对于任何非null的引用值x、y，当且仅当y.equals(x)返回true时，x.equals(y)必须返回true。
3. 传递性。对于任何非null的引用值x、y、z，如果x.equals(y)返回true,并且y.equals(z)返回true，则x.equals(z)必须返回true。
4. 一致性。对于任何非null的引用值x、y，只要equals的比较操作在对象中所用的信息没有被修改，多次调用x.equals(y)就会一致地返回true，或者一致地返回false。
5. 非空性。对于任何非null的引用值x、x，equals(null),必须返回false。

一般IDE工具，如IntelliJ IDEA可以帮助实现equals方法覆盖。基本上是符合以上约定的。

![这里写图片描述](http://img.blog.csdn.net/20170217112058004?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmlja3lJVA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


### 实现高质量equals方法的诀窍
1. 使用==操作符检查“参数是否为这个对象的引用”。
2. 使用instanceof操作符检查“参数是否为正确的类型”。
3. 把参数转化为正确的类型。
4. 对于该类的中每个关键域，检查参数中的域是否与该对象中对应的域想匹配。

#### 示例

```
public class Point {
	private final int x;
	private final int y;

	public Point(int x, int y) {
		this.x = x;
		this.y = y;
	}
	@Override
	public boolean equals(Object obj) {
		if (this==obj) return true;
		if (!(obj instanceof Point)) {
			return false;
		}
		Point p = (Point) obj;
		return p.x == x && p.y == y;
	}
}
```

#### 类型检查为什么不用getClass()

getClass测试代替instanceof 测试，只有当对象有相同的实现时，才能使对象等同。此外，使用instanceof 还可以省去对null的判断。

```
	@Override
	public boolean equals(Object obj) {
		if (this==obj) return true;
		if (obj==null || this.getClass()!=obj.getClass()) return false;
		Point p = (Point) obj;
		return p.x == x && p.y == y;
	}
```

`Point1`,`Point2`继承`Point`。
```
	public static void point1ComparePoint2(){
		Point point1=new Point1(1,2);
		Point point2=new Point2(1,2);
		Point point3=new Point2(1,2);

		//不同实现的比较
		System.out.println(point1.equals(point2));
		System.out.println(point2.equals(point1));

		//相同实现的比较
		System.out.println(point2.equals(point3));

	}
```

运行结果：
`false
false
true`


## 覆盖hashCode方法

重写equals方法必须要重写hashCode方法。如果不这样做的话会违反Object.hashCode的通用约定，从而导致该类无法结合所有基于散列的集合一起正常运作。如HashMap、HashTable、HashSet。

```
	public static void pointListCompare(){
		Point point1=new Point(1,2);
		Point point2=new Point(1,2);
		Map<Point,String> mp=new HashMap<>();
		mp.put(point1,"Point1");
		System.out.println("point1==point2 "+(point1==point2));
		System.out.println("point1 hashCode is:"+(point1.hashCode()));
		System.out.println("point2 hashCode is:"+(point1.hashCode()));
		System.out.println(mp.containsKey(point1));
		System.out.println(mp.containsKey(point2));
	}
```

运行结果：
`point1==point2 false
point1 hashCode is:2133927002
point2 hashCode is:1836019240
true
false`

`Point`中添加hashCode()方法

```
	@Override
	public int hashCode() {
		//31是一个奇素数，有个很好的特性，乘法可以优化为移位和减法：31*i==(i<<5)-i。
		// 现在的VM可以自动完成这种优化。习惯上都使用素数来计算散列结果
		int result = x;
		result = 31 * result + y;
		return result;
	}
```


运行结果：
`point1==point2 false
point1 hashCode is:33
point2 hashCode is:33
true
true`

HaspMap有一项优化，可以将与每个项相关联的散列码缓存起来，如果散列码不匹配，也不必检验对象的等同性。


## 完美实例

不同类型的覆盖方法和hashCode生成。

```
public class PerfectEquals {
	//基本类型
	private int x;
	private char c;
	private double d;
	private float f;
	private String name;//引用类型
	private Color color;//枚举类型
	private Point point;//引用类型
	private List<Point> pointList;//集合类


	public PerfectEquals(int x, char c, double d, float f, String name, Color color, Point point, List<Point> pointList) {
		this.x = x;
		this.c = c;
		this.d = d;
		this.f = f;
		this.name = name;
		this.color = color;
		this.point = point;
		this.pointList = pointList;
	}

	@Override
	public boolean equals(Object o) {
		//1. 使用==操作符检查“参数是否为这个对象的引用”。
		if (this == o) return true;
		//2. 使用instanceof操作符检查“参数是否为正确的类型”。
		if (!(o instanceof PerfectEquals)) return false;
		//3. 把参数转化为正确的类型。
		PerfectEquals that = (PerfectEquals) o;

		//4. 对于该类的中每个关键域，检查参数中的域是否与该对象中对应的域想匹配。
		if (x != that.x) return false;
		if (c != that.c) return false;
		if (Double.compare(that.d, d) != 0) return false;
		if (Float.compare(that.f, f) != 0) return false;
//		if (name != null ? !name.equals(that.name) : that.name != null) return false;//10000000 times compare cost time is 27-32ms
		if (name!=that.name|| name!=null&&!name.equals(that.name)) return false;//更快些，10000000 times compare cost time is 3-4ms
		if (color != that.color) return false;
//		if (point != null ? !point.equals(that.point) : that.point != null) return false;
		if (point!=that.point||point!=null&&!point.equals(that.point)) return false;
//		return pointList != null ? pointList.equals(that.pointList) : that.pointList == null;
		return pointList==that.pointList|| pointList!=null&&pointList.equals(that.pointList);
	}

	@Override
	public int hashCode() {
		int result;
		long temp;
		result = x;
		//31是一个奇素数，有个很好的特性，乘法可以优化为移位和减法：31*i==(i<<5)-i。
		// 现在的VM可以自动完成这种优化。习惯上都使用素数来计算散列结果
		result = 31 * result + (int) c;
		temp = Double.doubleToLongBits(d);
		result = 31 * result + (int) (temp ^ (temp >>> 32));
		result = 31 * result + (f != +0.0f ? Float.floatToIntBits(f) : 0);
		result = 31 * result + (name != null ? name.hashCode() : 0);
		result = 31 * result + (color != null ? color.hashCode() : 0);
		result = 31 * result + (point != null ? point.hashCode() : 0);
		result = 31 * result + (pointList != null ? pointList.hashCode() : 0);
		return result;
	}

	public static void main(String[] args) {
		List<Point> pointList=new ArrayList<>();
		Point point1=new Point(1,2);
		Point point2=new Point(2,2);
		Point point3=new Point(3,2);
		pointList.add(point1);
		pointList.add(point2);
		pointList.add(point3);

		PerfectEquals pe1=new PerfectEquals(12,'c',12D,21f,"Perfect",Color.RED,new Point(23,24),pointList);
		PerfectEquals pe2=new PerfectEquals(12,'c',12D,21f,"Perfect",Color.RED,new Point(23,24),pointList);

		long start=System.currentTimeMillis();
		for (int i=0;i<10000000;i++){
			pe1.equals(pe2);
		}
		long end=System.currentTimeMillis();
		System.out.println("compare cost time is :"+(end-start));
	}
```

---

**作者：Wuxinshui**  
**出处：[http://wuxinshui.github.io/](http://wuxinshui.github.io/blog)**      
**版权归作者所有，转载请注明出处** 