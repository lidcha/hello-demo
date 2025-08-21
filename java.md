
## JAVA

1.  Equals,hashcode方法，equals方法满足哪些特性，hashcode用在了哪些地方，Hashmap中的hashcode怎么用的

    一般定义对象时，需要同时重写equals和hashCode方法，如果不同时重写，在往hashMap中存放对象时，可能会出现相同对象都放进去的情况。

    equals方法用来判断两个对象是否相等，通常在类中重写它。equals方法满足以下几个特性：

    - 自反性：对于任何非空引用x，x.equals(x)应该返回true。
    
    - 对称性：如果x.equals(y)返回true，那y.equals(x)也应该返回true。

    - 传递性：x.equals(y)和y.equals(z)都返回true，那z.equals(x)也返回true。

    - 一致性：如果两个对象多次调用equals()方法在没有修改对象的情况下，结果应该始终一致。

    - 非空性：任何对象与null比较时，equals()应该返回false。

    hashCode方法用于返回对象的哈希值，通常用作哈希表中bucketn的索引。

    hashCode和equals方法的关系：
    
    - 如果两个对象根据equals()比较返回true，那它们的hashcode必须相同。

    - 如果两个对象的hashcode相同，equals()不一定返回true（哈希冲突）。

    - 如果没有重写equals()时，hashcode使用的是Object类的默认实现，通常是对象的内存地址。

    在HashMap中，hashCode()用于确定对象存放在数组中的哪个位置。具体步骤如下：
    - HashMap内部有一个数组，数组中每个元素是一个链表或者红黑树（JDK8后）。
    - 当一个键值对被插入时，hashMap计算key的hashCode，根据这个hashCode定位到bucket位置。
    - 如果发生hash冲突，那会将这些键值放入到该bucket的链表或者红黑树中。
    - 判断key是否相同时，先计算hashcode，如果hashcode相同，则通过equals方法进一步检查是否相等。    

2.  线程池的参数，有哪些线程池类型，线程池的各种抛弃策略适合哪些场景，线程池的原理
3.  Thredlocal的原理，为什么容易内存泄漏
4.  Syschronized和Lock的区别，AQS的原理，volatile
5.  JVM的原理，垃圾回收，分代收集的算法，各种问题怎么排查（频繁Full GC，内存占用大，OOM等），JVM各种参数的优化。
6.  CMS, Parralel, G1等垃圾回收器的原理
7.  JAVA类加载的过程与这样做的原因，有没有办法打破双亲委派，Class这个类在哪一块区域
8.  Spring中Bean的生命周期，Spring MVC的流程，Spring IOC和AOP的原理，为什么动态代理必须基于接口？
9.  各种Collection的原理，Hashmap，Hashtable，ArrayList，LinkedList，Hashset，Treemap，ConcurrentHashMap（重点，扩容机制，写入机制，新旧版本对比（阻塞队列等））
10. String, StringBuffer, StringBuilder的区别，String不可变有什么好处？
11. NIO, BIO, AIO
12. 虚拟线程
13. SPI和API有什么区别？
    SPI将服务接口和具体的服务实现分离开来，将服务调用方和实现放解耦。
    ![alt text](image-3.png)
    
    SPI相当于接口存在调用方这边，由接口调用方确定接口规则，然后由不同的厂商根据这个规则对接口实现；而
    
    API是指实现方或者说是服务提供者提供了接口和实现。

    SLF4j就是SPI实现的一个经典示例。



