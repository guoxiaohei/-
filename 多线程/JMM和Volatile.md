- Java内存模型JMM

  ​	java内存模型（Java Memory Model，简称JMM）本身是一种抽象的概念，并不真实存在，它描述的是一组规则或规范，通过这组规范定义了程序中各个变量（包括实例字段，静态字段和构成数组对象的元素）的访问方式。

- JMM的主内存

  1. 存储Java实例对象
  2. 包括成员变量、类信息、常量、静态变量等
  3. 属于数据共享的区域，多线程并发操作时会引发线程安全问题

- JMM中的工作内存
  1. 存储当前方法的所有本地变量信息，本地变量对其他线程不可见
  2. 字节码行号指示器、Native方法信息
  3. 属于线程私有数据区域，不存在线程安全问题
- JMM内存可见性问题：A操作的结果需要对B操作可见，则A与B存在happens-before关系

![1556476336165](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/4e638c41c8e0569116ff0a2feead620b.png)

- 指令重排序：

  ​	重排序是指编译器和处理器为了优化程序性能而对指令序列进行排序的一种手段。但是重排序也需要遵守一定规则：

  1. 重排序操作不会对存在数据依赖关系的操作进行指令重排序
  2. 重排序是为了优化性能，但是不管怎么重排序，单线程下程序的执行结果不能被改变

  

- volatile：

  volatile是一种轻量级的同步机制，它主要有两个特性：一是保证共享变量对所有线程的可见性；二是禁止指令重排序优化。同时需要注意的是，volatile对于单个的共享变量的读/写具有原子性，但是像num++这种复合操作，volatile无法保证其原子性。

  将一个共享变量声明为volatile后，会有以下效应：

  　1.当写一个volatile变量时，JMM会把该线程对应的本地内存中的变量强制刷新到主内存中去；

  　2.这个写会操作会导致其他线程中的缓存无效。

  volatile禁止指令重排序优化的典型应用场景：单例模式懒汉单例的双重检测方法

  ```java
   1 class Singleton{
   2     private volatile static Singleton singleton;    
   3     private Singleton(){}
   4     
   5     public static Singleton getInstance(){
   6         if(singleton == null){
   7             synchronized(Singleton.class){
   8                 if(singleton == null)
   9                     singleton = new Singleton();   //1
  10             }
  11         }
  12         return singleton;
  13     }
  14  }
  ```

  

- volatile与sychronized的区别：
  1. volatile本质是告诉JVM当前变量在寄存器（工作内存）中的值是不确定的，需要从主存中读取；synchronized则是锁定当前变量，只有当前线程可以访问该变量，其他线程被阻塞住直到该线程完成变量操作为止。
  2. volatile仅能作用在变量级别；synchronized可以使用在变量，方法，类级别。
  3. volatile仅能实现变量的修改可见性，不能保证原子性；而synchronized可以保证变量修改的可见性和原子性。
  4. volatile不会造成线程的阻塞；synchronized可能会造成线程的阻塞。
  5. volatile标记的变量不会被编译器优化；synchronized标记的变量可以被编译器优化。