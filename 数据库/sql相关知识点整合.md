**目录：**

[TOC]



## **SQL 执行顺序**

- 以Mysql为例我们手写的 SQL 顺序

  ```mysql
  SELECT DISTINCT
  	< select_list >
  FROM 
  	< left_table > < join_type >
  JOIN < right_table > ON < join_condition >
  WHERE
  	< where_condition >
  GROUP BY 
  	< group_by_list >
  HAVING
  	< having_condition >
  ORDER BY
  	< order_by_condition >
  LIMIT < limit_number >
  ```

- 以Mysql为例的实际执行的顺序

  ```mysql
FROM < left_table > 
  ON < join_condition >
< join_type > JOIN < right_table >
  WHERE < where_condition >
  GROUP BY < group_by_list >
  HAVING < having_condition >
  SELECT 
  DISTINCT < select_list >
  ORDER BY < order_by_condition >
  LIMIT < limit_number >
  ```
  
  Mysql从FROM开始执行
  
  ![aHR0cDovL2hleWdvLm9zcy1jbi1zaGFuZ2hhaS5hbGl5dW5jcy5jb20vaW1hZ2VzL2ltYWdlLTIwMjAwODAzMjEyOTM2MTExLnBuZw](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/6f8a4e7bdaec91c3f7818bf959ea2228.png)

- ### **GROUP BY:**

  - 列函数对于group by子句定义的每个组各返回一个结果
  - 如果用group by，那么你的select语句中选择的列要么是你group by语句里用到的列，要么是其他表的列，要么是带有sum，min，avg，count等列函数的类

- ### **HAVING**：

  - 通常与GROUP BY字句一起使用

  - WHERE 过滤行，HAVING过滤组

  - 出现在同一sql的顺序：WHERE>GROUP BY>HAVING

    示例：

    ```mysql
    #查询所有平均分大于60的同学的学号，姓名，平均成绩
    select stu.student_id,stu.student_name,avg(s.score)
    from 
    student stu,
    score s
    where stu.student_id=s.student_id
    group by s.student_id
    having avg(s.score)>60;
    
    ```

- **Oracle 分页：**

  ​	分页公式：

  ```plsql
  --pageNo当前页码，pageSize一页几行
  pageNo = 1;
  pageSize = 3;
  select * from (select rownum r,e.* from (select * from emp order by empno desc) e) e1 where r > (pageNo-1)*pageSize and r <= pageNo * pageSize;
  ```

- **DB2 分页：**

  ​	分页公式：

  ```sql
  --pageNo当前页码，pageSize一页几行
  pageNo = 1;
  pageSize = 3;
  select * from (select ROW_NUMER() OVER() r,e.* from (select * from emp order by empno desc) e) e1 where r > (pageNo-1)*pageSize and r <= pageNo * pageSize;
  ```

  ​	

- ### MYSQL分页LIMIT基本语法：

  ```sql
  SELECT * FROM table  LIMIT [offset,] rows | rows OFFSET offset;
  ```

  如果只给定一个参数，表示记录数。

  ```
  mysql> SELECT * FROM orange LIMIT 5;     //检索前5条记录(1-5)
  ```

  相当于

  ```
  mysql> SELECT * from orange LIMIT 0,5;1
  ```

  两个参数，第一个参数表示offset, 第二个参数为记录数。

  ```
  mysql> SELECT * FROM orange LIMIT 10,15;  // 检索记录11-25
  ```

  再如，另一种用法：

  ```
  mysql> SELECT * FROM orange LIMIT 2 OFFSET 3;//查询4-5两条记录
  ```

  等价于

  ```
  mysql> SELECT * FROM orange  LIMIT 3,2;
  ```

  另外，可以指定第二个参数为-1，用于检索从偏移量到最后一条记录。 
  (试过，MySQL报错，有待进一步验证。)

  ```
  mysql> SELECT * FROM orange LIMIT 95,-1; // 检索记录96-last
  ```


## **JOIN连接查询**

![aHR0cDovL2hleWdvLm9zcy1jbi1zaGFuZ2hhaS5hbGl5dW5jcy5jb20vaW1hZ2VzL2ltYWdlLTIwMjAwODAzMjEzMTA2NDM0LnBuZw](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/6c54c25dfaabaabb5e74be6fddbd8e10.png)

> **7 种 JOIN 示例**

**笛卡尔积**

