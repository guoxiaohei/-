![1558906060806](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/7bb701d192b53f2c32fca3049d9652c1.png)

<img src="http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/d727e5aaec690518281649858303c7cb.png" alt="1559561242334"  />

![1560199211756](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/c5a924600de6ed9776b787eb65ec814d.png)

![1560201268068](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/991a06e51abeb3c24bb72a38842a907b.png)

![1560201284296](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/acb55479766a4679e86db645d178c57a.png)

- HashMap的数据结构：由数组Node<K,V>[] 和链表组成的复合结构

  Node<K,V>由哈希值，key，value，下个节点next组成

  ![1560201635060](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/d7208bd296989c0fbfffebbcedbd4f42.png)

  这个数组被分为一个个的桶（bucket），通过hash值决定了键值对在这个数组的寻址，hash值相同的键值对以链表的形式来存储，链表的长度超过TREEIFY_THRESHOLD(默认是8)，就会被改造为红黑树，当某个桶上的元素低于UNTREEIFY_THRESHOLD(默认是6)，红黑树会转换回链表。

  hashMap构造函数中传入数组的初始值大小参数会被自动调整为接近的2^n大小

  HashMap()：构造一个具有默认初始容量 (16) 和默认加载因子 (0.75) 的空 HashMap。

  HashMap(int initialCapacity)：构造一个带指定初始容量和默认加载因子 (0.75) 的空 HashMap。

  HashMap(int initialCapacity, float loadFactor)：构造一个带指定初始容量和加载因子的空 HashMap。

  ![1560205510964](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/f03d83f728e70084f80494ca8fa3145b.png)

   ![1560207321071](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/1dc630be868f31ec6856d53ccbf724e3.png)

  (h=key.hashCode())^(h>>>16)

  ![1560207421627](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/3f9e135b764d1129a52d5b620b8953cb.png)

  ![1560207510537](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/5b349e01694581f225b496acfeb8919f.png)

- ConcurrentHashMap:

  ![1560208262778](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/71bfb360622bcf4ff1f7ad0db93cf629.png)

  ![1560210588738](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/ff93ef3eb6e3ffbbd19790f1739d8364.png)

  ![1560210806424](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/540b34d12325838572cf9e7773813dcc.png)

