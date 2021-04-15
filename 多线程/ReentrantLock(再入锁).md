位于Java.util.concurrent.locks(JUC)包基于AbstractQueuedSynchronizer（简称AQS）类实现

特点：

1. 能够实现比sychronized更细粒度的控制，如控制fairness（公平性）
2. 调用lock()之后，必须调用unlock()释放锁
3. 性能未必比synchronized高，并且也是可重入的

ReentrantLock公平性的设置

1. ReentrantLock fairLock = new ReentrantLock(true);
2. 参数为true时，倾向于将锁赋予等待时间最久的线程
3. 公平锁：获取锁的顺序按先后调用lock方法的顺序（慎用）
4. 非公平锁：抢占的顺序不一定，看运气
5. synchronized是非公平锁

- ReentrantLock将锁对象化
  1. 判断是否有线程，或者特定某个线程，在排队等待获取锁
  2. 带超时的获取锁的尝试
  3. 感知有没有成功获取锁

- ReentrantLock与synchronized区别：
  1. sychronized是关键字，ReentrantLock是类
  2. ReentrantLock可以对获取锁的等待时间进行设置，避免死锁
  3. ReentrantLock可以获取各种锁的信息
  4. ReentrantLock可以灵活地实现多路通知
  5. 机制：sync操作MarkWord，lock调用Unsafe类的park()方法