1. tbl_emp 表和 tbl_dept 表的笛卡尔乘积：`select * from tbl_emp, tbl_dept;`
2. 其结果集的个数为：5 * 8 = 40

```mysql
mysql> select * from tbl_emp, tbl_dept;
+----+------+--------+----+----------+--------+
| id | NAME | deptId | id | deptName | locAdd |
+----+------+--------+----+----------+--------+
|  1 | z3   |      1 |  1 | RD       | 11     |
|  1 | z3   |      1 |  2 | HR       | 12     |
|  1 | z3   |      1 |  3 | MK       | 13     |
|  1 | z3   |      1 |  4 | MIS      | 14     |
|  1 | z3   |      1 |  5 | FD       | 15     |
|  2 | z4   |      1 |  1 | RD       | 11     |
|  2 | z4   |      1 |  2 | HR       | 12     |
|  2 | z4   |      1 |  3 | MK       | 13     |
|  2 | z4   |      1 |  4 | MIS      | 14     |
|  2 | z4   |      1 |  5 | FD       | 15     |
|  3 | z5   |      1 |  1 | RD       | 11     |
|  3 | z5   |      1 |  2 | HR       | 12     |
|  3 | z5   |      1 |  3 | MK       | 13     |
|  3 | z5   |      1 |  4 | MIS      | 14     |
|  3 | z5   |      1 |  5 | FD       | 15     |
|  4 | w5   |      2 |  1 | RD       | 11     |
|  4 | w5   |      2 |  2 | HR       | 12     |
|  4 | w5   |      2 |  3 | MK       | 13     |
|  4 | w5   |      2 |  4 | MIS      | 14     |
|  4 | w5   |      2 |  5 | FD       | 15     |
|  5 | w6   |      2 |  1 | RD       | 11     |
|  5 | w6   |      2 |  2 | HR       | 12     |
|  5 | w6   |      2 |  3 | MK       | 13     |
|  5 | w6   |      2 |  4 | MIS      | 14     |
|  5 | w6   |      2 |  5 | FD       | 15     |
|  6 | s7   |      3 |  1 | RD       | 11     |
|  6 | s7   |      3 |  2 | HR       | 12     |
|  6 | s7   |      3 |  3 | MK       | 13     |
|  6 | s7   |      3 |  4 | MIS      | 14     |
|  6 | s7   |      3 |  5 | FD       | 15     |
|  7 | s8   |      4 |  1 | RD       | 11     |
|  7 | s8   |      4 |  2 | HR       | 12     |
|  7 | s8   |      4 |  3 | MK       | 13     |
|  7 | s8   |      4 |  4 | MIS      | 14     |
|  7 | s8   |      4 |  5 | FD       | 15     |
|  8 | s9   |     51 |  1 | RD       | 11     |
|  8 | s9   |     51 |  2 | HR       | 12     |
|  8 | s9   |     51 |  3 | MK       | 13     |
|  8 | s9   |     51 |  4 | MIS      | 14     |
|  8 | s9   |     51 |  5 | FD       | 15     |
+----+------+--------+----+----------+--------+
40 rows in set (0.00 sec)
```

------

**inner join**

1. tbl_emp 表和 tbl_dept 的交集部分（公共部分）
2. `select * from tbl_emp e inner join tbl_dept d on e.deptId = d.id;`

```mysql
mysql> select * from tbl_emp e inner join tbl_dept d on e.deptId = d.id;
+----+------+--------+----+----------+--------+
| id | NAME | deptId | id | deptName | locAdd |
+----+------+--------+----+----------+--------+
|  1 | z3   |      1 |  1 | RD       | 11     |
|  2 | z4   |      1 |  1 | RD       | 11     |
|  3 | z5   |      1 |  1 | RD       | 11     |
|  4 | w5   |      2 |  2 | HR       | 12     |
|  5 | w6   |      2 |  2 | HR       | 12     |
|  6 | s7   |      3 |  3 | MK       | 13     |
|  7 | s8   |      4 |  4 | MIS      | 14     |
+----+------+--------+----+----------+--------+
7 rows in set (0.00 sec)

```

------

**left join**

1. tbl_emp 与 tbl_dept 的公共部分 + tbl_emp 表的独有部分
2. left join：取左表独有部分 + 两表公共部分
3. `select * from tbl_emp e left join tbl_dept d on e.deptId = d.id;`

