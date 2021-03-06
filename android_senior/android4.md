## java的GC机制

随着程序的运行，内存中的实例对象、变量等占据的内存越来越多，如果不及时进行回收，会降低程序运行效率，甚至引发系统异常。

五个内存区域中，有3个是不需要进行垃圾回收的：本地方法栈、程序计数器、虚拟机栈。
因为他们的生命周期是和线程同步的，随着线程的销毁，他们占用的内存会自动释放。所以，只有方法区和堆区需要进行垃圾回收，回收的对象就是那些不存在任何引用的对象。


## 一.垃圾查找算法(判断对象是否是垃圾)；


1. 引用计数算法

每个对象添加到引用计数器，每被引用一次，计数器+1，失去引用，计数器-1，当计数器在一段时间内为0时，即认为该对象可以被回收了。

但是这个算法有个明显的缺陷：当两个对象相互引用，但是二者都已经没有作用时，理应把它们都回收，但是由于它们相互引用，不符合垃圾回收的条件，所以就导致无法处理掉这一块内存区域。

因此，Sun的JVM并没有采用这种算法，而是采用一个叫——<font size=4 >根搜索算法</font>

2. 根搜索算法

基本思想是：从一个叫GC Roots的根节点出发，向下搜索，如果一个对象不能达到GC Roots的时候，说明该对象不再被引用，可以被回收。如上图中的Object5、Object6、Object7，虽然它们三个依然相互引用，但是它们其实已经没有作用了，这样就解决了引用计数算法的缺陷。


![根搜索算法](imgs/gc_root.png)

## 二. 四种引用类型

1. java的四种引用类型

```
（1）强引用（Strong Reference）：如“Object obj = new Object（）”，这类引用是Java程序中最普遍的。只要强引用还存在，垃圾收集器就永远不会回收掉被引用的对象。

  （2）软引用（Soft Reference）：它用来描述一些可能还有用，但并非必须的对象。在系统内存不够用时，这类引用关联的对象将被垃圾收集器回收。JDK1.2之后提供了SoftReference类来实现软引用。

  （3）弱引用（Weak Reference）：它也是用来描述非须对象的，但它的强度比软引用更弱些，被弱引用关联的对象只能生存到下一次垃圾收集发生之前。当垃圾收集器工作时，无论当前内存是否足够，都会回收掉只被弱引用关联的对象。在JDK1.2之后，提供了WeakReference类来实现弱引用。

  （4）虚引用（Phantom Reference）：最弱的一种引用关系，完全不会对其生存时间构成影响，也无法通过虚引用来取得一个对象实例。为一个对象设置虚引用关联的唯一目的是希望能在这个对象被收集器回收时收到一个系统通知。JDK1.2之后提供了PhantomReference类来实现虚引用。

```

