- JVM内存模型——jdk8

  ![1558553390568](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/684f5218be16b8a734cb908a401cefc4.png)

- 程序计数器

  ![1558553615831](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/2516bad419935117481e223cec5ef37f.png)

- 栈：每一个栈帧包含局部变量表，操作数栈，指向运行时常量池的引用，方法返回地址

- 方法区（永久代）：Java7中已经将运行时常量池从永久代移除，在Java 堆（Heap）中开辟了一块区域存放运行时常量池。而在Java8中，已经彻底没有了永久代，将方法区直接放在一个与堆不相连的本地内存区域，这个区域被叫做元空间。 元空间与永久代的区别：

  元空间使用本地内存，而永久代使用jvm内存

  ![1558642021470](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/4828f6e977e185283f0efdae8d7f2af2.png)

  

  ![1558554486118](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/03935c7fa445b6bd7f3ca3c7b0f83233.png)

- 堆（Heap）

  Java堆是Java虚拟机所管理的内存中最大的一块。Java堆是被所有线程共享的一块内存区域，在虚拟机启动时创建。此内存区域的唯一目的就是存放对象实例：所有的对象实例以及数组都要在堆上分配（The heap is the runtime data area from which memory for all class instances and arrays is allocated）。但Class对象比较特殊，它虽然是对象，但是存放在方法区里。

![1558557131652](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/a41fdebd9237efa9ec4acf79601ee6f5.png)

![1558557263314](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/2bb702825d4eb68d57352bf27437e678.png)

![1558557315330](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/29e6564155e978147fefc0ee3f85c894.png)

![1558557524543](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/d4a4ac41df24b17c53ce6f162959ef96.png)

![1558557466376](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/48eeb9e301bfcc603e66d9a299f25f57.png)

![1558557693178](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/d70ae2acec1c79cf1f8dcb61bd208933.png)

