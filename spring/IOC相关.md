SpringIOC(Inversion of Control):控制反转

- 需要先了解依赖注入（Dependency Inversion）：由上层依赖于下层改为下层依赖于上层。含义：把底层类作为参数传递给上层类，实现上层对下层的“控制” 。
- ![1562047651250](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/74397d3fe3677aa5d55ec234942080ca.png)

- IOC容器的优势：
  1. 避免在各处使用new来创建类，并且可以做到统一维护，容器可以自动对代码进行初始化
  2. 在创建实例的时候不需要了解其中的细节

- Spring IOC原理图

  ![1562050500944](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/743e430f00e4f9b98d1e9cedbf389852.png)

  1. Spring容器读取Bean的配置信息
  2. 根据Bean注册表实例化Bean
  3. 将Bean实例放到Spring容器中
  4. 使用Bean

- Spring IOC支持的功能：

  ![1562051396040](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/0d44d53d8965ebb1d6c155ed3312be55.png)

![1562056324239](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/9cc8c6d4c38d3da02e7a3a2a6a1f5113.png)

#### 1、什么是 IOC

（1）控制反转，把对象创建和对象之间的调用过程，交给 Spring 进行管理

（2）使用 IOC 目的：为了耦合度降低

（3）做入门案例就是 IOC 实现

2、IOC 底层原理

（1）xml 解析、工厂模式、反射

3、画图讲解 IOC 底层原理

![image-20201123173234595](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/d454ed0360dd3f398d34ddcec57b5573.png)

1、IOC 思想基于 IOC 容器完成，IOC 容器底层就是对象工厂

2、Spring 提供 IOC 容器实现两种方式：（两个接口）

（1）BeanFactory：IOC 容器基本实现，是 Spring 内部的使用接口，不提供开发人员进行使用

\* 加载配置文件时候不会创建对象，在获取对象（使用）才去创建对象

（2）ApplicationContext：BeanFactory 接口的子接口，提供更多更强大的功能，一般由开发人

员进行使用

\* 加载配置文件时候就会把在配置文件对象进行创建

3、ApplicationContext 接口有实现类

![image-20201123173707783](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/3b6ce5e967e88a74abf01d4c64ce5bc4.png)

**IOC** **操作** **Bean** 管理（bean 生命周期）

1、生命周期

（1）从对象创建到对象销毁的过程

2、bean 生命周期

（1）通过构造器创建 bean 实例（无参数构造）

（2）为 bean 的属性设置值和对其他 bean 引用（调用 set 方法）

（3）调用 bean 的初始化的方法（需要进行配置初始化的方法）

（4）bean 可以使用了（对象获取到了）

（5）当容器关闭时候，调用 bean 的销毁的方法（需要进行配置销毁的方法）

![image-20201123174620506](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/c37cacd899d94a55e3ada5b118d7025a.png)

**演示添加后置处理器效果**

（1）创建类，实现接口 BeanPostProcessor，创建后置处理器

```java
public class MyBeanPost implements BeanPostProcessor {

 @Override

 public Object postProcessBeforeInitialization(Object bean, String beanName) 

throws BeansException {

 System.out.println("在初始化之前执行的方法");

 return bean;

 }

 @Override

 public Object postProcessAfterInitialization(Object bean, String beanName) 

throws BeansException {

 System.out.println("在初始化之后执行的方法");

 return bean;

 } 

}
```