```mysql
mysql> select * from tbl_emp e left join tbl_dept d on e.deptId = d.id;
+----+------+--------+------+----------+--------+
| id | NAME | deptId | id   | deptName | locAdd |
+----+------+--------+------+----------+--------+
|  1 | z3   |      1 |    1 | RD       | 11     |
|  2 | z4   |      1 |    1 | RD       | 11     |
|  3 | z5   |      1 |    1 | RD       | 11     |
|  4 | w5   |      2 |    2 | HR       | 12     |
|  5 | w6   |      2 |    2 | HR       | 12     |
|  6 | s7   |      3 |    3 | MK       | 13     |
|  7 | s8   |      4 |    4 | MIS      | 14     |
|  8 | s9   |     51 | NULL | NULL     | NULL   |
+----+------+--------+------+----------+--------+
8 rows in set (0.00 sec)

```

------

**right join**

1. tbl_emp 与 tbl_dept 的公共部分 + tbl_dept表的独有部分
2. right join：取右表独有部分 + 两表公共部分
3. `select * from tbl_emp e right join tbl_dept d on e.deptId = d.id;`

```mysql
mysql> select * from tbl_emp e right join tbl_dept d on e.deptId = d.id;
+------+------+--------+----+----------+--------+
| id   | NAME | deptId | id | deptName | locAdd |
+------+------+--------+----+----------+--------+
|    1 | z3   |      1 |  1 | RD       | 11     |
|    2 | z4   |      1 |  1 | RD       | 11     |
|    3 | z5   |      1 |  1 | RD       | 11     |
|    4 | w5   |      2 |  2 | HR       | 12     |
|    5 | w6   |      2 |  2 | HR       | 12     |
|    6 | s7   |      3 |  3 | MK       | 13     |
|    7 | s8   |      4 |  4 | MIS      | 14     |
| NULL | NULL |   NULL |  5 | FD       | 15     |
+------+------+--------+----+----------+--------+
8 rows in set (0.00 sec)

```

------

**left join without common part**

1. tbl_emp 表的独有部分：将 left join 结果集中的两表公共部分去掉即可：`where d.id is null`
2. `select * from tbl_emp e left join tbl_dept d on e.deptId = d.id where d.id is null;`

```mysql
mysql> select * from tbl_emp e left join tbl_dept d on e.deptId = d.id where d.id is null;
+----+------+--------+------+----------+--------+
| id | NAME | deptId | id   | deptName | locAdd |
+----+------+--------+------+----------+--------+
|  8 | s9   |     51 | NULL | NULL     | NULL   |
+----+------+--------+------+----------+--------+
1 row in set (0.00 sec)

```

------

**right join without common part**

1. tbl_dept表的独有部分：将 right join 结果集中的两表公共部分去掉即可：`where e.id is null`
2. `select * from tbl_emp e right join tbl_dept d on e.deptId = d.id where e.id is null;`

```mysql
mysql> select * from tbl_emp e right join tbl_dept d on e.deptId = d.id where e.id is null;
+------+------+--------+----+----------+--------+
| id   | NAME | deptId | id | deptName | locAdd |
+------+------+--------+----+----------+--------+
| NULL | NULL |   NULL |  5 | FD       | 15     |
+------+------+--------+----+----------+--------+
1 row in set (0.00 sec)

```

------

**full join**

1. mysql 不支持 full join ，但是我们可以通过骚操作实现 full join ，union 关键字用于连接结果集，并且自动去重
2. tbl_emp 与 tbl_dept 的公共部分 + tbl_emp 表的独有部分 + tbl_dept表的独有部分：将 left join 的结果集和 right join 的结果集使用 union 合并即可
3. `select * from tbl_emp e left join tbl_dept d on e.deptId = d.id union select * from tbl_emp e right join tbl_dept d on e.deptId = d.id;`

```mysql
mysql> select * from tbl_emp e left join tbl_dept d on e.deptId = d.id
    -> union
    -> select * from tbl_emp e right join tbl_dept d on e.deptId = d.id;
+------+------+--------+------+----------+--------+
| id   | NAME | deptId | id   | deptName | locAdd |
+------+------+--------+------+----------+--------+
|    1 | z3   |      1 |    1 | RD       | 11     |
|    2 | z4   |      1 |    1 | RD       | 11     |
|    3 | z5   |      1 |    1 | RD       | 11     |
|    4 | w5   |      2 |    2 | HR       | 12     |
|    5 | w6   |      2 |    2 | HR       | 12     |
|    6 | s7   |      3 |    3 | MK       | 13     |
|    7 | s8   |      4 |    4 | MIS      | 14     |
|    8 | s9   |     51 | NULL | NULL     | NULL   |
| NULL | NULL |   NULL |    5 | FD       | 15     |
+------+------+--------+------+----------+--------+
9 rows in set (0.00 sec)

```