## 三. java的内存分配机制
[参考博客,这个人真的吊](https://www.jianshu.com/p/5261a62e4d29)

这里所说的内存分配，主要指的是在堆上的分配，一般的，对象的内存分配都是在堆上进行，但现代技术也支持将对象拆成标量类型（标量类型即原子类型，表示单个值，可以是基本类型或String等），然后在栈上分配，在栈上分配的很少见，我们这里不考虑。


Java内存分配和回收的机制概括的说，就是：分代分配，分代回收。

对象将根据存活的时间被分为：年轻代（Young Generation）、年老代（Old Generation）、永久代（或者是持久代）（Permanent Generation，也就是方法区）

1. 年轻代

```
几乎所有新生成的对象首先都是放在年轻代的。

新生代内存按照8:1:1的比例分为一个Eden区（伊甸园区）和两个Survivor(Survivor0,Survivor1)区。

大部分对象在Eden区中生成。

当新对象生成，Eden区申请失败（因为空间不足等），则会发起一次GC(Gcavenge GC)。

回收时先将Eden区存活对象复制到一个Survivor0区，然后清空Eden区，
当这个Survivor0区也存放满了时，则将Eden区和Survivor0区存活对象复制到另一个Survivor1区，
然后清空Eden和这个Survivor0区，此时Survivor0区是空的，
然后将Survivor0区和Survivor1区交换，即保持Survivor1区为空， 如此往复。

当Survivor1区不足以存放 Eden和Survivor0的存活对象时，就将存活对象直接存放到老年代。

当对象在Survivor区躲过一次GC的话，其对象年龄便会加1，默认情况下，如果对象年龄达到15岁，就会移动到老年代中。

若是老年代也满了就会触发一次Full GC，也就是新生代、老年代都进行回收。
```


2. 年老代

```
在年轻代中经历了N次垃圾回收后仍然存活的对象，就会被放到年老代中。
因此，可以认为年老代中存放的都是一些生命周期较长的对象。

年老代内存比新生代也大很多(大概比例是1:2)，当老年代内存满时触发Major GC即Full GC，
Full GC发生频率比较低，老年代对象存活时间比较长，存活率标记高。

一般来说，大对象会被直接分配到老年代。所谓的大对象是指需要大量连续存储空间的对象，最常见的一种大对象就是大数组。
例如

```

```java
//这种一般会直接在老年代分配存储空间。
//当然分配的规则并不是百分之百固定的，
//这要取决于当前使用的是哪种垃圾收集器组合和JVM的相关参数。
 byte[] data = new byte[4*1024*1024];

```

3. 持久代

```
用于存放静态文件（class类、方法）和常量等。持久代对垃圾回收没有显著影响，但是有些应用可能动态生成或者调用一些class，例如Hibernate 等，在这种时候需要设置一个比较大的持久代空间来存放这些运行过程中新增的类。对永久代的回收主要回收两部分内容：废弃常量和无用的类。

```

4. 堆内存分配策略明确以下三点：

（1）对象优先在Eden分配。

（2）大对象直接进入老年代。

（3）长期存活的对象将进入老年代。

## 四. 垃圾收集器

## HotSpot（JDK 7)虚拟机提供的几种垃圾收集器

```
1.Serial（SerialMSC）（Copying算法）

      Serial收集器是最基本最古老的收集器，它是一个单线程收集器，并且在它进行垃圾收集时，必须暂停所有用户线程。Serial收集器是针对新生代的收集器，采用的是Copying算法。

2.Serial Old （标记—整理算法）

      Serial Old收集器是针对老年代的收集器，采用的是Mark-Compact算法。它的优点是实现简单高效，但是缺点是会给用户带来停顿。

2.ParNew （Copying算法）

      ParNew收集器是新生代收集器，Serial收集器的多线程版本。使用多个线程进行垃圾收集，在多核CPU环境下有着比Serial更好的表现。

3.Parallel Scavenge （Copying算法）

      Parallel Scavenge收集器是一个新生代的多线程收集器（并行收集器），它在回收期间不需要暂停其他用户线程，其采用的是Copying算法，该收集器与前两个收集器有所不同，它主要是为了达到一个可控的吞吐量。追求高吞吐量，高效利用CPU。吞吐量一般为99%。 吞吐量= 用户线程时间/(用户线程时间+GC线程时间)。适合后台应用等对交互相应要求不高的场景。

4.Parallel Old（ParallelMSC）（标记—整理算法）

      Parallel Old是Parallel Scavenge收集器的老年代版本（并行收集器），使用多线程和Mark-Compact算法。吞吐量优先。

5.CMS  （标记—整理算法）

      CMS（Current Mark Sweep）收集器是一种以获取最短回收停顿时间为目标的收集器，它是一种并发收集器，采用的是Mark-Sweep算法。高并发、低停顿，追求最短GC回收停顿时间，CPU占用比较高。响应时间快，停顿时间短，多核CPU 追求高响应时间的选择。

6.G1

      G1收集器是当今收集器技术发展最前沿的成果，它是一款面向服务端应用的收集器，它能充分利用多CPU、多核环境。因此它是一款并行与并发收集器，并且它能建立可预测的停顿时间模型。

      G1垃圾回收器适用于堆内存很大的情况，他将堆内存分割成不同的区域，并且并发的对其进行垃圾回收。G1也可以在回收内存之后对剩余的堆内存空间进行压缩。并发扫描标记垃圾回收器在STW情况下压缩内存。G1垃圾回收会优先选择第一块垃圾最多的区域
```

