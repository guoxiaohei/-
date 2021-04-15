

系统变量（VARIABLES）：

show variables like "%quer%";

slow_query_log: on/off 是否打开慢查询日志

slow_query_log_file: 慢查询日志的保存地址

long_query_time：认定为慢查询的时间（秒）

status：

show status like '%slow_queries%';

slow_queries:记录慢查询次数

![1599728176442](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/fdd80ae210951e65ad5b2bf9ef245144.png)

- explain 关键字：放在select查询语句之前，用于描述mysql如何进行查询操作

![1600331858642](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/7b936667c779d96a61db39a13c0801be.png)

id标明了sql的执行顺序，id越大，越先执行

![1600331969059](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/3ca6127e7d9e74ad2da9995e0b3367d6.png)

type：表示mysql找到数据行的方式（上图中查找性能从最优到最差）index与all表示全表扫描，可以优化

![1600332104915](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/a59c162ae04dbbdaf4b2d2037ca5ee60.png)