------

**full join without common part**

1. tbl_emp 表的独有部分 + tbl_dept表的独有部分
2. `select * from tbl_emp e left join tbl_dept d on e.deptId = d.id where d.id is null union select * from tbl_emp e right join tbl_dept d on e.deptId = d.id where e.id is null;`

```mysql
mysql> select * from tbl_emp e left join tbl_dept d on e.deptId = d.id where d.id is null
    -> union
    -> select * from tbl_emp e right join tbl_dept d on e.deptId = d.id where e.id is null;
+------+------+--------+------+----------+--------+
| id   | NAME | deptId | id   | deptName | locAdd |
+------+------+--------+------+----------+--------+
|    8 | s9   |     51 | NULL | NULL     | NULL   |
| NULL | NULL |   NULL |    5 | FD       | 15     |
+------+------+--------+------+----------+--------+
2 rows in set (0.00 sec)
```

## **SQL优化方案**

不良的SQL往往来自于不恰当的索引设计、不充份的连接条件和不可优化的where子句。

- ### 索引的建立和使用


1. 定义主键的数据列一定要建立索引
2. 定义有外键的数据列一定要建立索引
3. 对于经常查询的数据列最好建立索引
4. 对于需要在指定范围内的快速或频繁查询的数据列
5. 经常用在WHERE子句中的数据列
6. 经常出现在关键字order by、group by、distinct后面的字段，建立索引。如果建立的是复合索引，索引的字段顺序要和这些关键字后面的字段顺序一致，否则索引不会被使用
7. 对于那些查询中很少涉及的列，重复值比较多的列不要建立索引
8. 对于定义为text、image和bit的数据类型的列不要建立索引
9. 对于经常存取的列避免建立索引
10. 限制表上的索引数目。对一个存在大量更新操作的表，所建索引的数目一般不要超过3个，最多不要超过5个。索引虽说提高了访问速度，但太多索引会影响数据的更新操作
11. 对复合索引，按照字段在查询条件中出现的频度建立索引。在复合索引中，记录首先按照第一个字段排序。对于在第一个字段上取值相同的记录，系统再按照第二个字段的取值排序，以此类推。因此只有复合索引的第一个字段出现在查询条件中，该索引才可能被使用,因此将应用频度高的字段，放置在复合索引的前面，会使系统最大可能地使用此索引，发挥索引的作用

- ### SQL语句的执行原理


1. 服务器在接收到查询请求后,并不会马上去数据库查询,而是在数据库中的计划缓存中找是否有相对应的执行计划,如果存在,就直接调用已经编译好的执行计划,节省了执行计划的编译时间
2. 语法效验、语义效验、权限验证
3. 针对SQL进行优化,选择不同的查询算法以最高效的形式返回
4. 语句执行，执行顺序：
   1）  FROM 子句返回初始结果集
   2）  WHERE 子句排除不满足搜索条件的行
   3）  GROUP BY 子句将选定的行收集到 GROUP BY 子句中各个唯一值的组中
   4）  选择列表中指定的聚合函数可以计算各组的汇总值
   5）  此外，HAVING 子句排除不满足搜索条件的行
   6）  计算所有的表达式
   7）  使用order by对结果集进行排序
5. where条件执行原理及效率
   首先要了解Where 条件执行方向是从右向左的（如多条件判断下，会从最后一个条件来判断过滤数据的，依次向前推进判断）
   1）  注意SQL运算符（非、与、或）优先级别，级别越高放最后
   2）  在同运算符内字段值数据范围越大的查询字段放最后

- ### 优化SQL语句的若干方法


