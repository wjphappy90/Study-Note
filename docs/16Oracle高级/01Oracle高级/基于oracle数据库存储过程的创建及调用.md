# 基于oracle数据库存储过程的创建及调用

教学大纲：

1.  PLSQL编程：Hello World、程序结构、变量、流程控制、游标.
2.  存储过程：概念、无参存储、有参存储（输入、输出）.
3. JAVA调用存储存储过程.



# 1.  PLSQL编程

## 1.1.  概念和目的

什么是PL/SQL?

1. PL/SQL（Procedure Language/SQL）
2.  PLSQL是Oracle对sql语言的过程化扩展  (类似于Basic)
3. 指在SQL命令语言中增加了过程处理语句（如分支、循环等），使SQL语言具有过程处理能力。


 



## 1.2.  程序结构

通过Plsql Developer工具的Test Window 创建 程序模版或者通过语句在SQL Window编写

提示：PLSQL语言的大小写是不区分的

PL/SQL可以分为三个部分：声明部分、可执行部分、异常处理部分。

```plsql
-- Created on 2018/3/21 by ADMINISTRATOR 
DECLARE
  -- 声明变量、游标。
  I INTEGER;
BEGIN
  -- 执行语句

  --[异常处理]

END;
```

其中 DECLARE部分用来声明变量或游标（结果集类型变量），如果程序中无变量声明可以省略掉

## 1.3.  Hello World

```plsql
BEGIN

  --打印hello world

  DBMS_OUTPUT.PUT_LINE('hello world');

END;
```

其中DBMS_OUTPUT 为oracle内置程序包，相当于Java中的System.out,而PUT_LINE（）是调用的方法,相当于println（）方法

 在sqlplus中也可以编写运行PLSQL程序：

```powershell
SQL> BEGIN
  2
  3    --打印hello world
  4
  5    DBMS_OUTPUT.PUT_LINE('hello world');
  6
  7  END;
  8  /

PL/SQL 过程已成功完成。
```

执行结束后并未显示输出的结果，默认情况下，输出选项是关闭状态的 我们需要开启一下 **set serveroutput on**

![](img/Image 4.png)

