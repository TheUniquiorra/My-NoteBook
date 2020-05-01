# Java技术总结
## 多线程
### 线程和进程的区别与联系
1. 进程：是资源分配和调度的基本单位，是操作系统结构的基础。同时进程也是线程的容器。进程是程序的实体。
2. 线程：是操作系统能够运行调度的基本单位，线程包含在进程中，是进程中的实际运行单位。一个线程指的是进程中一个单一的顺序控制流。
### 线程的几种状态
1. 初始化
2. 运行状态
   * Runnable：可运行状态，获取到CPU时间片后就可以执行。
   * Running：线程获取到了对应的时间片，线程执行中。
3. 阻塞状态
4. 等待状态
   * WAITING（无时限等待）
   * TIMED_WAITING（有时限等待）
5. 终止状态
### 线程池
1. 什么是线程池

线程池是一种多线程处理形式，处理过程中将任务提交到线程池，任务的执行由线程池来进行管理。如果每次请求都创建一个线程去处理，那么服务器的资源很快就会被耗光，使用线程池可以减少创建和销毁线程的次数，每个工作线程都可以重复使用，可以执行多个任务。

2. 为什么使用线程池

减少创建和销毁线程所需要的资源。

3. 线程池的作用
   线程池的作用就是限制系统中执行的线程的数量。
   1. 提高效率，创建好一定数量的线程，需要的时候直接去取就可以了，不需要再去创建线程。
   2. 方便管理，可以编写对线程池管理代码对池中的线程进行统一管理。

4. 说说常见的线程池和使用场景
    1. newSingleThreadExecutor

       创建一个单线程的线程池，这个线程池中只有一个线程在工作，相当于所有的任务都是串行执行的。如果这个线程出现异常，会有一个新的线程来代替这个线程,
    2. newFixedThreadPool

       创建一个固定大小的线程池，每次提交一个任务就创建一个线程，直到线程数达到最大。线程池一旦到达最大就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新的线程。
    3. newCachedThreadPool

       创建一个可缓存的线程池，如果线程池的大小超过了任务处理所需要的线程，那么就会回收部分空闲的（60秒不执行任务）线程，当任务数增加的时候，此线程池又可以智能的添加线程来处理任务，此线程池不会对线程池大小做限制，线程池的大小完全依赖操作系统能够创建的最大线程数。
    4. newSchedualedThreadPool
       创建一个大小无限制的线程池，此线程池支持定时以及周期性任务执行的需求。
5. 什么不建议使用 Executors静态工厂构建线程池
阿里巴巴Java开发手册，明确指出不允许使用Executors静态工厂构建线程池
原因如下：
线程池不允许使用Executors去创建，而是通过ThreadPoolExecutor的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险

说明：Executors返回的线程池对象的弊端如下：

1：FixedThreadPool 和 SingleThreadPool：
允许的请求队列（底层实现是LinkedBlockingQueue）长度为Integer.MAX_VALUE，可能会堆积大量的请求，从而导致OOM

2：CachedThreadPool 和 ScheduledThreadPool
允许的创建线程数量为Integer.MAX_VALUE，可能会创建大量的线程，从而导致OOM。

创建线程池的正确姿势

避免使用Executors创建线程池，主要是避免使用其中的默认实现，那么我们可以自己直接调用ThreadPoolExecutor的构造函数来自己创建线程池。在创建的同时，给BlockQueue指定容量就可以了。

``` Java
private static ExecutorService executor = new ThreadPoolExecutor(10, 10,
        60L, TimeUnit.SECONDS,
        new ArrayBlockingQueue(10));
```
6. 线程池常用参数
corePoolSize：核心线程数量，会一直存在，除非allowCoreThreadTimeOut设置为true
maximumPoolSize：线程池允许的最大线程池数量
keepAliveTime：线程数量超过corePoolSize，空闲线程的最大超时时间
unit：超时时间的单位
workQueue：工作队列，保存未执行的Runnable 任务
threadFactory：创建线程的工厂类
handler：当线程已满，工作队列也满了的时候，会被调用。被用来实现各种拒绝策略。

7. 线程池的拒绝策略
   当请求任务不断的过来，而系统此时又处理不过来任务的时候,我们需要采取拒绝服务。RejectedExecutionHandle接口提供了拒绝任务处理的自定义的方法。在ThreadPoolExecutor中以及包含了四种处理策略。
   * AbortPolicy：会直接抛出异常，阻止系统正常工作。
   * CallerRunsPolicy:只要线程池未关闭，该策略直接在调用者线程中，运行当前被丢弃的任务。
   * DiscardOleddestPolicy:该策略将丢弃最老的一个请求，也就是即将被执行的任务，并尝试再次提交任务。
   * DiscardPolicy策略：该策略默默的丢弃无法处理的任务，不予任何处理。
8. executor和submit的区别
   * executor用在不需要关注返回值的场景
   * submit用在需要关注返回值的场景
9. 线程池的关闭
   * shutdownNow：对于全部执行的线程发出interrupt()方法
   * shutdown:当执行shutdown后，线程池不会在接收新的任务，但是也不会中断当前执行的任务。
10. 初始化线程池的时候线程数的选择
    *　如果是ＩＯ密集型，一般线程设置为２＊CＰＵ数
    *　如果是CＰＵ密集型，一般线程设置为CＰＵ数＋１