1. WHERE 语句中，小表字段写左边
2. 在WHERE中尽量不要使用OR
3. 操作符号： IN  &  NOT IN操作符
   NOT IN操作是强列推荐不使用的，NOT IN会多次扫描表
   推荐方案：用NOT EXISTS 或（外连接+判断为空）方案代替
   而Exists比IN更快，最慢的是NOT操作
   使用in时，在IN后面值的列表中，将出现最频繁的值放在最前面，出现得最少的放在最后面，这样可以减少判断的次数
   另外，多表连接查询时，用IN，sql会先尝试转换成多表连接，转换不成功则先执行IN里面的查询，再查询外层表记录。
4. 注意union和union all的区别。union比union all多做了一步distinct操作。能用union all的情况下尽量不用union
5. 查询时尽量不要返回不需要的行、列。另外在多表连接查询时，尽量改成连接查询，少用子查询
6. Between在某些时候比IN速度更快,Between能够更快地找到范围
7. 从右到左的顺序处理FROM子句中的表名，选择数据量少的表作为基础表
8. 没有必要时不要用DISTINCT和ORDER BY，它们增加了额外的开销
9. 计算记录条数
   和一般的观点相反, count(*) 比count(1)稍快 , 当然如果可以通过索引检索,对索引列的计数仍旧是最快的. 例如 COUNT(CONTRACT_NO)
10. 减少对表的查询
11. 使用表的别名

## **DB2相关知识点**

- ### DB2常用系统命令

  1.查询当前库有N张表

  ```sql
  select count(*) from sysibm.SYSTABLES; 
  ```

  2.查询当前DB2的版本

  ```sql
  select * from sysibm.SYSVERSIONS;
  ```

  3.查询TABLESPACE的状况

  ```sql
  select * from sysibm.SYSTABLESPACES;
  ```

  4.查询DB2的数据类型

  ```sql
  select * from sysibm.SYSDATATYPES;
  ```

  5.查询缓冲池状况

  ```sql
  select * from sysibm.SYSBUFFERPOOLS;
  ```

  6.查询DB2分区

  ```sql
  select * from syscat.DATAPARTITIONS
  ```

  7.查看数据库的存储过程

  ```sql
  select * from SYSCAT.PROCEDURES
  ```

- ### DB2与ORACLE常用语法区别：

  1.取前N条记录：

  ```sql
  Oracle： select * from tablename where rownum <= N;
  DB2：    select * from tablename fetch first N rows only;
  ```

  2.取得系统时间：

  ```sql
  Oracle： Select sysdate from dual;
  DB2：    Select current timestamp from sysibm.sysdummy1;
           Select current timestamp from sysibm.dual;
  ```

  3.空值转换

  ```sql
  Oracle：	Select loginname, nvl(cur_rate,'0') from TableName;
  DB2：	Select loginname, nvl(nullif(cur_rate,’ ’),’0’) from TableName; (DB2v9.5以后已经支持)
  ```

  4.隐性转换

  ```sql
  Oracle： select 'abc' from dual where '1'=1 ;
  DB2：   	select ‘abc’ from sysibm.dual where ‘1’=1;(DB2客户机9.5以前的不支持隐性转换)
  ```

  5.数据类型转换函数

  ```sql
  --转换形式 ORACLE & DB2兼容写法
  --整型转字符型 	  
  to_char(1) ,char(1) ,cast(1 as char)
  --字符串转整型 	  
  to_number('1'), int(‘1’),dec('1') ,cast('1' as int)
  --字符串转浮点型  
  to_number('1.1'), Double(‘1.1’)
  --浮点型转字符串  
  to_char(1.1), char(1.1)
  --字符串转日期
  oracle:  to_date('2007-04-26','yyyy-mm-dd')
  db2: 	 date(‘2007-04-26’)、
           to_date('2007-04-26','yyyy-mm-dd')
  --日期转字符串
  oracle:  to_char(A, 'yyyy-mm-dd hh24:mi:ss')
           to_char(to_date(‘2007-04-29’,‘yyyy-mm-dd’),‘yyyy-mm-dd’)
  db2:	char(date('2007-04-29'));
           char(to_date(‘2007-04-26’,‘yyyy-mm-dd’))
           to_char(to_date(‘2007-04-26’,‘yyyy-mm-dd’),‘yyyy-mm-dd’);
  ```

  6.快速清空表

  ```sql
  Oracle：	 truncate table tableName;
  DB2：     alter table tableName activate not logged initially with empty table;
  ```

  7.创建类似的表

  ```sql
  Oracle：  create table new_table as select * from old_table;
  DB2：     create table new_table like old_table; 
         或 create table new_table as(select * from old_table) ;
  ```