SQLPLUS中执行PLSQL程序 需要在程序最后添加一个  **/**  标识程序的结束



## 1.4.  变量



PLSQL编程中常见的变量分两大类：

1.  普通数据类型（char,varchar2, date, number, boolean, long）


2.  特殊变量类型（引用型变量、记录型变量）

声明变量的方式为

```plsql
变量名  变量类型（变量长度）  例如： v_name  varchar2(20);
```



### 1.4.1.  普通变量

变量赋值的方式有两种：

1. 直接赋值语句      :=   比如:    v_name  := 'zhangsan'
2.  语句赋值，使用select …into … 赋值：（语法 select 值 into 变量）

【示例】打印人员个人信息，包括： 姓名、薪水、地址

```plsql
-- 打印人员个人信息，包括： 姓名、薪水、地址
DECLARE
  -- 姓名
  V_NAME VARCHAR2(20) := '张三'; -- 声明变量直接赋值
  --薪水
  V_SAL NUMBER;
  --地址
  V_ADDR VARCHAR2(200);

BEGIN

  --在程序中直接赋值
  V_SAL := 1580;

  --语句赋值
  SELECT '上海市传智播客' INTO V_ADDR FROM DUAL;

  --打印变量
  DBMS_OUTPUT.PUT_LINE('姓名：' || V_NAME || ',薪水：' || V_SAL || ',地址：' ||V_ADDR);

END;
```





### 1.4.2.   引用型变量

变量的类型和长度取决于表中字段的类型和长度

通过**表名.列名%TYPE**指定变量的类型和长度，例如： v_name  emp.ename%TYPE;

【示例】查询emp表中7839号员工的个人信息，打印姓名和薪水

```plsql
-- 查询emp表中7839号员工的个人信息，打印姓名和薪水
DECLARE
  -- 姓名
  V_NAME EMP.ENAME%TYPE; -- 声明变量直接赋值
  --薪水
  V_SAL EMP.SAL%TYPE;

BEGIN
  --查询表中的姓名和薪水并赋值给变量
  --注意查询的字段和赋值的变量的顺序、个数、类型要一致
  SELECT ENAME, SAL INTO V_NAME, V_SAL FROM EMP WHERE EMPNO = 7839;

  --打印变量
  DBMS_OUTPUT.PUT_LINE('姓名：' || V_NAME || ',薪水：' || V_SAL);

END;
```



引用型变量的好处：

 使用普通变量定义方式，需要知道表中列的类型，而使用引用类型，不需要考虑列的类型，使用%TYPE是非常好的编程风格，因为它使得PL/SQL更加灵活，更加适应于对数据库定义的更新。

 

### 1.4.3.  记录型变量

 接受表中的一整行记录，相当于Java中的一个对象

语法： 变量名称   表名%ROWTYPE， 例如： **v_emp emp%rowtype;**

 

【示例】

查询并打印7839号员工的姓名和薪水

```plsql
-- 查询emp表中7839号员工的个人信息，打印姓名和薪水
DECLARE
  -- 记录型变量接受一行
  V_EMP EMP%ROWTYPE;

BEGIN
  --记录型变量默认接受表中的一行数据，不能指定字段。
  SELECT * INTO V_EMP FROM EMP WHERE EMPNO = 7839;

  --打印变量，通过变量名.属性的方式获取变量中的值
  DBMS_OUTPUT.PUT_LINE('姓名：' || V_EMP.ENAME || ',薪水：' || V_EMP.SAL);

END;
```

如果有一个表，有100个字段，那么你程序如果要使用这100字段话，如果你使用引用型变量一个个声明，会特别麻烦，记录型变量可以方便的解决这个问题 

错误的使用：

1．  记录型变量只能存储一个完整的行数据

 ![](img/Image 5.png)

2．返回的行太多了，记录型变量也接收不了

 ![](img/Image 6.png)

 

## 1.5.  流程控制

### 1.5.1.  条件分支

语法：

```plsql
BEGIN

  IF 条件1 THEN 执行1
    
   ELSIF 条件2 THEN 执行 2
  
   ELSE 执行3
    
  END IF; 
  
END;
```

 注意关键字：ELSIF 

【示例】判断emp表中记录是否超过20条，10-20之间，或者10条以下

```plsql
DECLARE
  --声明变量接受emp表中的记录数
  V_COUNT NUMBER;

BEGIN

  --查询emp表中的记录数赋值给变量

  SELECT COUNT(1) INTO V_COUNT FROM EMP;

  --判断打印

  IF V_COUNT > 20 THEN
    DBMS_OUTPUT.PUT_LINE('EMP表中的记录数超过了20条为：' || V_COUNT || '条。');

  ELSIF V_COUNT >= 10 THEN
    DBMS_OUTPUT.PUT_LINE('EMP表中的记录数在10~20条之间为：' || V_COUNT || '条。');

  ELSE
    DBMS_OUTPUT.PUT_LINE('EMP表中的记录数在10条以下为：' || V_COUNT || '条。');

  END IF;

END;
```

 

### 1.5.2.  循环

在ORACLE中有三种循环方式，这里我们不展开,只介绍其中一种：loop循环

语法：

```plsql
BEGIN
  LOOP
  	EXIT WHEN 退出循环条件  
  END LOOP;
END;
```

【示例】打印数字1-10

 ```plsql
DECLARE
  --声明循环变量并赋初值
  V_NUM NUMBER := 1;

BEGIN

  LOOP
  
    EXIT WHEN V_NUM > 10;
    
    DBMS_OUTPUT.PUT_LINE(V_NUM);
  
    --循环变量自增
    V_NUM := V_NUM + 1;
  
  END LOOP;

END;
 ```



# 2.  游标

## 2.1.  什么是游标

用于临时存储一个查询返回的多行数据（结果集,类似于Java的Jdbc连接返回的ResultSet集合），通过遍历游标，可以逐行访问处理该结果集的数据。

游标的使用方式：声明--->打开--->读取--->关闭

## 2.2.  语法

游标声明：

CURSOR  游标名[(参数列表)]    IS 查询语句;

游标的打开：

OPEN 游标名;

游标的取值：

FETCH 游标名 INTO 变量列表;

游标的关闭：

CLOSE 游标名;

## 2.3.  游标的属性

| 游标的属性    | 返回值类型 | 说明                                        |
| ------------- | ---------- | ------------------------------------------- |
| %ROWCOUNT     | 整型       | 获得FETCH语句返回的数据行数                 |
| %FOUND        | 布尔型     | 最近的FETCH语句返回一行数据则为真，否则为假 |
| **%NOTFOUND** | 布尔型     | 与%FOUND属性返回值相反                      |
| %ISOPEN       | 布尔型     | 游标已经打开时值为真，否则为假              |

 其中 %NOTFOUND是在游标中找不到元素的时候返回TRUE,通常用来判断退出循环

## 2.4.  创建和使用

【示例】使用游标查询emp表中所有员工的姓名和工资，并将其依次打印出来。

 ```plsql
--使用游标查询emp表中所有员工的姓名和工资，并将其依次打印出来。
DECLARE
  --声明游标
  CURSOR C_EMP IS
    SELECT ENAME, SAL FROM EMP;

  --声明变量用来接受游标中的元素
  V_ENAME EMP.ENAME%TYPE;

  V_SAL EMP.SAL%TYPE;

BEGIN

  --打开游标
  OPEN C_EMP;

  --遍历游标中的值
  LOOP
  
    --通过FETCH语句获取游标中的值并赋值给变量
    FETCH C_EMP
      INTO V_ENAME, V_SAL;
  
    --通过%NOTFOUND判断是否有值,有值打印,没有则退出循环
    EXIT WHEN C_EMP%NOTFOUND;
  
    DBMS_OUTPUT.PUT_LINE('姓名:' || V_ENAME || ',薪水:' || V_SAL);
  
  END LOOP;

  --关闭游标
  CLOSE C_EMP;

END;
 ```

执行结果:

![](img/Image 7.png)



## 2.5.  带参数的游标

【示例】使用游标查询并打印某部门的员工的姓名和薪资，部门编号为运行时手动输入。

```plsql
 --使用游标查询并打印某部门的员工的姓名和薪资，部门编号为运行时手动输入。
DECLARE
  --声明游标传递参数
  CURSOR C_EMP(V_EMPNO EMP.EMPNO%TYPE) IS
    SELECT ENAME, SAL FROM EMP WHERE EMPNO = V_EMPNO;

  --声明变量用来接受游标中的元素
  V_ENAME EMP.ENAME%TYPE;

  V_SAL EMP.SAL%TYPE;

BEGIN

  --打开游标并传递参数
  OPEN C_EMP(10);

  --遍历游标中的值
  LOOP

 


       --通过%NOTFOUND判断是否有值,有值打印,没有则退出循环
       EXIT WHEN C_EMP%NOTFOUND;
       
       --通过FETCH语句获取游标中的值并赋值给变量
    FETCH C_EMP
      INTO V_ENAME, V_SAL;

    DBMS_OUTPUT.PUT_LINE('姓名:' || V_ENAME || ',薪水:' || V_SAL);


  END LOOP;

  --关闭游标
  CLOSE C_EMP;

END;
```

 注意:%NOTFOUND属性默认值为FLASE,所以在循环中要注意判断条件的位置.如果先判断在FETCH会导致最后一条记录的值被打印两次(多循环一次默认);

 

 

# 3.   存储过程

## 3.1.  概念作用

之前我们编写的PLSQL程序可以进行表的操作,判断,循环逻辑处理的工作,但无法重复调用.

可以理解之前的代码全都编写在了main方法中,是匿名程序. JAVA可以通过封装对象和方法来解决复用问题

PLSQL是将一个个PLSQL的业务处理过程存储起来进行复用,这些被存储起来的PLSQL程序称之为存储过程

存储过程作用：

1，  在开发程序中，为了一个特定的业务功能，会向数据库进行多次连接关闭(连接和关闭是很耗费资源), 需要对数据库进行多次I/O读写，性能比较低。如果把这些业务放到PLSQL中，在应用程序中只需要调用PLSQL就可以做到连接关闭一次数据库就可以实现我们的业务,可以大大提高效率.

2，  ORACLE官方给的建议：能够让数据库操作的不要放在程序中。在数据库中实现基本上不会出现错误，在程序中操作可能会存在错误.(如果在数据库中操作数据,可以有一定的日志恢复等功能.)

 

## 3.2.  语法

```plsql
CREATE OR REPLACE PROCEDURE 过程名称[(参数列表)] IS
BEGIN

END [过程名称];
```

根据参数的类型，我们将其分为3类讲解：

l 不带参数的

l 带输入参数的

l 带输入输出参数(返回值)的。

 

## 3.3.  无参存储

### 3.3.1.  创建存储

 通过Plsql Developer或者语句创建存储过程:

![](img/Image 8.png)



【示例】 通过调用存储过程打印hello world

创建存储过程:

```plsql
--通过调用存储过程打印hello world
CREATE OR REPLACE PROCEDURE P_HELLO IS
BEGIN

  DBMS_OUTPUT.PUT_LINE('hello world');

END P_HELLO;
```

通过工具查看创建好的存储过程:

![](img/Image 9.png)





### 3.3.2.  调用存储过程

1. 通过PLSQL程序调用：

```plsql
BEGIN
 --直接输入调用存储过程的名称
  P_HELLO;

END P_HELLO;
```



2. 在SQLPLUS中通过EXEC命令调用:

 ![52161711055](img/1521617110557.png)

 提示:SQLPLUS中显示结果的前提是需要 set serveroutput on

注意：

第一个问题：is和as是可以互用的，用哪个都没关系的
第二个问题：过程中没有declare关键字，declare用在语句块中



## 3.4.  带输入参数的存储过程

【示例】查询并打印某个员工（如7839号员工）的姓名和薪水--存储过程：要求，调用的时候传入员工编号，自动控制台打印。

```plsql
--查询并打印某个员工（如7839号员工）的姓名和薪水
--要求，调用的时候传入员工编号，自动控制台打印。
CREATE OR REPLACE PROCEDURE P_QUERYNAMEANDSAL(I_EMPNO IN EMP.EMPNO%TYPE) IS
  --声明变量接受查询结果
  V_ENAME EMP.ENAME%TYPE;
  V_SAL   EMP.SAL%TYPE;

BEGIN

  --根据用户传递的员工号查询姓名和薪水
  SELECT ENAME, SAL INTO V_ENAME, V_SAL FROM EMP WHERE EMPNO = I_EMPNO;

  --打印结果
  DBMS_OUTPUT.PUT_LINE('姓名:' || V_ENAME || ',薪水:' || V_SAL);

END P_QUERYNAMEANDSAL;
```



命令调用：

```powershell
SQL> exec p_querynameandsal(7839);
姓名:KING,薪水:5000

PL/SQL 过程已成功完成。
```

PLSQL程序调用：

 ```plsql
BEGIN

  P_QUERYNAMEANDSAL(7839);

END;
 ```

执行结果:

![52161758413](img/1521617584136.png)

 

## 3.5.  带输出参数的存储过程

【示例】输入员工号查询某个员工（7839号员工）信息，要求，将薪水作为返回值输出，给调用的程序使用。

```plsql
 --输入员工号查询某个员工（7839号员工）信息，要求，将薪水作为返回值输出，给调用的程序使用。
CREATE OR REPLACE PROCEDURE P_QUERYSAL_OUT(I_EMPNO IN EMP.EMPNO%TYPE,O_SAL OUT EMP.SAL%TYPE) IS

BEGIN

  SELECT SAL INTO O_SAL FROM EMP WHERE EMPNO = I_EMPNO;

END P_QUERYSAL_OUT;
```

PLSQL程序调用：

```plsql
DECLARE
  --声明一个变量接受存储过程的输出参数
  V_SAL EMP.SAL%TYPE;

BEGIN

  P_QUERYSAL_OUT(7839, V_SAL); --注意参数的顺序

  DBMS_OUTPUT.PUT_LINE(V_SAL);

END; 
```

注意:调用的时候,参数要与定义的参数的顺序和类型一致.

 

## 3.7. JAVA程序调用存储过程

需求:如果一条语句无法实现结果集, 比如需要多表查询,或者需要复杂逻辑查询,我们可以选择调用存储查询出你的结果.

### 3.7.1.  分析jdk API

![52162118986](img/1521621189862.png)

 通过Connection对象的prepareCall方法可以调用存储过程

![52162141526](img/1521621415263.png)

得出结论: 通过Connection对象调用prepareCall方法传递一个转义sql语句调用存储过程, 输入参数直接调用set方法传递.输出参数需要注册后,执行存储过程,通过get方法获取.参数列表的下标是从1开始的



### 3.7.2. 实现代码

准备环境：

l  导入Oracle的jar包

【示例】通过员工号查询员工的姓名和薪资

```java
package cn.itcast.oracle.jdbc;

import oracle.jdbc.OracleTypes;
import java.sql.CallableStatement;
import java.sql.Connection;
import java.sql.DriverManager;

public class ProcedureTest {
    public static void main(String[] args) throws Exception {
        //1.加载驱动
        Class.forName("oracle.jdbc.driver.OracleDriver");

        //2.获得连接对象

        //2.1 设置连接字符串
        String url ="jdbc:oracle:thin:@localhost:1521:xe" ;

        String name = "scott";

        String password = "tiger";

        Connection conn = DriverManager.getConnection(url, name, password);


        //3.获取语句对象
        String sql = "{call p_querysal_out(?,?)}";//转义语法,{call  存储过程(参数列表)}
        CallableStatement call = conn.prepareCall(sql);

        //4.设置输入参数
        call.setInt(1,7839);

        //5.注册输出参数
        call.registerOutParameter(2, OracleTypes.DOUBLE);

        //6.执行存储过程
        call.execute();

        //7.获取输出参数
        double sal = call.getDouble(2);
        System.out.println("薪水:"+sal);

        //8.释放资源
        call.close();
        conn.close();

    }
}
```

