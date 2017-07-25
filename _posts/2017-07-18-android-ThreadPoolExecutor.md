---
layout: post
title: android线程池——ThreadPoolExecutor理解与使用
tags:
- android
categories: android
---
使用线程池的好处可以归纳为3点：

 1. 重用线程池中的线程， 避免因为线程的创建和销毁所带来的性能开销.
 2. 有效控制线程池中的最大并发数，避免大量线程之间因为相互抢占系统资源而导致的阻塞现象.
 3. 能够对线程进行简单的管理，可提供定时执行和按照指定时间间隔循环执行等功能.
 
ThreadPoolExecutor是Executors类的底层实现。android中线程池的概念来源于java中的Executor,线程池真正的实现类是ThreadPoolExecutor，它间接实现了Executor接口。ThreadPoolExecutor提供了一系列参数来配置线程池，通过不同的参数配置实现不同功能特性的线程池，android中的Executors类提供了4个工厂方法用于创建4种不同特性的线程池给开发者用.
android中的线程池都是直接或是间接通过配置ThreadPoolExecutor来实现的
```java
public class Executors {
    ...
    public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                              0L, TimeUnit.MILLISECONDS,
                              new LinkedBlockingQueue<Runnable>());
    }
    ...
}
```
ThreadPoolExecutor 的配置参数
```java
public ThreadPoolExecutor(int corePoolSize,
                      int maximumPoolSize,
                      long keepAliveTime,
                      TimeUnit unit,
                      BlockingQueue<Runnable> workQueue,
                      ThreadFactory threadFactory,
                      RejectedExecutionHandler handler) {
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}

corePoolSize: 线程池的核心线程数，默认情况下， 核心线程会在线程池中一直存活， 即使处于闲置状态. 但如果将allowCoreThreadTimeOut设置为true的话, 那么核心线程也会有超时机制， 在keepAliveTime设置的时间过后， 核心线程也会被终止.
maximumPoolSize: 最大的线程数， 包括核心线程， 也包括非核心线程， 在线程数达到这个值后，新来的任务将会被阻塞.
keepAliveTime: 超时的时间， 闲置的非核心线程超过这个时长，讲会被销毁回收， 当allowCoreThreadTimeOut为true时，这个值也作用于核心线程.
unit：超时时间的时间单位.
workQueue：线程池的任务队列， 通过execute方法提交的runnable对象会存储在这个队列中.
threadFactory: 线程工厂, 为线程池提供创建新线程的功能.
handler: 任务无法执行时，回调handler的rejectedExecution方法来通知调用者.
```

### ThreadPoolExecutor的执行过程

 - 如果运行的线程少于 corePoolSize，则 Executor 始终首选添加新的线程，而不进行排队。（什么意思？如果当前运行的线程小于corePoolSize，则任务根本不会存放，添加到queue中，而是直接抄家伙（thread）开始运行）
 - 如果运行的线程等于或多于 corePoolSize，则 Executor 始终首选将请求加入队列，而不添加新的线程。
 - 如果无法将请求加入队列，则创建新的线程，除非创建此线程超出 maximumPoolSize，在这种情况下，任务将被拒绝。

用currentSize表示线程池中当前线程数量，将上述过程可以表示如下
 1. 当currentSize < corePoolSize时，直接启动一个核心线程并执行任务。
 2. 当currentSize>=corePoolSize、并且workQueue未满时，添加进来的任务会被安排到workQueue中等待执行。
 3. 当workQueue已满，但是currentSize<maximumPoolSize时，会立即开启一个非核心线程来执行任务。
 4. 当currentSize>=corePoolSize、workQueue已满、并且currentSize>maximumPoolSize时，调用handler默认抛出RejectExecutionExpection异常。
 
一句话概括来说就是先装满核心线程，再装满workQueue，再装满非核心线程，都装满则拒绝任务。 

### workQueue
在上述执行过程中有个关键的承载工具workQueue，queue的种类有三种：

 1. 直接提交。工作队列的默认选项是 SynchronousQueue，它将任务直接提交给线程而不保持它们。在此，如果不存在可用于立即运行任务的线程，则试图把任务加入队列将失败，因此会构造一个新的线程。此策略可以避免在处理可能具有内部依赖性的请求集时出现锁。直接提交通常要求无界 maximumPoolSizes 以避免拒绝新提交的任务。当命令以超过队列所能处理的平均数连续到达时，此策略允许无界线程具有增长的可能性。
 2. 无界队列。使用无界队列（例如，不具有预定义容量的 LinkedBlockingQueue）将导致在所有 corePoolSize 线程都忙时新任务在队列中等待。这样，创建的线程就不会超过 corePoolSize。（因此，maximumPoolSize 的值也就无效了。）当每个任务完全独立于其他任务，即任务执行互不影响时，适合于使用无界队列；例如，在 Web 页服务器中。这种排队可用于处理瞬态突发请求，当命令以超过队列所能处理的平均数连续到达时，此策略允许无界线程具有增长的可能性。
 3. 有界队列。当使用有限的 maximumPoolSizes 时，有界队列（如 ArrayBlockingQueue）有助于防止资源耗尽，但是可能较难调整和控制。队列大小和最大池大小可能需要相互折衷：使用大型队列和小型池可以最大限度地降低 CPU 使用率、操作系统资源和上下文切换开销，但是可能导致人工降低吞吐量。如果任务频繁阻塞（例如，如果它们是 I/O 边界），则系统可能为超过您许可的更多线程安排时间。使用小型队列通常要求较大的池大小，CPU 使用率较高，但是可能遇到不可接受的调度开销，这样也会降低吞吐量。
 
