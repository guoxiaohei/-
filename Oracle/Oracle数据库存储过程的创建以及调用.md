## 1.PLSQL编程

### 1.1 程序结构

PLSQL大小写不区分

PLSQL可以分为三个部分：声明部分、可执行部分、异常处理部分

```plsql
DECLARE
	-- 声明变量，游标。(没有声明的话可以省略)
	I INTEGER;
BEGIN
 	--执行语句
 	--异常处理
END;
```

其中DECLARE部分用来声明变量或游标（结果集类型变量），如果程序中无变量声明可以省略掉。

### 1.2  Hello world

```plsql
BEGIN
	--打印输出语句
	DBMS_OUTPUT_PUT_LINE('Hello world');
END;
	
```

cmd下执行：sqlplus --打开oracle操作指令

​					 set serveroutput on；--打开输出指令

​					执行指令需要后加'/'表示结束；

![image-20201204043451165](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/055c2aeef3d92c59bb0e51dfbde8af77.png)

### 1.3 变量

PLSQL编程中常见的变量分为两大类：

1. 普通数据类型（char，varchar2，date，number，boolean，long）
2. 特殊变量类型（引用型变量，记录型变量）

声明变量的方式为

```plsql
变量名 变量类型（变量长度） 例如：v_name varchar2(20);
```

#### 1.3.1普通变量

变量赋值方式有两种：

1. 直接赋值，如：

   ```plsql
   v_name := '张三';
   ```

2. 语句赋值，（select [value] into [name] from dual）

   ```plsql
   --打印人员个人信息，包括：姓名，薪水，地址
   DECLARE
   	--姓名
   	v_name varchar2(20) := '张三';
   	--薪水
   	v_sal number;
   	--地址
   	v_addr varchar2(200);
   	
   BEGIN
   	--在程序中赋值
   	v_sal := 15000;
   	--语句赋值
   	select '上海安硕信息' INTO v_addr from dual;
   	--打印变量
   	DBMS_OUTPUT.PUT_LINE('姓名：'||v_name||'薪水'||v_sal||'地址'||v_addr);
   END;
   ```

#### 1.3.2 引用型变量

变量的类型和长度取决于表中字段的类型和长度

通过表名.列名%TYPE指定变量的类型和长度，例如：v_name emp.ename%TYPE;

```plsql
--查询emp表中7839号员工的个人信息，打印姓名和薪水
DECLARE
	--姓名
	V_NAME EMP.ENAME%TYPE;
	--薪水
	V_SAL EMP.ESAL%TYPE;

BEGIN
	--查询表中姓名和薪水并赋值给变量
	--注意查询的字段和复制的变量的顺序、个数、类型要一致
	SELECT ENAME,ESAL INTO V_NAME,V_SAL FROM EMP WHERE EMPNO = 1234;
   	
   	--打印变量
   	DBMS_OUTPUT.PUT_LINE('姓名：'||v_name||'薪水'||v_sal);
 
 END;
```

引用型变量的好处：

使用普通变量定义方式，需要知道表中列的类型，而使用引用类型，不需要考虑列的类型，使用%TYPE更加适应于对数据库定义的更新。

#### 1.3.3 记录型变量

接受表中的一整行记录，相当于java中的一个对象

语法：变量名称  表名%ROWTYPE，例如： v_emp emp%rowtype;

```plsql
--查询emp表中某员工的个人信息，打印姓名和薪水
DECLARE
	--记录型变量接受一行
	V_EMP EMP%ROWTYPE;
BEGIN
	--记录型变量默认接受表中的一行数据，不能指定字段
	SELECT * INTO V_EMP FROM EMP WHERE EMPNO = 1111;
    --打印变量，通过变量名，属性的方式获取变量中的值
    DBMS_OUTPUT.PUT_LINE('姓名：'||V_EMP.ENAME||'，薪水：'||V_EMP.SAL);
 
 END;
```

如果有一个表，有一百个字段，那么你程序要使用这100个字段的话，如果使用引用型变量一个个声明，会特别麻烦，记录型变量可以方便的解决这个问题。

错误的使用：

1. 记录型变量只能存储一个完整的行数据

   ![image-20201204061629698](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/fb57685a3a060d82da3fe85ce7e3e59f.png)

2. 返回的行太多了，记录型变量无法接受

   ![image-20201204061900451](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/8288115d8313418e06f6b63a11a5b4ea.png)



