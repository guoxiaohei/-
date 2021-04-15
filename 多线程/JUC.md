## JUC知识点梳理：

![java-thread-x-juc-overview-1](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/241aea12c454b9971c68aa33d5005db4.png)

### java.util.concurrent : 提供了并发编程的解决方案：

1. CAS是java.util.concurrent.atomic包的基础
2. AQS是java.util.concurrent.locks包以及一些常用类比如semophore，reentrantlock等类的基础

### JUC包的分类：

1. 线程执行器executor
2. 锁locks
3. 原子变量类atomic
4. 并发工具类tools
5. 并发集合collections

### 并发工具类（TOOLS）

1. 闭锁 CountDownLatch:让一个主线程等待一组事件发生后继续执行

   ​	事件指的是CountDownLatch里的countDown()方法，子线程调用countDown()方法后，主线程恢复运行状态，同时子线程会继续执行。

   ​	![image-20210113064027503](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/e93e95bbfb4c718379cb0a60a5bd0d84.png)

   示例：

   ```java
   package javabasic_JUC;
   
   import java.util.concurrent.CountDownLatch;
   
   /**
    * JUC包中闭锁CountDownLatch示例
    * 
    * */
   public class CountDownLatchDemo {
   
   	public static void main(String[] args) throws InterruptedException {
   		new CountDownLatchDemo().go();
   	}
   	
   	private void go() throws InterruptedException{
   		CountDownLatch countDownLatch = new CountDownLatch(3);
   		//依次创建三个线程，并启动
   		new Thread(new Task(countDownLatch),"Thread1").start();
   		Thread.sleep(1000);
   		new Thread(new Task(countDownLatch),"Thread2").start();
   		Thread.sleep(1000);
   		new Thread(new Task(countDownLatch),"Thread3").start();
   		countDownLatch.await();
   		System.out.println("所有线程已到达，主线程开始执行"+System.currentTimeMillis());
   	}
   	
   	class Task implements Runnable {
   		private CountDownLatch countDownLatch;
   		Task(CountDownLatch countDownLatch){
   			this.countDownLatch = countDownLatch;
   		}
   		@Override
   		public void run() {
   			System.out.println("线程"+Thread.currentThread().getName()+"已经到达"+System.currentTimeMillis());
   			countDownLatch.countDown();
   		}
   		
   	}
   	class Task1 implements Runnable {
   		private CountDownLatch countDownLatch;
   		Task1(CountDownLatch countDownLatch){
   			this.countDownLatch = countDownLatch;
   		}
   		@Override
   		public void run() {
   			System.out.println("线程"+Thread.currentThread().getName()+"已经到达"+System.currentTimeMillis());
   		//	countDownLatch.countDown();
   		}
   		
   	}
   
   }
   
   ```

   

2. 栅栏 CyclicBarrier：阻塞当前线程，等待其他线程

   等待其他线程，且会阻塞自己当前线程，所有线程必须同时到达栅栏位置后，才能继续执行；

   所有线程到达栅栏处，可以触发执行另外一个预先设置的线程

   示例：

   ```java
   package javabasic_JUC;
   
   import java.util.concurrent.BrokenBarrierException;
   import java.util.concurrent.CyclicBarrier;
   
   
   public class CyclicBarrierDemo {
   
   	public static void main(String[] args) throws InterruptedException{
   		new CyclicBarrierDemo().go();
   	}
   	
   	private void go() throws InterruptedException {
   		CyclicBarrier cyclicBarrier = new CyclicBarrier(3);
   		new Thread(new Task(cyclicBarrier),"Thread1").start();
   		Thread.sleep(1000);
   		new Thread(new Task(cyclicBarrier),"Thread2").start();
   		Thread.sleep(1000);
   		new Thread(new Task(cyclicBarrier),"Thread3").start();
   		Thread.sleep(1000);
   	}
   	
   	class Task implements Runnable{
   		private CyclicBarrier cyclicBarrier;
   		Task(CyclicBarrier cyclicBarrier){
   			this.cyclicBarrier = cyclicBarrier;
   		}
   		@Override
   		public void run() {
   			System.out.println("线程"+Thread.currentThread().getName()+"已经到达"+System.currentTimeMillis());
   			try {
   				cyclicBarrier.await();
   			}catch (InterruptedException e) {
   				e.printStackTrace();
   			}catch (BrokenBarrierException e) {
   				e.printStackTrace();			
   			}
   			System.out.println("线程"+Thread.currentThread().getName()+"开始处理"+System.currentTimeMillis());
   		}
   		
   	}
   }
   
   ```

   

3. 信号量 Semaphore

4. 交换器 Exchanger