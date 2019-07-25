---
layout: post
title: Effective Java-嵌套类(内部类)
category: 编程 
tags: [编程]
---
>嵌套类是指被定义在另一个类内部的类。

## 概念
嵌套类(nested class)是指被定义在另一个类内部的类。嵌套类的存在目的应该只是为外围类(enclosing class)提供服务。嵌套类有四种：

 -  静态成员类(static member class)；
 -  非静态成员类(nonstatic member class)；
 -  匿名类(anonymous class)；
 -  局部类(local class)。

除了第一种外，其他三种都被成为内部类(inner class)。静态嵌套类， 就是用static修饰的成员嵌套类。InnerClass,也就是没有用static定义的nested classes，Inner Classes 不能定义为static，不能有static方法和static初始化语句块

## 静态成员类
1. 可以访问外围类的所有成员，非静态成员需要实例化外围类才能访问。
2.  如果静态成员类是私有的，只能在外围类的内部才能访问。
3.  外围类外部实例化静态成员类：外围类.静态成员类，`        OuterStaticInnerClass.PublicInnerCLass publisInnerCLass = new OuterStaticInnerClass.PublicInnerCLass();`
4.  私有静态成员类一种常见用法是用来代码外围类所代表的对象的组件。
`
### 代码示例

```java
public class OuterStaticInnerClass {

    private String teacher;
    private static int grade;//年级
    private static String className;//班级名称

    public OuterStaticInnerClass() {
        this.grade = 1;
        this.className = "高三";
        this.teacher="王莉";
    }

    public void outerPrint() {
        System.out.println("OuterStaticInnerClass.outerPrint");
        System.out.println(className + grade + "班的老师是：" + teacher);
    }

    public static class PublicInnerCLass {
        private int age;//年龄
        private String name;//名字

        //构造方法
        public PublicInnerCLass() {

        }

        public PublicInnerCLass(int age, String name) {
            this.age = age;
            this.name = name;
        }

        public String print() {
            //静态类可以访问外围类的成员
            new OuterStaticInnerClass().outerPrint();
            System.out.println("OuterStaticInnerClass:PublicInnerCLass");
            return name + " 年龄：" + age + ",班级：" + className + Integer.toString(grade)+"班,老师："+new OuterStaticInnerClass().teacher;
        }
    }

    private static class PrivateInnerCLass {
        private int age;//年龄
        private String name;//名字

        public PrivateInnerCLass() {

        }

        public PrivateInnerCLass(int age, String name) {
            this.age = age;
            this.name = name;
        }


        public void print() {
            System.out.println("OuterStaticInnerClass:PrivateInnerCLass");
            System.out.println(name + " 年龄：" + age + ",班级：" + className + Integer.toString(grade) + "班,老师：" + new OuterStaticInnerClass().teacher);
        }
    }
}
```
### 实例化

```java
    /**
     * 静态成员类
     */
    public static void outerStaticInnerClassTest() {
        OuterStaticInnerClass outerStaticInnerClass = new OuterStaticInnerClass();
        //静态成员类实例化
        OuterStaticInnerClass.PublicInnerCLass publisInnerCLass = new OuterStaticInnerClass.PublicInnerCLass(18, "lis");
        //chapter4.item22.OuterStaticInnerClass.PrivateInnerCLass' has private access in 'chapter4.item22.OuterStaticInnerClass
        //如果静态成员类是私有的，只能在外围类的内部才能访问
        //OuterStaticInnerClass.PrivateInnerCLass privateInnerCLass=new OuterStaticInnerClass.PrivateInnerCLass();

        System.out.println(publisInnerCLass.print());
    }
```

## 非静态成员类
1. 非静态成员类的实例方法内部可以调用外围实例的方法。
2.  在没有外围实例的情况下，创建非静态成员类的实例是不可能的。
3.  非静态成员类的每个实例都隐含着与外围类的一个外围实例相关联。

### 代码示例

```java
public class OuterNonStaticInnerClass {
    private String teacher;
    private static int grade;//年级
    private static String className;//班级名称

    public OuterNonStaticInnerClass() {
        this.grade = 1;
        this.className = "高三";
        this.teacher = "王莉";
    }

    public void outerPrint() {
        System.out.println("OuterNonStaticInnerClass.outerPrint");
        System.out.println(className + grade + "班的老师是：" + teacher);
    }

    public class PublicInnerCLass {
        private int age;//年龄
        private String name;//名字

        //构造方法
        public PublicInnerCLass() {

        }

        public PublicInnerCLass(int age, String name) {
            this.age = age;
            this.name = name;
        }