### 1.4 流程控制

#### 1.4.1 条件分支

语法：

```plsql
BEGIN

	IF 条件1 THEN 执行1
	
    	ELSIF 条件2 THEN 执行2
    	
    	ELSE 执行3
    	
    END IF;
    
END;
```

【实例】判断emp表中记录是否超过20条，10-20之间，或者10条以下

```plsql
DECLARE
	--声明变量，接受EMP表中的数据记录
	V_COUNT NUMBER;
BEGIN
	--查询emp表中的记录数赋值给变量
	SELECT COUNT(1) INTO V_COUNT FROM EMP;
	--判断打印
	IF V_COUNT > 20 THEN 
			DBMS_OUTPUT.PUT_LINE('EMP表中的记录数超过了20条，为：'||V_COUNT);
		ELSIF V_COUNT >=10 THEN
			DBMS_OUTPUT.PUT_LINE('EMP表中的记录数为10-20条，为：'||V_COUNT);
		ELSE 
			DBMS_OUTPUT.PUT_LINE('EMP表中的记录数小于10条，为：'||V_COUNT);
	END IF;
END;	
```

#### 1.4.2循环

在ORACLE中有三种循环方式：loop

语法1：

```PLSQL
BEGIN
	LOOP
		EXIT WHEN 退出循环条件
	END LOOP;
END
```

语法2：

```plsql
BEGIN
	WHILE 循环条件 LOOP
	END LOOP;
END;
```

语法3：

```PLSQL
BEGIN
	FOR I IN 1...10 LOOP
	END LOOP;
END;
```

打印数字1-10

```PLSQL
DECLARE
	--声明循环变量并赋初始值
	V_NUM NUMBER := 1;
BEGIN
	LOOP
		--设置退出循环条件
		EXIT WHEN V_NUM > 10;
		DBMS_OUTPUT.PUT_LINE(V_NUM);
		--循环变量自增
		V_NUM := V_NUM + 1; 
	END LOOP;
END;
```



## 2. 游标

### 2.1  什么是游标

用于临时存储一个查询返回的多行数据（结果集，类似于Java的JDBC连接返回的ResultSet集合），通过遍历游标，可以逐行访问处理该结果集的数据。

游标的使用方式：声明--->打开--->读取--->关闭

### 2.2 语法

游标声明：

CURSOR  游标名[(参数列表)]  IS  查询语句；

游标的打开：

OPEN 游标名；

游标的取值：

FETCH 游标名 INTO 变量列表；

游标的关闭：

CLOSE 游标名；

### 2.3 游标的属性

| 游标的属性 | 返回值类型 | 说明                                        |
| ---------- | ---------- | ------------------------------------------- |
| %ROWCOUNT  | 整型       | 获得FETCH语句返回的数据行数                 |
| %FOUND     | 布尔型     | 最近的FETCH语句返回一行数据则为真，否则为假 |
| %NOTFOUND  | 布尔型     | 与%FOUND属性返回值相反（默认为false）       |
| %ISOPEN    | 布尔型     | 游标已经打开时值为真，否则为假              |

其中%NOTFOUND是在游标中找不到元素的时候返回TRUE,通常用来判断退出循环

### 2.4 创建和使用

```plsql
--使用游标查询emp表中所有员工的姓名和工资，并将其依次打印出来
DECLARE
	--声明游标
	CURSOR C_EMP IS SELECT ENAME,SAL FROM EMP;
	--声明变量接收游标中的数据
	V_ENAME EMP.ENAME%TYPE;
	V_SAL EMP.SAL%TYPE;
BEGIN
	--打开游标
	OPEN C_EMP;
	--遍历游标
	LOOP
	--获取游标中的数据
	FETCH C_EMP INTO V_ENAME,V_SAL;
	EXIT WHEN C_EMP%NOTFOUND;
	DBMS_OUTPUT.PUT_LINE(V_ENAME||'-'||V_SAL);
	END LOOP;
	
	--关闭游标
	CLOSE C_EMP;
END;
```

### 2.5 带参数的游标

