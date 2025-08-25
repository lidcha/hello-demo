
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

    进程是程序的一次执行过程，是系统运行的基本单位，而线程与进程相似，但是是比进程更小的一个执行单位，一个进程中可产生多个线程。
    线程和进程最大的不同在于基本上各进程是独立的，而各线程则不一定，同进程中的线程有可能会互相影响。
    
    Java内存区域如下图所示：

    ![alt text](image-4.png)

    Java内存区域在JVM中可以大致分为线程私有和线程共享两部分，
    - 线程私有部分包括：
        - 程序计数器：保存当前字节码执行位置，多线程情况记录当前线程执行的位置，从而线程被切换回来的时候能知道该线程运行到哪里了；它是私有的主要是为了线程切换后能回复到正确的位置。
        - 虚拟机栈：每个Java方法执行前都会创建一个栈帧来存储局部变量表、操作数栈、常量池引用等信息，方法调用直到执行完，对应栈帧入栈和出栈的过程。
        - 本地方法栈：和虚拟机栈类似，只不过适用于native方法。
        - 这两个栈是线程私有的主要是为了保证线程中的局部变量不被别的线程访问到。
    - 线程共享部分包括
        - 堆
        - 方法区/元空间
        - 运行时常量池。

    **Java内存模型**

    java内存模型是共享内存的并发模型，线程之间主要通过读-写共享变量来完成隐式通信。

    创建线程可以通过继承Thread类、实现Runnable接口、实现Callable接口、线程池、使用CompletableFuture类等。

    Java线程的状态在Thread.State枚举类里有定义，一共有：NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING和TERMINATED。
    ![alt text](image-6.png)
    - NEW：初始状态，线程对象被创建，但是未调用start()方法；
    - RUANNABLE：运行状态 ，线程被调用了start()方法后；
    - BLOCKED: 阻塞状态，例如线程等待获取synchronized锁；
    - WAITING: 等待状态，直到被其他线程显示唤醒（如notify()通知或中断）；
    - TIME_WAITING: 超时等待，超过指定时间后自动返回；
    - TERMINATED: 终止状态，表示该线程已经运行完毕或异常退出；

    Q: 线程间的状态是怎么流转的？

    A: 从NEW -> RUNNABLE（初始态 到 运行态，通过调用线程对象的start()方法）；从RUNNABLE -> BLOCKED（从运行态 到 阻塞态，线程尝试获取synchronized锁失败）；从RUNNABLE -> WAITING（从运行态 到 等待态，通过调用Object.wait() / LockSupport.park()，进入无限等待）；从 RUNNABLE -> TIMED_WAITING（运行态 到 超时等待，通过调用sleep(ms) / join(ms) / wait(ms) 进入超时等待）；从WAITING/TIMED_WAITING -> RUNNABLE（从等待/超时等待 到 运行态，通过调用notify() / notifyAll() / unpark()唤醒）；BLOCKED -> RUNNABLE ( 从阻塞 到 运行，获取锁后 )；RUN -> TERNIMATED（run()执行完毕或者抛异常结束）。

    PS：实际回答时不用所有流转都打出来，只需要简单陈述6种状态含义后，提及从NEW -> RUNNABLE、RUNNABLE -> BLOCKED、RUNNABLE -> WAITING/TIMED_WAITING、RUNNABLE -> TERMINATED是怎么流转的即可。

    Q: join()方法的底层原理是什么？

    A: join()方法本质上是等待另一个线程结束，底层调用了wait()方法，线程A调用ThreadB.join()方法，会让A进入WAITING/TIMED_WAITING状态，直到线程B结束或中断。

    Q: join()会释放锁吗？

    A: 

    三个线程交替打印的实现如下：
    ```
    public class ThreeThreadWaitNotify {
    private static final Object lock = new Object();
    private static int state = 0; // 0 -> A, 1 -> B, 2 -> C
    private static final int times = 5; // 每个线程打印次数

    public static void main(String[] args) {
        Thread threadA = new Thread(() -> {
            for (int i = 0; i < times; i++) {
                synchronized (lock) {
                    while (state % 3 != 0) { // 不该 A 打印就等待
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                    System.out.println("A");
                    state++;
                    lock.notifyAll(); // 唤醒其他线程
                }
            }
        });

        Thread threadB = new Thread(() -> {
            for (int i = 0; i < times; i++) {
                synchronized (lock) {
                    while (state % 3 != 1) {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                    System.out.println("B");
                    state++;
                    lock.notifyAll();
                }
            }
        });

        Thread threadC = new Thread(() -> {
            for (int i = 0; i < times; i++) {
                synchronized (lock) {
                    while (state % 3 != 2) {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                    System.out.println("C");
                    state++;
                    lock.notifyAll();
                }
            }
        });

        threadA.start();
        threadB.start();
        threadC.start();
    }
}

    ```

    **线程池的参数**
    
    - corePoolSize: 核心线程数。线程池会保持这些线程，即使线程是空闲的。
    - 

