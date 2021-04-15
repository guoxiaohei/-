- 判断对象是否被回收的方法：引用计数算法，可达性算法

- 引用计数算法：

  1. 通过判断对象的引用数量来决定对象是否可以被回收
  2. 每个对象实例都有一个引用计数器，被引用则+1，完成引用则-1
  3. 任何引用计数为0的对象实例都可以被当作垃圾收集

  优点：执行效率高，程序执行受影响较小

  缺点：无法检测出循环引用的情况，导致内存泄漏

- 可达性分析算法：

  通过判断对象的引用链是否可达来决定对象是否可以被回收：

  ![1558641374521](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/558084c8d60915d6d8182f11d84018ca.png)

- 可以作为GC ROOT的对象：
  1. 虚拟机栈引用的对象（栈帧中的本地变量表）
  2. 方法区中的常量引用的对象
  3. 方法区中的类静态属性引用的对象
  4. 本地方法栈中（Native方法）的引用对象
  5. 活跃线程的引用对象

- 垃圾回收算法：

  - 标记-清除算法：（Mark and Sweep）

    ​	标记：从根集合进行扫描，对存活的对象进行标记

    ​	清除：对堆内存从头到尾进行线性遍历，回收不可达对象内存

    缺点：由于标记的内存不连续，容易造成内存碎片化

  - 复制算法：

    ​	分为对象面和空闲面

    ​	对象在对象面上创建

    ​	存活的对象被从对象面复制到空闲面

    ​	将对象面所有对象内存清除

    ![1558643782069](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/ddb71b1350aa116d965849ff72768164.png)

  - 标记-整理算法：

    ​	标记：从根集合进行扫描，对存活的对象进行标记。

    ​	清除：移动所有存活的对象，且按照内存地址次序依次排列，然后将末端内存地址以后的内存全部回收。

    ![1558644289047](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/42e753fe59cd7b82ab539389412ae5fc.png)

- 分代收集算法（Generational Collector）:按照对象生命周期的不同划分区域以采用不同的来及回收算法，目的是提高JVM的回收效率。

  ​	JDK8以前把堆内存区域划分年轻代（Young Generational），老年代（Old Generational）和永久代（Permanent Generation）。JDK8及其右后版本只保留了年轻代和老年代

  GC的分类：Minor GC，Full GC

  从年轻代空间（包括 Eden 和 Survivor 区域）回收内存被称为 Minor GC。

  年轻代：尽可能快速地收集掉那些生命周期较短的对象（一般新生成的对象都存放在年轻代中）

  当Eden区空间不足时，会触发Minor GC，Minor GC采用复制算法

  ![1558645572848](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/0839bb3016842e195bd38bcf8b776778.png)

  对象如何晋升到老年代

  1. 经历一定Minor次数依然存活的对象（默认15次）
  2. Survivor区中存放不下的对象
  3. 新生成的大对象（-XX:+PretenuerSizeThreshold）

  ![1558646109959](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/5287a0751d68403b38bb868f146fecac.png)

  老年代：存放生命周期较长的对象：（与年轻代的大小比例默认为2:1）

  ![1558646207373](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/287b397ff944f9610b76edf53dd946dd.png)

  老年代采用Full GC和Major GC，Full GC比Minor GC慢，但是执行频率低

  ![1558646709514](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/09e1c2fb84cd721ec06c0979fd19fc53.png)

  