```plsql
--使用游标查询emp表中所有部门号为10的员工的姓名和工资，并将其依次打印出来
DECLARE
	--声明游标(定义参数)
	CURSOR C_EMP(V_DEPTNO EMP.DEPTNO%TYPE) IS 
		SELECT ENAME,SAL FROM EMP WHERE DEPTNO =V_DEPTNO;
	--声明变量接收游标中的数据
	V_ENAME EMP.ENAME%TYPE;
	V_SAL EMP.SAL%TYPE;
BEGIN
	--打开游标（插入参数）
	OPEN C_EMP(10);
	--遍历游标
	LOOP
	--获取游标中的数据
	FETCH C_EMP INTO V_ENAME,V_SAL;
	EXIT WHEN C_EMP%NOTFOUND;
	DBMS_OUTPUT.PUT_LINE(V_ENAME||'-'||V_SAL);
	END LOOP;
	
	--关闭游标
	CLOSE C_EMP;
END;
```

## 3.异常

### 3.1系统定义异常

​	NO_DATA_FOUND(没有找到数据)

​	TOO_MANY_ROWS(SELECT ...INTO 语句匹配多个行)

​	ZERO_DIVIDE(被零除)

​	VALUE_ERROR(算术或转换错误)

​	TIMEOUT_ON_RESOURCE(在等待资源时发生超时)

​	范例1：写出被0除的异常plsql程序

```plsql
DECLARE
	N NUMBER;
BEGIN
	N := 1 / 0;
	EXCEPTION
		WHEN ZERO_DIVIDE THEN
			DBMS_OUTPUT.PUT_LINE('被零除异常');
		WHEN VALUE_ERROR THEN
			DBMS_OUTPUT.PUT_LINE('数值转换错误');
		WHEN OTHERS THEN --others相当于java中EXCEPTION，可以捕获一切异常
			DBMS_OUTPUT.PUT_LINE('其他错误');
END;
```

### 3.2用户自定义异常

用户自定义异常，在声明中定义异常

使用raise 【异常名】抛出异常；

【异常名】EXCEPTION定义异常

示例：

```plsql
DECLARE
	CURSOR EMP_CUR IS SELECT * FROM EMP WHERE DEPTNO = 40;
	NO_DATE EXCEPTION;--定义异常
	PEMP EMP%ROWTYPE;--定义记录变量用来接受游标内容
BEGIN
	OPEN EMP_CUR;
		FETCH EMP_CUR INTO PEMP;
		IF EMP_CUR%NOTFOUND THEN 
			RAISE NO_DATE;
		END IF;
	CLOSE EMP_CUR;
EXCEPTION 
	WHEN NO_DATE THEN 
		DBMS_OUTPUT.PUT_LINE('没有找到数据');
END;
```

## 4.存储过程

### 4.1概念作用

PLSQL将一个个PLSQL的业务处理过程存储起来进行复用，这些被存储起来的PLSQL被称之为存储过程（procedure）

**存储过程作用：**

在开发程序中，为了一个特定的业务功能，会向数据库进行多次连接关闭（连接和关闭很耗费资源），需要对数据库进行多次I/O读写，性能比较低。如果把这些业务放到PLSQL中，在应用程序中只需要调用PLSQL就可以做到连接关闭一次数据库就可以实现我们的业务，可以大大提高效率。

**存储过程与存储函数的区别：**

1. 定义的语法不一样，存储过程为procedure，存储函数为function
2. function有返回值（return）
3. function可以直接在select语句中使用
4. 存储过程可以通过out类型来返回参数

### 4.2语法

```plsql
CREATE OR REPLACE PROCEDURE 过程名称 [（参数列表）] IS
BEGIN

END [过程名称];
```

根据参数的类型，可以分为三类：

1. 不带参数的
2. 带输入参数的
3. 带输入输出参数（返回值）的

### 4.3无参存储

#### 4.3.1创建存储过程

通过plsql Developer或者语句创建存储过程

![image-20201207060848119](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/e7da708a9107ec129d51edb0ddbeaf48.png)

```plsql
--创建一个存储过程
CREATE OR REPLACE PROCEDURE P_HELLO IS
BEGIN
	DBMS_OUTPUT.PUT_LINE('hello world');
END;
```

通过工具查看创建的存储过程：

![image-20201207061120769](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/834a19683ca5bff0b3ed121854709b7f.png)

#### 4.3.2 调用存储过程

1. 通过PLSQL程序调用

   ```plsql
   BEGIN
   	--直接输入调用的存储过程的名称
   	P_HELLO;
   END;
   ```

   