3.  Thredlocal的原理，为什么容易内存泄漏
4.  Syschronized和Lock的区别，AQS的原理，volatile
5.  JVM的原理，垃圾回收，分代收集的算法，各种问题怎么排查（频繁Full GC，内存占用大，OOM等），JVM各种参数的优化。
6.  CMS, Parralel, G1等垃圾回收器的原理
7.  JAVA类加载的过程与这样做的原因，有没有办法打破双亲委派，Class这个类在哪一块区域
8.  Spring中Bean的生命周期，Spring MVC的流程，Spring IOC和AOP的原理，为什么动态代理必须基于接口？
9.  各种Collection的原理，Hashmap，Hashtable，ArrayList，LinkedList，Hashset，Treemap，ConcurrentHashMap（重点，扩容机制，写入机制，新旧版本对比（阻塞队列等））

    **CopyOnWriteArrayList**
    arrylist是非线程安全的， 如果要使用线程安全的list，可以考虑CopyOnWriteArrayList，其读操作时完全无需加锁的，且写操作也不会阻塞读操作，只有写写才会互斥，这样保证读操作性能可以大幅度提升。其核心思想主要是采用了Copy-On-Write（写时复制）的策略。

    当需要修改（add，set，remove等操作）CopyOnWriteArrayList的内容时，不会直接修改原数组，而是先创建底层数组副本，对副本数组进行修改，修改完后再赋值回去。适合读多写少的并发场景。缺点是：内存占用比较大，每次都需要赋值；写操作开销比较大，写入频繁的场景下，性能可能会受到影响；存在一定程度上的数据一致性问题。

    CopyOnWriteArrayList内部使用的是ReetrantLock来加锁的。

    **HashMap**

    HashMap主要用来存放键值对，它是基于哈希表实现，是非线程安全的，可以存储null的key和value但是null作为key只能有一个，null作为value可以有多个。

    HashMap在JDK1.8之前是由数组+链表组成的，链表主要为了解决哈希冲突。在1.8之后，HashMap在解决哈希冲突时有了较大变化，当链表长度大于等于阈值后，会将链表转为红黑树（转红黑树前会判断，如果当前数组长度小于64，会先进行数组扩容，而不是直接转换红黑树），以减少搜索时间。

    hashMap中通过hash & (n-1) 来获取元素在数组中的位置原因主要是效率和均匀分布，按位与操作直接在二进制位上进行的，速度更快且分布更均匀，这样相当于取hash值的低位 去 跟 数组位置去操作。

    put()的过程：

    HashMap通过key的hash方法获取得到hash值，然后通过（n-1）& hash 判断当前元素存放的位置，如果当前位置存在元素，就判断该元素是否与要存入的元素的hash值及key是否相同，如果相同的话，直接覆盖，不相同的话通过拉链法解决冲突。而jdk1.8之后，会先根据hashMap数组来决定是否转换为红黑树，否则只是执行resize()方法对数组扩容。

    resize()过程：

    HashMap通过调用resize()进行扩容，扩容会伴随着重新hash分配，并且会遍历hash表中的所有元素，是比较耗时的。
    
    HashMap在元素数量超过阈值（容量 * 负载因子，默认0.75）时会扩容，一般是翻倍容量。扩容时会把原来的节点迁移到新数组，JDK1.8优化了扩容过程，只需要根据hash值的新增的那个bit是1还是0来判断元素是否需要移动到新位置，不必重新计算hash。但是扩容时很耗时的，而且在并发情况下可能出问题。

    为什么hashMap是线程不安全的？

    主要是：
    - 多个线程同时进行put(),可能同时我那个同一个桶里写数据，导致数据覆盖丢失的情况
    - 扩容过程不是原子操作，在JDK1.7的链表头插法迁移里，如果两个线程同时扩容，可能导致环形链表，虽然在1.8中改成了尾插法，避免了环，但依然可能因为并发迁移导致数据覆盖丢失。
    - 总结来说主要是因为内部没有加锁控制，多个线程同时put或扩容时会互相覆盖数据。


    **ConcurrentHashMap**

    ConcurrentHashMap保证线程安全在Jdk1.7是由分段锁来实现的，整个Map被分成多个Segment，每个Segment维护一部分桶，put/get操作只锁定某一个segment，相当于锁分段，但是确定是Segment是固定数量的，并发度有限。

    ConcurrentHashMap相对于HashTable来讲，虽然二者都是线程安全的，且底层数据结构都比较类似，但是HashTable是的锁粒度比较粗，是方法级别的synchronized，并发性能低；而ConcurrentHashMap锁粒度会相对更细，并发度和性能都会更好。

    而在jdk1.8则抛弃了Segment，改用CAS + synchronized 的方式来实现，锁粒度更小，只锁住某个桶的头节点，同时还支持链表转红黑树和多线程扩容。
    - 插入时，先尝试用CAS把同位置设置成新节点，如果成功，就不用加锁；失败说明有竞争，进入锁逻辑。
    - synchronized锁定链表/红黑树的头节点，这样对某个桶的链表或树加锁，不会影响整个表，避免大面积锁竞争。
    
    put时要用synchronized，而不是完全依赖CAS，主要是由于CAS适合单变量更新，不适合复杂逻辑，在put操作中，不仅要新写入节点，还涉及例如遍历桶链表查重、链表转红黑树等一系列操作；另外CAS在冲突激烈时效率低，如果很多线程同时竞争同一个桶位置，CAS失败率高，就会反复自旋重试，性能反而下降，而synchronized反而会更高效点，而且只锁住桶头节点，即使加锁了也能保证并发度。

    在扩容时，ConcurrentHashMap不是单线程完成的， 而是允许多线程协作，当一个桶正在迁移时，会用forwardNode（持有指向新数组的引用）占位，其他线程访问时就知道该桶已经迁移过了，会直接去新表；扩容任务会被分成多个小段，多个线程通过CAS抢任务并行迁移；在迁移具体桶时会用synchronized保证链表/树的安全。

    如果你来涉及一个高并发的线程安全的HashMap，你怎么做？

    首先需要明确设计目标：线程安全、高并发性能、可扩容性。在实现方式上，可以采用分段锁或桶级别的锁 + CAS来减少竞争，同时在扩容时用协作迁移的方式保证数据一致性。如果是读多邪少的场景，还可以考虑Copy-On-Write（写操作时复制一份新数组，修改后替换旧数组；读操作不加锁）；如果追求极致性能，可以尝试无锁化方案（CAS）但是可能实现复杂。jdk1.8中的ConcurrentHashMap其实就是一个挺不错的这种方案，既利用CAS乐观锁提升并发性能，又用synchronized保证局部互斥。

    CAS是线程安全的主要是因为它是一个原子操作，而不是我们常规理解的先比较后交换，这种两步的情况。是由CPU保证的，但是可能会出现“ABA”问题，变量初始是A，线程A读到变量A，准备CAS，线程B把变量由A改成B，由改为了A，线程A执行CAS发现还是A，于是成功了，但其实值被改过，解决方法是可以给值加上版本号，变成（值，版本号）一起比较。

    ConcurrentHashMap中的size()操作只是近似的值，不能保证完全精确，如果要保证完全精确，可以考虑自己维护一个独立的计数器，保证实时一致。