### Android中的4种常用的线程池

通过配置不同参数的ThreadPoolExecutor， 有4类常用的线程池, Executors类提供了4个工厂方法用于创建4种不同特性的线程池给开发者用.

#### FixedThreadPool
池子里的线程数有个最大值，可以自己设置，如果超过这个最大值，那么任务就会加入任务队列去等待。
用法:

```java 
ExecutorService fixedThreadPool = Executors.newFixedThreadPool(num);
fixedThreadPool.execute(runnable对象);
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                              0L, TimeUnit.MILLISECONDS,
                              new LinkedBlockingQueue<Runnable>());
}
```
特点：只有核心线程数，并且没有超时机制，因此核心线程即使闲置时，也不会被回收，因此能更快的响应外界的请求.

#### CachedThreadPool
池里的线程数量并不是固定的，理论上可以无限大，任务不需要排队，如果有空闲的线程，则复用，无则新建线程。
用法:
```java
ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
cachedThreadPool.execute(runnable对象);
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                              60L, TimeUnit.SECONDS,
                              new SynchronousQueue<Runnable>());
}
```
特点：没有核心线程，非核心线程数量没有限制， 超时为60秒.
适用于执行大量耗时较少的任务，当线程闲置超过60秒时就会被系统回收掉，当所有线程都被系统回收后，它几乎不占用任何系统资源.

#### ScheduledThreadPool
定长线程池，但是可以支持周期性的任务。
用法:
```java
ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(int corePoolSize);
scheduledThreadPool.schedule(runnable对象, 2000, TimeUnit.MILLISECONDS);
public ScheduledThreadPoolExecutor(int corePoolSize) {
    super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
          new DelayedWorkQueue());
}
```
特点：核心线程数是固定的，非核心线程数量没有限制， 没有超时机制.
主要用于执行定时任务和具有固定周期的重复任务.

#### SingleThreadExecutor
单例化的线程池，他只有一个线程去执行任务。最常见的一个例子就是我们的UI线程。它就是典型的单线程模型。
```java
ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();
singleThreadExecutor.execute(runnable对象);
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                            0L, TimeUnit.MILLISECONDS,
                            new LinkedBlockingQueue<Runnable>()));
}
```
特点：只有一个核心线程，并没有超时机制.
意义在于统一所有的外界任务到一个线程中， 这使得在这些任务之间不需要处理线程同步的问题.

下面是一个对线程池的简单封装，管理项目中的线程。
```java
/**
 * 线程池管理
 */
public class UPThreadPoolManager {

    private static UPThreadPoolManager mInstance = new UPThreadPoolManager();

    public static final synchronized UPThreadPoolManager getInstance() {
        if (mInstance == null) {
            mInstance = new UPThreadPoolManager();
        }
        return mInstance;
    }

    private int corePoolSize; // 核心线程池的数量，同时能够执行的线程数量
    private int maximumPoolSize;// 最大线程池数量，表示当缓冲队列满的时候能继续容纳的等待任务的数量
    private long keepAliveTime = 3;// 存活时间
    private TimeUnit unit = TimeUnit.MINUTES;
    private ThreadPoolExecutor executor;

    private UPThreadPoolManager() {
        /**
         * 给corePoolSize赋值：当前设备可用处理器核心数*2 + 1,能够让cpu的效率得到最大程度执行
         */
        corePoolSize = Runtime.getRuntime().availableProcessors() * 2 + 1;
        maximumPoolSize = corePoolSize; // 虽然maximumPoolSize用不到，但是需要赋值，否则报错
        executor = new ThreadPoolExecutor(corePoolSize, // 当某个核心任务执行完毕，会依次从缓冲队列中取出等待任务
                maximumPoolSize, // 5,先corePoolSize,然后new
                                 // LinkedBlockingQueue<Runnable>(),然后maximumPoolSize,但是它的数量是包含了corePoolSize的
                keepAliveTime, // 表示的是maximumPoolSize当中等待任务的存活时间
                unit, new LinkedBlockingQueue<Runnable>(), // 缓冲队列，用于存放等待任务，Linked的先进先出（无界队列）
                Executors.defaultThreadFactory(), // 创建线程的工厂
                new ThreadPoolExecutor.AbortPolicy() // 用来对超出maximumPoolSize的任务的处理策略
        );
        executor.allowCoreThreadTimeOut(true); // keepAliveTime同样作用于核心线程
    }

    /**
     * 执行任务
     */
    public void execute(Runnable runnable) {
        if (runnable == null)
            return;

        executor.execute(runnable);
    }

    /**
     * 从线程池中移除任务
     */
    public void remove(Runnable runnable) {
        if (runnable == null)
            return;

        executor.remove(runnable);
    }
}
```