![1557129137003](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/fe30845dc637bf23ee65e06af073cb8d.png)

如果并发的请求数量非常多，但每个线程执行的时间很短，这样就会频繁的创建和销毁线程，如此一来会大大降低系统的效率。可能出现服务器在为每个请求创建新线程和销毁线程上花费的时间和消耗的系统资源要比处理实际的用户请求的时间和资源更多。

什么时候使用线程池？

- 单个任务处理时间比较短
- 需要处理的任务数量很大

使用线程池的好处：

- 降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- 提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行。
- 提高线程的可管理性。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

![1562780031663](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/1cfb69521cbefa112ed71aa8c8bf8779.png)

- Fork/Join框架      （Work-Stealing算法：某个线程从其他队列里窃取任务来执行）

  ​	把大任务分割成若干个小任务并行执行，最终汇总每个小任务结果后得到大任务结果的框架

  ![1562920133824](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/1a251b82371b98c83b6305a998584196.png)

- ![1562920193450](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/4a97e6bfc7cbca45cde038824e7ada43.png)

- ![1562920216840](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/e6edcbe85ab79d5b8e472b46dce3a359.png)

- ThreadPoolExecutor继承自AbstractExecutorService，也是实现了ExecutorService接口。

- ThreadPoolExecutor的构造函数：

  ```java
  public ThreadPoolExecutor(int corePoolSize,
                            int maximumPoolSize,
                            long keepAliveTime,
                            TimeUnit unit,
                            BlockingQueue<Runnable> workQueue,
                            ThreadFactory threadFactory,
                            RejectedExecutionHandler handler) {
      if (corePoolSize < 0 ||
          maximumPoolSize <= 0 ||
          maximumPoolSize < corePoolSize ||
          keepAliveTime < 0)
          throw new IllegalArgumentException();
      if (workQueue == null || threadFactory == null || handler == null)
          throw new NullPointerException();
      this.corePoolSize = corePoolSize;
      this.maximumPoolSize = maximumPoolSize;
      this.workQueue = workQueue;
      this.keepAliveTime = unit.toNanos(keepAliveTime);
      this.threadFactory = threadFactory;
      this.handler = handler;
  }
  ```

  

  1. corePoolSize：核心线程数量

  2. maximumPoolSize：线程不够用时能够创建的最大线程数

  3. workQueue：任务等待队列

  4. keepAliveTime：线程池维护线程所允许的空闲时间。当线程池中的线程数量大于corePoolSize的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了keepAliveTime；

  5. threadFactory：它是ThreadFactory类型的变量，用来创建新线程。默认使用Executors.defaultThreadFactory() 来创建线程。使用默认的ThreadFactory来创建线程时，会使新创建的线程具有相同的NORM_PRIORITY优先级并且是非守护线程，同时也设置了线程的名称。

  6. handler:它是RejectedExecutionHandler类型的变量，表示线程池的饱和策略。如果阻塞队列满了并且没有空闲的线程，这时如果继续提交任务，就需要采取一种策略处理该任务。线程池提供了4种策略：

     ![1562920601318](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/b8113888a368f169d336890db0dcf593.png)

     newFixedThreadPool实现方法

  ![1562920526075](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/050b3b3db33111a8995fdc2f898d6e88.png)

- 新任务提交execute执行后的判断：

  ![1562920672355](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/dc100a5cde4e1cb9abd98f931963a9a0.png)

![1562920727445](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/5e56e6f41c580f6cd5088af87b54c654.png)

- 线程池的状态：

  1. Running：能接受新提交的任务，并且也能处理阻塞队列中的任务
  2. Shutdown：不再接受新提交的任务，但可以处理存量任务
  3. Stop：不在接受新提交的任务，也不处理存量任务
  4. Tidying：所有的任务都已终止
  5. Terminated：terminated()方法执行完后进入该状态

  ![1562921374906](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/005d30ad092f2f38ada99d430e93f98b.png)

- `ctl`是对线程池的运行状态和线程池中有效线程的数量进行控制的一个字段， 它包含两部分的信息: 线程池的运行状态 (runState) 和线程池内有效线程的数量 (workerCount)，这里可以看到，使用了Integer类型来保存，高3位保存runState，低29位保存workerCount。COUNT_BITS 就是29，CAPACITY就是1左移29位减1（29个1），这个常量表示workerCount的上限值，大约是5亿。

  ```java
  private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
  private static final int COUNT_BITS = Integer.SIZE - 3;
  private static final int CAPACITY   = (1 << COUNT_BITS) - 1;
  
  // runState is stored in the high-order bits
  private static final int RUNNING    = -1 << COUNT_BITS;
  private static final int SHUTDOWN   =  0 << COUNT_BITS;
  private static final int STOP       =  1 << COUNT_BITS;
  private static final int TIDYING    =  2 << COUNT_BITS;
  private static final int TERMINATED =  3 << COUNT_BITS;
  ```

  ctl相关方法：

  - **runStateOf**：获取运行状态；

  - **workerCountOf**：获取活动线程数；

  - **ctlOf**：获取运行状态和活动线程数的值。