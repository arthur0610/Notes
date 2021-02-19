# Java线程
## 计算机两种线程模式
用户级线程 `ULT` 用户程序实现 不依赖操作系统核心 应用提供创建 同步 调度和管理线程的函数来控制用户线程
**不需要用户态/内核态切换 速度快** 内核对`ULT`无感知 线程阻塞则进程（包括内部所有线程）阻塞

内核级线程 `KLT` 系统内核管理线程 内核保存线程的状态和上下文信息 线程阻塞并不会引起进程阻塞 在多处理器系统上多线程在多处理并行运行
线程的创建 调度 和管理由内核完成 效率比`ULT`要慢 比进程操作快

## 线程池
Java虚拟机使用的是哪一种线程模型呢？
```
JVM虚拟机使用的线程模式： KLT
```

计算机操作系统 会把内存条 格式成逻辑地址 对应着硬件地址
内存空间分配逻辑空间 逻辑空间一般比硬件空间小一些

`逻辑空间`分为两个部分 `内核空间`和`用户空间`
区别在于两个空间的**权限级别**不同

防止无关人员可以越级操作一些敏感数据
操作系统为了杜绝用户去访问和操作底层数据 把运行级别分成了两份

操作系统会提供一个接口 给上层的用户空间
```
JVM调用内核系统提供的API 去创建线程 这些线程再映射到底层的CPU实现KLT线程
```

Java线程逻辑就是Java线程创建完成以后会使用操作系统提供的库调度器陷入（映射？）到内核空间 创建内核线程 内核线程会被维护线程表 被操作系统调度器调度 CPU执行完一个线程之后 会根据算法来分配每个线程的时间
```
Java线程创建是依赖于系统内核提供的API 通过JVM调用系统库创建内核线程 内核线程和Java-Thread的是 1:1 的映射关系
Linux
$man pthread_create
```
`线程`是稀缺资源 它的创建和销毁是一个相对偏重且消耗资源的操作 而Java线程依赖于内核线程 创建线程需要**操作系统状态切换** 为避免资源过度消耗需要设法**重用线程**执行多个任务

**线程池**就是一个程序缓存 负责对线程进行统一分配 调优和监控

什么时候使用线程池？
1. 单个任务处理时间短
2. 需要处理的任务数量大

线程池优势
1. 重用存在的线程 减少线程创建 销毁的开销 提升性能
2. 提高响应速度 当任务到达时 任务可以不需要等到线程创建就能立即执行
3. 提高线程的可管理性 可统一分配 调优和监控


## 阻塞队列 BlockingQueue
FIFO
在任意时刻 不管并发有多高 永远只有一个线程能够进行队列的入队或者出队的操作

**有界队列&无界队列**
如果队列满时 只能进行出队操作 所有入队的操作必须等待 这也是一种阻塞
如果队列空时 只能进行入队操作 所有出队的操作必须等待 这也是一种阻塞

```java
/*
 * corePoolSize: 2
 * maximumPoolSize: 3
 * keepAliveTime: 60
 * TimeUnit.SECONDS: s
 */

final ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 3, 60, TimeUnit.SECONDS, new ArrayBlockingQueue<Runable>(5), Executors.defaultThreadFactory());

for(int i = 0; i < 9; i++) {
    pool.execute(new Task(i));
}

pool.shutdown();
```

如果任务的数量超过了最大连接池数量+阻塞队列最大值 (上例为 3+5)

线程池提供了解决办法：拒绝策略 (RejectedExecutionHandler)
1. AbortPolicy 抛异常
2. CallerRunsPolicy 
3. DiscardOldestPolicy 丢弃最老的任务
4. DIscardPolicy 丢弃任务

```java
for(int i=0; i < 9; i++) {
    pool.execute(new Task(i));
}

pool.shutdown();
```

## 线程池的五种状态
1. `Running` 能接受新任务以及处理已添加的任务
2. `Shutdown` 不接受新任务 可以处理已添加的任务
3. `Stop` 不接受新任务 不处理已添加的任务
4. `Tidying` 所有任务终止 ctl记录的任务数量为0
5. `Terminated` 线程池彻底终止 则转变线程池为`Terminated`状态


## 线程池的运行状态 ctl
**ctl负责记录线程池的运行状态和活动线程数量**
```java
// ctl是一个 原子性的Integer
private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
// 高3位记录线程当前生命状态
// 低29位记录当前工作线程数
private static final int COUNT_BITS = Integer.SIZE - 3; // 32 - 3 = 29

// 0001 1111 1111 1111 1111 1111 1111 1111
private static final int CAPACITY   = (1 << COUNT_BITS) - 1;


// runState is stored in the high-order bits
private static final int RUNNING    = -1 << COUNT_BITS;
// RUNNING = 1110 0000 0000 0000 0000 0000 0000 0000
// 负数符号位（最高位）为1  其余位数正数的反码 补1
//  1 = 0000 0000 0000 0000 0000 0000 0000 0001
//  取反
//    = 1111 1111 1111 1111 1111 1111 1111 1110
//  补一
//    = 1111 1111 1111 1111 1111 1111 1111 1111
// -1 = 1111 1111 1111 1111 1111 1111 1111 1110
//  COUNT_BITS = 29 位移29位
//  RUNNING = 1110 0000 0000 0000 0000 0000 0000 0000


// 0000 0000 0000 0000 0000 0000 0000 0000
private static final int SHUTDOWN   =  0 << COUNT_BITS;
// 0010 0000 0000 0000 0000 0000 0000 0000
private static final int STOP       =  1 << COUNT_BITS;
// 0100 0000 0000 0000 0000 0000 0000 0000
private static final int TIDYING    =  2 << COUNT_BITS;
// 1000 0000 0000 0000 0000 0000 0000 0000
private static final int TERMINATED =  3 << COUNT_BITS;

// Packing and unparking ctl
private static int runStateOf(int c)     { return c & ~CAPACITY; } 
private static int workerCountOf(int c)  { return c & CAPACITY; }
private static int ctlOf(int rs, int wc) { return rs | wc; }
```


## Worker的工作原理
```java
private final calss Worker
    extends AbstractQueuedSynchronizer
    implements Runnable 
{
    /** Thread this worker is running in. Null if factory fails. **/
    final Thread thread;
    /** Initial task to run. Possibly null. **/
    Runnable firstTask;
    ...
    Worker(Runnable firstTask) {
        ...
        this.firstTask = firstTask;
        this.thread = getThreadFactory().newThread(this);
    }

    /** Delegates main run loop to outer runWorker **/
    public void run() { runWorker(this); }
    ...

}
```
