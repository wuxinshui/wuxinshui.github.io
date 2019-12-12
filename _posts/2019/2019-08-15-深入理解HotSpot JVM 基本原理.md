---
layout: post
title: 深入理解HotSpot JVM 基本原理
category: JVM
tags: [JVM]
---

>Java HotSpot虚拟机是Sun用于Java平台的VM。 它使用许多先进技术为Java应用程序提供最佳性能，包括最先进的内存模型，垃圾收集器和自适应优化器。


## 关于JAVA

Java®编程语言是一种通用的、并发的、面向对象的语言。它的语法类似于C和C++，但它省略了许多使C和C++复杂、混乱和不安全的特性。

Java 是几乎所有类型的网络应用程序的基础，也是开发和提供嵌入式和移动应用程序、游戏、基于 Web 的内容和企业软件的全球标准。.

从笔记本电脑到数据中心，从游戏控制台到科学超级计算机，从手机到互联网，Java 无处不在！

![](https://oscimg.oschina.net/oscnet/dc63cb372413fdaf6a83ab99724c69f04d3.jpg)


Java的技术体系主要有各种硬件平台上的JVM虚拟机、提供各开发领域接口支持的Java API、Java编程语言、三方Java框架(Spring等)构成。

Java程序设计语言、Java虚拟机、Java API类库这三部分统称为JDK（Java Development Kit），JDK是用于支持Java程序开发的最小环境。

可以把Java API类库中的Java SE API子集和Java虚拟机这两部分统称为JRE（Java Runtime Environment），JRE是支持Java程序运行的标准环境。

下图展示了Java技术体系所包含的内容，以及JDK和JRE所涵盖的范围。

![](https://oscimg.oschina.net/oscnet/501e504442bedb1835f50cab35fa9b1fa69.jpg)


## 关于JVM

Java虚拟机是Java平台的基石。负责其硬件和操作系统的独立性，为Java字节码的执行提供运行时环境。

JVM虚拟机在Java 虚拟机规范中没有规定具体实现，而是有各大厂商自己实现。

> Implementation details that are not part of the Java Virtual Machine's specification would unnecessarily constrain the creativity of implementors. For example, the memory layout of run-time data areas, the garbage-collection algorithm used, and any internal optimization of the Java Virtual Machine instructions (for example, translating them into machine code) are left to the discretion of the implementor.

Classic VM 是“世界上第一款商用Java虚拟机”，在JDK 1.2之前是Sun JDK中唯一的虚拟机。

在JDK 1.2时，它与HotSpot VM并存，而在JDK 1.3时，HotSpot VM成为默认虚拟机，直到JDK 1.4的时候，Classic VM才完全退出商用虚拟机的历史舞台。

1999年4月27日，HotSpot虚拟机发布，HotSpot最初由一家名为“Longview Technologies”的小公司开发，因为HotSpot的优异表现，这家公司在1997年被Sun公司收购了。后来它成为了JDK 1.3及之后所有版本的Sun JDK的默认虚拟机。

在2008年和2009年，Oracle公司分别收购了BEA公司和Sun公司，这样Oracle就同时拥有了两款优秀的Java虚拟机：JRockit VM和HotSpot VM。

## 关于HostSpot

Java HotSpot虚拟机是Sun用于Java平台的VM。 它使用许多先进技术为Java应用程序提供最佳性能，包括最先进的内存模型，垃圾收集器和自适应优化器。

在SUN/Orace JDK中包括两种风格的VM
- `client mode`
- `server mode`

默认以`client mode`启动。

启动命令加`- server`,以`server mode`启动。

查看当前JVM mode：

![](https://oscimg.oschina.net/oscnet/415351618fc4aa674b59d2ba86c54d80ff9.jpg)

两种mode的区别：

`client mode`
- 短时间内启动，运行时，占用更少内存
- C1轻量级编译器，优化较少
- 适合轻量级程序和桌面程序
	
`server mode`
- 启动慢，运行时，占用更大的内存
- C2重量级编译器，更彻底的优化
- 能提供更好的性能，适合生产部署

**HotSpot JVM Architecture**

![](https://javapapers.com/wp-content/uploads/2014/10/JVM-Architecture.jpg)

HotSpot JVM 主要包括3个组件:
- Class Loader Subsystem
- Runtime Data Areas
- Execution Engine

## Class Loader Subsystem

`Class Loader Subsystem`是JVM必不可少的核心，用于读取/加载`.class`文件,并把字节码保存在JVM方法区。

### 加载过程

Java虚拟机中类加载的全过程：加载，验证，准备，解析，初始化。

![](https://www.javacodegeeks.com/wp-content/uploads/2018/04/jvm_archi_clrda_guide_3.jpg.webp)

#### Loading

`Loading`阶段，虚拟机完成三件事情：
1. 通过一个类的全限定名来获取定义此类的二进制字节流；
2. 将这个字节流所代表的静态存储结构转换为方法区的运行时数据结构；
3. 在内存中生成一个代表这个类的`java.lang.class`对象，作为方法区这个类的各种数据的访问入口。

加载的第一步是在Java虚拟机外部去实现的，有‘类加载器’来完成加载动作。

绝大部分Java程序都会用到以下3种系统提供的类加载器：
1. 启动类加载器(Bootstrap ClassLoader)
	- 使用C++语言实现，是虚拟机自身的一部分。
	- 加载<JAVA_HOME>/lib中虚拟机识别的jar，如rt.jar。
	- 无法被Java程序直接引用
2. 扩展类加载器(Extension ClassLoader)
	- 由sun.misc.Launcher$ExtClassLoader实现
	- 加载<JAVA_HOME>/lib/ext中类库
	- Java程序可以直接使用
3. 应用程序类加载器(Application ClassLoader)
	- 由sun.misc.Launcher$AppClassLoader实现
	- 也称系统类加载器，加载应用程序classpath下的jar
	- 可以直接使用，程序中默认的类加载器
#### Linking

执行类或接口的链接。

- Verification，验证。确保Class文件的字节流中包含的信息符合当前虚拟机的要求，且不会危害虚拟机的安全。
- Preparation，准备。在方法区中为类变量(被static修饰的变量)分配内存并设置初始值。
- Resolution，解析。虚拟机将常量池的符号引用替换为直接引用。

#### Initialization

执行类加载的最后阶段，为所有静态变量都分配了原始值，静态块从父类执行到子类。

### 加载原则

1. Delegation，委派
2. Visibility，可见性
3. Uniqueness，唯一性

#### Delegation

**双亲委派模型**

![](https://oscimg.oschina.net/oscnet/804c2d9c52a3ba45d380d56753cd7e134b3.jpg)

双亲委派模型在JDK1.2中被引入，要求除了启动类加载器，其他类加载器都要有父类加载器。

加载过程：当一个类加载器收到加载请求的时候，先委派给父类加载器去完成，每个层次的类加载器都是如此，最终加载请求传到顶层启动类加载器。如果父类无法完成加载请求，子类才会去尝试加载。

**破坏双亲委派模型**

双亲委派解决了各类加载器的基础类的统一问题。

如果基础类需要反调用户代码，怎么办？

线程上下问类加载器(Thread Context ClassLoader),TCCL。解决基础类反调用户代码。例如JDK中实现SPI机制的JDBC、JNDI等。

#### Visibility

子类加载器能看到父类加载器加载的类。父类加载器看不到子类加载器中的类。

#### Uniqueness

父类加载器加载过的类不会被子类加载器加载。

## Runtime Data Areas

![](https://oscimg.oschina.net/oscnet/2ad33a8db8a7dcc4097db7fc43266d2cfbe.jpg)

`Runtime Data Areas`大致可以分为两部分：
- 线程私有区，线程创建时分配内存。线程启动时初始化，并在线程完成后销毁，
- 线程共享区，所有线程都可以访问。JVM启动时初始化，在关闭时销毁。

### 程序计数器

保存当前正在执行的JVM指令地址。每个线程都有自己的PC。

### Java虚拟机栈

每个Java虚拟机线程都有一个私有Java虚拟机堆栈，与线程同时创建。

### 本地方法栈

供用非Java语言实现的本地方法的堆栈。换句话说，它是用来调用通过JNI(Java Native Interface Java本地接口）调用的C/C++代码。根据具体的语言，一个C堆栈或者C++堆栈会被创建。

### Java堆

![](https://oscimg.oschina.net/oscnet/c57fc2d1bcf686cc79464581d82736d1aaf.jpg)

用来保存实例或者对象的空间，而且它是垃圾回收的主要目标。当讨论类似于JVM性能之类的问题时，它经常会被提及。JVM提供者可以决定怎么来配置堆空间，以及不对它进行垃圾回收。

### 方法区

方法区是所有线程共享的，它是在JVM启动的时候创建的。它保存所有被JVM加载的类和接口的运行时常量池，成员变量以及方法的信息，静态变量以及方法的字节码。JVM的提供者可以通过不同的方式来实现方法区。在Oracle 的HotSpot JVM里，方法区被称为永久区或者永久代（PermGen）。是否对方法区进行垃圾回收对JVM的实现是可选的。

### 运行时常量池

这个区域和class文件里的constant_pool是相对应的。这个区域是包含在方法区里的，不过，对于JVM的操作而言，它是一个核心的角色。因此在JVM规范里特别提到了它的重要性。除了包含每个类和接口的常量，它也包含了所有方法和变量的引用。简而言之，当一个方法或者变量被引用时，JVM通过运行时常量区来查找方法或者变量在内存里的实际地址。

## Execution Engine

![](https://www.javacodegeeks.com/wp-content/uploads/2018/04/jvm_archi_ee_guide_3.jpg)

### Interpreter

读取字节码指令并以顺序方式执行。

### JIT

JIT (Just In Time) Compiler。

抵消了Interpreter执行速度慢的缺点并提高了性能。
JIT编译器同时编译字节码的类似部分，从而减少了编译所需的总时间。

### Garbage Collection

通过收集和删除未引用的对象来释放内存。

## 对象是否存活

在堆里面存放着Java世界几乎所有的对象实例，垃圾收集器在对堆进行回收前，第一件事就是要判断对象是否存活。

### 引用计数算法

引用计数算法(Reference Counting)实现简单，判定效率也很高。给对象中添加一个引用计数器，每当一个地方引用它时，计数器值就加1；引用失效时，计数器值就减1；计数器为0的对象就是不可能再被使用的。

Python语言、游戏脚本领域被广泛应用的Squire中都使用了引用计数算法进行内存管理。但是，java虚拟机里没有用，主要原因是很难解决对象之间相互循环引用的问题

### 可达性分析

可达性分析(Reachability Analysis),基本思路是通过一系列的称为“GC Roots”的对象作为起始点，从这些节点开始往下搜索，搜索所走过的路程称为引用链(Reference Chain),当一个对象到GC Roots没有任何引用链相连时，则证明此对象不可用。

Java语言中，可作为GC Roots的对象包括以下几种：
- 虚拟机栈(栈帧中的本地变量表)中引用的对象；
- 方法区中类静态属性引用的对象
- 方法区中常量引用的对象
- 本地方法栈中JNI(Native方法)引用的对象

## 垃圾收集算法

常见的垃圾收集算法。

### 标记-清除算法

算法分为‘标记’、‘清除’两个阶段。

首先标记出所有需要回收的对象，在标记完成后统一回收所有被标记的对象。

### 复制算法

复制(Copying)，将可用内存按容量分为大小相等的两块，每次只使用其中的一块。
当这一块的内存用完了，将还存活的对象复制到另外一块上去。把已使用过的内存空间一次清理掉。

实现简单，运行高效，内存使用率不高，用于回收新生代。

IBM研究表明，新生代中98%的对象都是在第一次GC时被回收掉，不需要按照1:1分配空间。

HotSpot虚拟机默认Eden和Survivor比例是8:1，只有10%的内存会被浪费。

Survivor空间不够时，需要依赖老年代进行分配担保，新生代收集下来的存活对象直接进入老年代。

### 标记-整理算法

标记-整理(Mark-Compact)算法，标记所有存活对象，向一端移动，然后直接清理掉端边界意外的内存。

### 分代收集算法

根据对象存活周期不同，将Java堆分为新生代和老年代。

新生代，采用复制算法。

老年代采用‘标记-清理’或者‘标记-整理’算法。

## 垃圾收集器

如果说，收集算法是内存回收的方法论，那么，垃圾收集器就是内存回收的具体体现。

Java虚拟机规范没有规范如何实现垃圾收集器。不同的厂商、版本的虚拟机实现可能会有很大差别。

下面，基于JDK1.7 Update14之后的HotSpot虚拟机讨论收集器。

![](https://cdn.app.compendium.com/uploads/user/e7c690e8-6ff9-102a-ac6d-e4aebca50425/f4a5b21d-66fa-4885-92bf-c4e81c06d916/Image/b125abbe194f5608840119eccc9d90e2/collectors.jpg)

### Serial 收集器

>"Serial" is a stop-the-world, copying collector which uses a single GC thread.


### ParNew 收集器
>"ParNew" is a stop-the-world, copying collector which uses multiple GC threads. 

### Parallel Scavenge 收集器
>"Parallel Scavenge" is a stop-the-world, copying collector 
which uses multiple GC threads.


### Serial Old 收集器
>"Serial Old" is a stop-the-world,
mark-sweep-compact collector that uses a single GC thread.

### Parallel Old 收集器
>"Parallel Old" is a compacting collector that uses multiple GC threads.
Using the -XX flags for our collectors for jdk6.

### CMS收集器
>"CMS"(Concurrent Mark Sweep) is a mostly concurrent, low-pause collector.


### G1收集器
>G1(Garbage First) straddles the young generation - tenured generation boundary because it is a generational collector only in the logical sense. G1 divides the heap into regions and during a GC can collect a subset of the regions.


## JMM

Java内存模型(Java Memory Model)，JMM，用来屏蔽掉各种硬件和操作系统之间内存访问差异，以实现在各种平台下一致的内存访问效果。

1. JMM主要目标是定义程序中各个变量的访问规则。即在虚拟机中将变量存储到内存和从内存中取出变量。

2. 所有变量都存储在主内存(Main Memory),每个线程还有自己的工作内存(Working Memory)。

3. 线程对变量的读取、赋值等操作都必须在工作内存中进行。

4. 线程间变量值的传递必须通过主内存来完成。


## 参考

[Our Collectors](https://blogs.oracle.com/jonthecollector/our-collectors)

[了解 Java 技术](https://www.java.com/zh_CN/about/)

[Java Virtual Machine from Sun](http://www.java-virtual-machine.net/sun-java-virtual-machine.html)

[Java Garbage Collection Introduction](https://javapapers.com/java/java-garbage-collection-introduction/)

[The Java® Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se7/html/index.html)

[Java发展史](https://book.2cto.com/201306/25431.html)

[The Java HotSpot Performance Engine Architecture](https://www.oracle.com/technetwork/java/whitepaper-135217.html#solid)

[JVM Server vs Client Mode](https://javapapers.com/core-java/jvm-server-vs-client-mode/)

[JSR-133: JavaTM Memory Model and Thread Specification](https://download.oracle.com/otn-pub/jcp/memory_model-1.0-pfd-spec-oth-JSpec/memory_model-1_0-pfd-spec.pdf?AuthParam=1567501963_ec5c815e549e4ec249df6e2d3d402463)

[JVM Architecture: JVM Class loader and Runtime Data Areas](https://www.javacodegeeks.com/2018/04/jvm-architecture-jvm-class-loader-and-runtime-data-areas.html)

[JVM Architecture: Execution Engine in JVM](https://www.javacodegeeks.com/2018/04/jvm-architecture-execution-engine-in-jvm.html)