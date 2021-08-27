:whale:<span style='color:pink;font-size:30'>JVM虚拟机</span>

[TOC]

#### 1、垃圾回收算法

`````java
/*判断对象是否被回收
··1、引用计数法
    优点：执行较快，适用于不被长时间打断的实时环境；
    缺点：伴随加减法影响性能；无法应用于循环引用
  2、可达性搜索算法
    通过GCROOT对象寻找所有引用的对象，剩余均为无用对象。至少两次标记，之后才回收。
    */

1、标记清除
    缺点：产生碎片
2、复制
    存活对象多，则效率大大降低；一半内存可用；
3、标记整理
    标记后，将存活对象向一端移动
4、分代收集算法
    将堆划分为新生代和老年代；新生代大部分采用复制算法，且划分为一个较大的Eden和两个较小的Survivor空间，大小约为8比2，垃圾回收时将eden和survivor1中存活对象复制到survivor2（若空间不足，有老年代担保；）gc后，对象年龄+1，达到15，移至老年代。一般，大对象（需要大量连续空间的对象，如数组）直接分配老年代。
    
`````

##### 1-1 GCRoot对象包括哪些

```java
方法区类静态属性引用的对象
方法区常量池引用的对象
虚拟机栈引用的对象
本地方法栈
```



#### 2、JVM的组成