11. 线程池的工作队列有哪些
    1. ArrayBlockingqueue

    是一个基于数组的有界阻塞队列，此队列按FIFO原则对元素

    2. LinkedBlockingQueue

    是一个基于双向链表的阻塞队列，吞吐量t通常高于ArrayBlockingQueue,newFixedthreadpool使用的就是该阻塞队列

    3. SynchronousQueue

    一个不存储元素的阻塞队列，每个插入操作必须等另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常高于 LinkedblockingQueue,

    4. PriorityBlockingQueue

    一个具有优先级的无限阻塞队列

12. synchronized底层原理
    1. 修饰代码块
        使用monitorenter和monitorexit指令。
        * monitorenter

          每个对象都有一个监视器锁（monitor）。当monitor被占用时就会处于锁定状态，线程执行 monitorenter时会尝试获取monitor的所有权。过程如下：
          1. 如果monitor进入数为0,则该线程进入monitor,然后将进入数设置成1,该线程就是monitor的拥有者。
          2. 如果该线程已经占有monitor，只是重新进入，则进入monitor的进入数加1.
          3. 如果其他线程已经占用了monitor，则该线程就会进入阻塞状态，知道monitor的进入数为0,在重新尝试获取monitor的所有权。

        *　monitorexit

          执行monitorexit的线程必须是objectref所对应monitor的所有者，

          执行指令的时候,monitor进入数减1,如果减1后进入数为0,则线程退出monitor，不再是这个monitor的持有者，其他被这个monitor阻塞的线程可以尝试再去获取monitor的所有权。
     2. 修饰方法

        相对于普通的方法，常量池中多了ACC_SYNCHRONIZED 标识符。JVM就是根据该标识符来实现方法的同步，当方法调用的时候，调用指令将会检查方法的ACC_SUNCHRONIZED标识符是否被设置，如果设置了，执行线程将先获取monitor，获取成功后再去执行方法，方法执行完成后在进行释放，在方法执行期间，其他的线程是无法获取monitor对象的。

13. synchronized和volatile的区别
    1. synchronized可以修饰变量、方法和代码块，volatile只能修饰变量
    2. synchronized能够保证变量修改的原子性和可见性，volatile只能保证变量修改的可见性，不能保证原子性。
    3. synchronized可能会造成线程的阻塞，volatile不会造成线程的阻塞。
    4. volatile修饰的变量不会被编译器优化，synchronized修饰的变量会被编译器优化。
    5. volatile本质上是告诉JVM当前变量在寄存器中的值是不确定的，需要从主存中获取，synchronized则是锁定当前变量，只有当前线程可以访问，其他线程被阻塞。

14. synchronized 和 Lock 有什么区别？
    1. 原始构成
       * synchronized是JVM层面的
       * lock是Java Api层面的
    2. 使用方法
       * synchronized不需要手动释放锁
       * lock需要手动释放锁
    3. 等待是否可以中断
       * synchronized不可中断，除非抛出异常或者正常执行完毕
       * ReentrantLock可以中断
         1. 设置超时方法 tryLock(long timeout,TimeUnit unit)
         2. 2.lockInterruptibly()放代码块中，调用interrupt（）方法可中断
    4. 加锁是否公平
       * synchronized非公平锁
       * ReentrantLock两者都可以，默认非公平锁
    5. 锁绑定多个条件Condition
       * synchronized没有
       * ReentrantLock用来实现分组唤醒需要唤醒的线程，可以精确唤醒，而不是像synchronized要么随机唤醒一个要么唤醒全部线程。

15. synchronized 和 ReentrantLock 区别是什么？
    1. synchronized 竞争锁时会一直等待；ReentrantLock 可以尝试获取锁，并得到获取结果
    2. synchronized 获取锁无法设置超时；ReentrantLock 可以设置获取锁的超时时间
    3. synchronized 无法实现公平锁；ReentrantLock 可以满足公平锁，即先等待先获取到锁
    4. synchronized 控制等待和唤醒需要结合加锁对象的 wait() 和 notify()、notifyAll()；ReentrantLock 控制等待和唤醒需要结合 Condition 的 await() 和 signal()、signalAll() 方法
    5. synchronized 是 JVM 层面实现的；ReentrantLock 是 JDK 代码层面实现
    6. synchronized 在加锁代码块执行完或者出现异常，自动释放锁；ReentrantLock 不会自动释放锁，需要在 finally{} 代码块显示释放

## 反射
1. 什么是反射

   Java反射机制是指在程序运行过程中可以构造任意一个类的对象，可以了解任意一个对象所属的类，可以了解任意一个类的成员变量和方法，可以调用任意一个对象的属性和方法。

2. 什么是Java反序列化

Java反序列化是指将Java对象转换成字节流的对象，序列化是将子节流转换成Java对象。当Java需要在网络中传输对象或者持久化存储到文件中时，就需要反序列化处理。

3. 什么是动态代理

   动态代理是指在程序运行过程中
## JVM虚拟机
1. Java堆和栈的区别
   * Java栈中存放的是局部变量和方法调用，堆中存放的是Java中的对象，
   * 栈属于单个线程，而堆内存中的对象是所有线程可见的。

## Java类型
1. 强引用、软引用、弱引用
   * 强引用：Java虚拟机不会回收该对象
   * 软引用：如果JVM内存空间足够，JVM就不会回收该对象，如果不足会回收该对象
   * 弱引用：弱引用的生命周期不会存活到下一次GC后，不管当前内存空间是否足够，JVM一定会进行回收
