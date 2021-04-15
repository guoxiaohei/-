CAS，在Java并发应用中通常指CompareAndSwap或CompareAndSet，即比较并交换。

CAS是一种乐观锁（lock-free：无锁编程,即不使用锁的情况下实现多线程之间的变量同步）。

是一种高效实现线程安全性的方法，支持原子更新操作，适用于计数器，序列发生器等场景。

CAS是一个原子操作，它比较一个内存位置的值并且只有相等时修改这个内存位置的值为新的值，保证了新的值总是基于最新的信息计算的，如果有其他线程在这期间修改了这个值则CAS失败。CAS返回是否成功或者内存位置原来的值用于判断是否CAS成功。

J.U.C的atomic包提供了常用的原子性数据类型以及引用、数组等相关原子类型和更新操作工具，是很多线程安全程序的首选。

Unsafe类虽然提供CAS服务，但因能够操作任意内存地址读写而有隐患。

缺点：

1. 循环时间长开销大。CAS长时间自旋不成功，给CPU带来很大的性能开销。
2. 只能保证一个共享变量的原子操作。对多个共享变量操作时，不能保证原子性。
3. ABA的问题。解决方法就是：增加版本号，每次使用的时候版本号+1，每次变量更新的时候版本号+1。java提供AtomicStampzedReference来解决ABA问题。

下面就是利用atomic包来进行共享变量自增操作的例子

```java
import java.util.concurrent.atomic.AtomicInteger;
 
public class AtomicIntegerTest {
 
    private static final int THREADS_CONUT = 20;
    public static AtomicInteger count = new AtomicInteger(0);
 
    public static void increase() {
        count.incrementAndGet();
    }
 
    public static void main(String[] args) {
        Thread[] threads = new Thread[THREADS_CONUT];
        for (int i = 0; i < THREADS_CONUT; i++) {
            threads[i] = new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int i = 0; i < 1000; i++) {
                        increase();
                    }
                }
            });
            threads[i].start();
        }
 
        while (Thread.activeCount() > 1) {
            Thread.yield();
        }
        System.out.println(count);
    }
}

```

结果每次都输出20000，程序输出了正确的结果，这都归功于AtomicInteger.incrementAndGet()方法的原子性。

AtomicInteger.incrementAndGet()方法:

```java
  /**
     * Atomically increments by one the current value.
     *
     * @return the updated value
     */
    public final int incrementAndGet() {
        for (;;) {
            int current = get();
            int next = current + 1;
            if (compareAndSet(current, next))
                return next;
        }
    }
```

incrementAndGet()方法在一个无限循环体内，不断尝试将一个比当前值大1的新值赋给自己，如果失败则说明在执行"获取-设置"操作的时已经被其它线程修改过了，于是便再次进入循环下一次操作，直到成功为止。这个便是AtomicInteger原子性的"诀窍"了，继续进源码看它的compareAndSet方法:

compareAndSet()调用的就是Unsafe.compareAndSwapInt()方法，即Unsafe类的CAS操作

```java
/**
     * Atomically sets the value to the given updated value
     * if the current value {@code ==} the expected value.
     *
     * @param expect the expected value
     * @param update the new value
     * @return true if successful. False return indicates that
     * the actual value was not equal to the expected value.
     */
    public final boolean compareAndSet(int expect, int update) {
        return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    }
```