![image-20210827090955441](https://github.com/younggungun/younggungun/blob/main/images/jvm%E7%9A%84%E7%BB%84%E6%88%90.png)

##### 2-1内存分区各个作用

程序计数器：纪录当前线程执行的字节码行号；

虚拟机栈：局部变量、对象引用、动态连接、操作数栈、方法出口等；

本地方法栈：与虚拟机栈作用类似，不同的是虚拟机栈服务java方法，本地方法栈服务本地native方法；

堆：分配内存给几乎所有的对象实例；

`随着JIT编译器的发展，在编译期间，如果JIT经过逃逸分析，发现有些对象没有逃逸出方法，那么有可能堆内存分配会被优化成栈内存分配。`

方法区：存储已被虚拟机加载的类信息、常量、静态变量、

`数组引用变量是存放在栈内存中，数组元素是存放在堆内存中`

#### 3、GC垃圾收集器

![image-20210827173853920](https://github.com/younggungun/younggungun/blob/main/images/jvm%E7%9A%84%E5%90%84%E6%94%B6%E9%9B%86%E5%99%A8.png)

Serial：单线程，——复制

ParNew：并行，serial的多线程版本——复制

Parallel Scavenge：高吞吐，高效利用cpu——复制

Serial Old：	Serial的老年代版本——标记整理

Parallel Old：老年代并行——标记整理

CMS：老年代并行，牺牲吞吐获取最短回收停顿——标记清除

G1：堆并行收集——标记整理

#### 4、fullGc的原因

#### 5、Java是否有内存泄露

- 内存泄露：不再使用的对象或变量一直占用着内存；

  虽有垃圾回收机制，但仍有内存泄露存在；原因：长生命周期对象持有短生命周期的引用；

#### 6、java四种引用

```java
/*1、强引用
  	 垃圾回收器不回收的对象，即便内存不足，抛出OOM异常
  2、软引用
  	 若内存不足，则回收这些对象的内存
  3、弱引用
  	 */与软引用不同的是：只具有弱引用的对象有更短生命周期，无论内存是否足够，只要发生gc则回收只具有弱引用的对象/*
  4、虚引用
  	 在任何时候都可能被垃圾回收
  除此，软引用和弱引用可以和引用队列联合使用，而虚引用必须与引用队列联合使用——>发生gc，jvm会把相应的引用加入引用队列。
```



#### 7、java类加载机制

```java
类的生命周期：虚拟机将字节码文件加载到内存>>并对其进行验证>>准备>>解析>>初始化>>使用>>卸载
类加载过程：读取二进制流至jvm->验证格式->为静态变量分配内存->常量池引用解析->执行static代码
              加载           验证         准备             解析    
                                  static 对象被设置默认值
    							  static final 被赋予给予的值
    												
```



#### 8、类加载器

```java
1、BootStrap ClassLoader 顶层类加载器，不继承ClassLoader类，加载jdk核心库
2、Extension ClassLoader 拓展类加载器，lib\ext下的jar包河class文件
3、Application ClassLoader 应用程序类加载器，加载当前应用classpath的所有类 
4、用户自定义类加载器
```

##### 8-1双亲委派机制

```java
当一个ClassLoader实例需要加载某个类时，在它尝试搜索某个类时，先把任务委托其夫加载器，即由上之下的依次尝试，先由顶层加载器尝试加载，若不行则任务转给拓展类加载器，若不行则任务转给app类加载器，若依旧不行，返回给一开始的ClassLoader类加载器实例，若均未加载，则抛classnotFoundException。
    
作用：避免重复加载；避免自定义类覆盖系统类
类加载器如何判断两个class是否相同：不仅判断类名，除此还要判断是否为同一类加载器实例加载。
```



#### 9、深拷贝和浅拷贝

```java
浅拷贝：增加一个指针指向已存在的内存地址；
深拷贝：增加一个指针且申请一个新内存；
    
```



#### 10、对象创建方式

```java
1、new创建——调用了构造函数
2、Class的newInstance方法——调用了构造函数   
3、constructor的newInstance方法——调用了构造函数
4、clone方法——未调用构造函数
5、反序列化——未调用构造函数
```

##### 10-1 数组复制效率PK

```java
clone最慢，for次之，copyof第三，arraycopy最快。copyof底层调用arraycopy
```

#### 11、对象创建流程

![image-20210827171421725](https://github.com/younggungun/younggungun/blob/main/images/jvm%E7%9A%84%E5%AF%B9%E8%B1%A1%E5%88%9B%E5%BB%BA%E8%BF%87%E7%A8%8B.png)

1、类加载  2、内存分配方式：堆内存规整-指针碰撞、堆内存不规整-空闲列表；

3、分配内存存在并发问题，解决方法：（1）cas同步，（2）tlab(thread local allocation buffer )

4、内存空间初始化 5、执行init方法

`内存分配方式`:

- 指针碰撞：（用于标记整理）内存一侧为用过的内存，另一侧为空闲内存，用一指针维护分界点，分配内存时将只能向空闲内存移动与待分配大小的位置
- 空闲列表：（用于标记清除）使用过的内存与空闲内存相互交错，维护一个列表纪录空闲内存，分配内存时在列表中找满足大小的内存空间，并更新列表纪录。

#### 12、永久代会发生垃圾回收么

#### 14、JVM调优工具

JDK自带，常用的有jconsole和jvisualvm

jconsole：内存，线程和类等进行监控

jvisualvm：内存快照、线程快照、死锁、内存变化、gc变化等

#### 15、JVM调优参数

- -Xms2g	初始化堆大小2g
- -Xmx2g    堆最大内存2g
- -XX:NewRatio=4    新生代老年代内存比例
- -XX:SurvivorRatio=8  eden与survivor比例8：2
- -XX:+UseParNewGC  ParNew Serial Old组合
- -XX:+UseParallelOldGC  ParNew ParNewOld组合
- -XX:+UseConcMarkSweepGC   CMS Serial Old组合
- -XX:+PrintGC 
- -XX:+PrintGCDetails

#### 16、System.gc()和Runtime.GC()

```java
System.gc()仅仅是一个请求或建议，并不是立即gc，而是对几个垃圾回收算法进行加权，使gc更容易、或更提前、或回收更多。
```

:four_leaf_clover:


#### 16、Minor GC、Major GC、Full GC区别及触发条件

- **Minor GC 触发条件一般为：**
  1. eden区满时，触发MinorGC。即申请一个对象时，发现eden区不够用，则触发一次MinorGC。
  2. 新创建的对象大小 > Eden所剩空间时触发Minor GC
- **Major GC和Full GC 触发条件一般为：**
  `Major GC通常是跟full GC是等价的`
  1. 每次晋升到老年代的对象平均大小>老年代剩余空间
  2. MinorGC后存活的对象超过了老年代剩余空间
  3. 永久代空间不足
  4. 执行System.gc()
  5. CMS GC异常
  6. 堆内存分配很大的对象

#### 17、为什么新生代要分Eden和两个 Survivor 区域？

- Eden区每进行一次Minor GC，存活的对象就会被送到老年代。老年代很快被填满
- 减少被送到老年代的对象，进而减少Full GC的发生
- 解决了碎片化
 