10. String, StringBuffer, StringBuilder的区别，String不可变有什么好处？
11. NIO, BIO, AIO
12. 虚拟线程
13. SPI和API有什么区别？
    SPI将服务接口和具体的服务实现分离开来，将服务调用方和实现放解耦。
    ![alt text](image-3.png)
    
    SPI相当于接口存在调用方这边，由接口调用方确定接口规则，然后由不同的厂商根据这个规则对接口实现；而
    
    API是指实现方或者说是服务提供者提供了接口和实现。

    SLF4j就是SPI实现的一个经典示例。

14. ArrayBlockingQueue

    **ArrayBlockingQueue**是一个阻塞队列，底层采用数组实现，是一个长度/容量优先的队列，一旦创建，容量不能改变。为了保证线程安全，其并发控制使用可重入锁ReentrantLock，不管是读取还是插入操作，都需要先获取锁再操作。

    - 内部维护一个定长的数组用于存储uansu。
    - 使用ReentrantLock锁来实现线程安全。
    - 通过Condition实现线程间的等待和唤醒操作。例如当队列已满时，生产者线程会调用notFull.await()方法让生产者线程进行等待，直到消费者线程从队列去出元素后，通过notFull.singal()唤醒生产者线程；当队列为空时，消费者线程会调用notEmpty。await()来让消费者线程等待，知道新的uansu被添加后，生产者线程调用notEmpt().singal()唤醒消费者线程。

15. DelayQue延迟队列

    DelayQueue是JUC包提供的一个延迟队列的实现，用于实现延时任务。它也是阻塞队列的一种，底层是基于PriorityQueue优先队列实现的一个无界队列，是线程安全的。默认按照到期时间升序编排任务，只有当元素到期时，才能从队列中去除。

    DelayQueue实现原理：

    DelayQueue底层是使用优先队列来存储元素，而优先队列采用小顶堆的思想确保值小的元素排在最前面，这就使得DelayQueue对于延迟任务优先级的管理变得非常方便了。同时DelayQueue为了保证线程安全还用到了可重入锁ReentrantLock，确保单位时间内只有一个线程可以操作延迟队列。最后为了实现多线程间的等待和唤醒交互，DelayQueue还用到了Condition，通过Condition的await和singal方法完成线程间的等待唤醒。

    使用时通过实现Delayed接口来定义一个延迟任务，然后将延迟任务实例化后提交到DelayQueue中。