2. 在CMD  --->  sqlplus中通过EXEC命令调用：

   ![image-20201207061426375](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/07828fbb59c3d2619145bfdca0aba53f.png)

提示：sqlplus中显示结果的前提是需要set severoutput on；

注意：

第一个问题：is和as是可以互用的，用哪个都没关系的

第二个问题：存储过程中不需要写declare关键字

### 4.4带输入参数的存储过程

【实例】查询并打印某个员工的姓名和薪水--存储过程：要求，调用的时候传入员工编号，自动控制台打印。

```plsql
CREATE OR REPLACE PROCEDURE P_QUERYNAMEANDSAL(I_EMPNO IN EMP.EMPNO%TYPE) AS
--声明变量
	V_NAME EMP.ENAME%TYPE;
	V_SAL EMP.SAL%TYPE;
BEGIN
--查询emp表中某个员工的姓名和薪水并赋值给变量
	SELECT ENAME,SAL INTO V_NAME,V_SAL FROM EMP WHERE EMPNO = I_EMPNO;
	DBMS_OUTPUT.PUT_LINE(V_NAME|'-'|V_SAL);
END;

```

SQLPLUS命令调用：

```
EXEC P_QUERYNAMEANDSAL(7000);
```

PLSQL程序调用

```PLSQL
BEGIN
	P_QUERYNAMEANDSAL(7000);
END;
```

### 4.5带输出参数的存储过程

#### 4.5.1 创建存储过程

示例：输入员工编号，把薪水作为返回值输出，给调用的程序使用

```plsql
CREATE OR REPLACE PROCEDURE P_QUERYNAMEANDSAL(I_EMPNO IN EMP.EMPNO%TYPE,O_SAL OUT EMP.SAL%TYPE) AS
BEGIN
--查询emp表中某个员工的姓名和薪水并赋值给变量
	SELECT SAL INTO O_SAL FROM EMP WHERE EMPNO = I_EMPNO;
END;
```

#### 4.5.2调用存储过程并取得返回值

```plsql
DECLARE
	--声明变量接收存储过程中的输出参数
	V_SAL EMP.SAL%TYPE;
BEGIN
	P_QUERYNAMEANDSAL(7000,V_SAL);
	DBMS_OUTPUT.PUT_LINE(V_SAL);
END;
```

如果以游标作为输入参数，需用‘SYS_REFCURSOR’作为返回类型，如下所示：

```PLSQL
--以游标作为输出参数
CREATE OR REPLACE PROCEDURE PRO_EMP_LIST(DNO IN NUMBER,EMPLIST OUT SYS_REFCURSOR)
AS
BEGIN
	--存储过程中只需要open，不需要close，close可以放到调用方法中
	OPEN EMPLIST FOR SELECT * FROM EMP WHERE DEPTNO = DNO;
END;
```

## 5.触发器

### 5.1 触发器的语法

```plsql
CREATE [OR REPLACE] TRIGGER 触发器名
BEFORE|AFTER --什么时候触发
INSERT|UPDATE|DELETE --什么方法触发
ON 表名
FOR EACH ROW  -- 语句中有‘:new|:old’ 的时候必须写
BEGIN
END;
```

### 5.2 示例

```plsql
--触发器应用（通过序列在插入数据的时候，将ID用序列赋值）
CREATE OR REPLACE TRIGGER TRI_EMP_INSERT
BEFORE
INSERT
ON EMP
FOR EACH ROW
BEGIN
	--插入新数据时，用EMP_SEQ序列给id赋值
	SELECT EMP_SEQ.NEXTVAL INTO :NEW.EMPNO FROM DUAL;
END;
```

## 6.java中调用存储过程

示例：

```java
//1.加载驱动
Class.forName("orcal.jdbc.driver.OracleDriver");
//2.获取连接对象
String url ="jdbc:oracle:thin:@localhost:1521:xe";
String user = "xxxx";
String password = "tiger";
Connection conn = DriverManager.getConnection(url,user,password);
//3.获得语句对象
String sql = "{call p_querysal_out(?,?)}";
CallableStatement call = conn.prepareCall(sql);
//4.设置输入参数
call.setInt(1,1000);
//5.注册输出参数
call.registerOutParameter(2,OracleTypes.DOUBLE);
//6.执行存储过程
call.execute();
//7.获取输出参数
double sal = call.getDouble(2);
//8.释放资源
call.close();
conn.close();


```