        public String print() {
            // 非静态成员类的实例方法内部可以调用外围实例的方法和变量。
            outerPrint();
            System.out.println("OuterStaticInnerClass:PublicInnerCLass");
            return name + " 年龄：" + age + ",班级：" + className + Integer.toString(grade) + "班,老师：" + teacher;
        }
    }

    private class PrivateInnerCLass {
        private int age;//年龄
        private String name;//名字

        public PrivateInnerCLass() {

        }

        public PrivateInnerCLass(int age, String name) {
            this.age = age;
            this.name = name;
        }

        public void print() {
            System.out.println("OuterStaticInnerClass:PrivateInnerCLass");
            System.out.println(name + " 年龄：" + age + ",班级：" + className + Integer.toString(grade) + "班,老师：" + teacher);
        }

    }
 }
```

### 实例化

```java
    public static void outerNonStaticInnerClassTest(){
        //类内部实例化
        // 非静态成员类的实例方法内部可以调用外围实例的方法。
        //在没有外围实例的情况下，创建非静态成员类的实例是不可能的。
        //非静态成员类的每个实例都隐含着与外围类的一个外围实例相关联。
        OuterNonStaticInnerClass outerClass = new OuterNonStaticInnerClass();
        //chapter4.item22.OuterNonStaticInnerClass.PrivateInnerCLass' has private access in 'chapter4.item22.OuterNonStaticInnerClass
        //OuterNonStaticInnerClass.PrivateInnerCLass privateInnerCLass = outerClass.new PrivateInnerCLass();
        //privateInnerCLass.print();
        OuterNonStaticInnerClass.PublicInnerCLass publicInnerCLass = outerClass.new PublicInnerCLass(18, "lis");
        System.out.println(publicInnerCLass.print());
    }
```


## 匿名内部类
1. 匿名类没有名字，不是外围类的一个成员，不与其他成员一起被声明。
2.  只能在使用的同时被声明和实例化，没有构造方法。
3.  当且仅当匿名类出现在非静态的环境中时，才有外围实例。
4. 即使匿名类出现在静态的环境中，也不可能拥有任何静态成员。
5.  匿名类不能实现接口或者扩展一个类。
6. 除了从它的超类型中得到之外，匿名类的客户端无法调用任何任何成员。
7.  常见用法：①动态创建函数对象；②创建过程对象，比如Runnable、Thread;③用于静态工程方法的内部

### 代码示例

```java
public interface OuterAnonymousInnerInter {
    public void print(int age, String name);
}

public abstract class OuterAnonymousInnerClass {

    //成员方法
    public abstract void innerMethod(int age, String name);
    }
```

### 实例化

```java
 public static void outerAnonymousInnerInterTest() {
        OuterAnonymousInnerInter innerInter = new OuterAnonymousInnerInter() {
            private String teacher = "王莉";
            @Override
            public void print(int age, String name) {
                System.out.println("OuterAnonymousInnerInter anonymous inner class");
                System.out.println(name + " 年龄：" + age + "老师：" + teacher);
            }
        };
        innerInter.print(18, "lis");
        
        OuterAnonymousInnerClass outerClass = new OuterAnonymousInnerClass() {
            private String teacher = "王莉";
            @Override
            public void innerMethod(int age, String name) {
                System.out.println("OuterAnonymousInnerClass innerMethod");
                System.out.println(name + " 年龄：" + age + "老师：" + teacher);
            }
        };
        outerClass.innerMethod(18, "lis");
    }
```

## 局部类

1. 在任何“可以声明局部变量”的地方，都可以声明局部类。
2.  是四种嵌套类中用的最少的。
3. 局部类不能有访问控制符(private,public,protected修饰），可以是抽象的,也可以定义为final。

### 代码示例

```java
public class OuterLocalInnerClass {
    private String teacher;//老师
    private static int grade;//年级
    private static String className;//班级名称

    public OuterLocalInnerClass() {
        this.grade = 1;
        this.className = "高三";
        this.teacher = "王莉";
    }

    //局部类
    public void innerMethod() {
       class InnerCLass3 {
            private int age;//年龄
            private String name;//名字

            public InnerCLass3(int age, String name) {
                this.age = age;
                this.name = name;
            }
        }
        InnerCLass3 innerCLass3=new InnerCLass3(18, "lis");
        System.out.println(innerCLass3.name + " 年龄：" + innerCLass3.age);
        System.out.println(className + grade + "班的老师是：" + teacher);
    }
}
```

### 实例化

```java
    public static void outerLocalInnerClassTest(){
        OuterLocalInnerClass outerClass=new OuterLocalInnerClass();
        outerClass.innerMethod();
    }
```

`lis 年龄：18
高三1班的老师是：王莉`


## 推荐

[java嵌套类(Nested Classes)总结](http://www.cnblogs.com/aigongsi/archive/2012/04/24/2467183.html)