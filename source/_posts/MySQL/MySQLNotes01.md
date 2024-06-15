---
title: 深入浅出MySQL笔记01--基础篇
categories: MySQL
date: 2022-04-01 17:00:00
typora-root-url: ../
---

## 深入浅出MySQL     基础篇

### 1. SQL基础

##### SQL分类

1. DDL语句：数据定义语言，定义不同的数据段、数据库、表、列、索引等数据库对象，常用关键字：create，drop，alter；
2. DML语句：数据操纵语句，增删改查以及检查数据完整性，常用关键字：insert，delete，update，select；
3. DCL语句：数据控制语句，控制不同数据段直接的许可和访问级别，定义了访问权限和安全级别，主要关键字：grant，revoke。

##### 查看表定义

1. DESC tname

2. show create table tname \G     ----- 可以看到存储引擎和字符集等信息

   ​	\G: 使得记录能按字段竖向排列

##### 修改表

1. 修改表类型：ALTER TABLE tname MODIFY col_def [FIRST | AFTER col_name]

   ​				col_def ：列定义，包含 col_name col_type [约束]

2. 增加表字段：ALTER TABLE tname ADD col_def [FIRST | AFTER col_name]

3. 删除表字段：ALTER TABLE tname DROP  col_name

4. 字段改名：ALTER TABLE tname CHANGE old_col_name col_def [FIRST | AFTER col_name]

   ​		change **vs** modify：都可以修改表的列定义，但change可以修改列名称。另外，change需要写两遍列名，只需要修改列类型时，用modify。 

5. 修改字段排列顺序：FIRST | AFTER col_name

   ​		上述124语句，ADD新增的字段默认在表最后，CHANGE & MODIFY 默认不改变字段位置。

   ​		FIRST ：将该列置于表最前

   ​		AFTER col_name： 将该列置于col_name列后

6. 更改表名： ALTER TABLE tname RENAME new_tname

##### 查询表

1. 查询不重复记录：distinct

2. 排序：order by，默认升序，desc降序

3. 限制：limit，配合order by进行分页显示

   ​				limit offset_start, row_count

4. 聚合：

   1. 语法：

      *SELECT [field] fun_name*

      *FROM tname*

      *[WHERE condition]*

      *[GROUP BY field1, field2, ..., fieldn]*

      *[WITH ROLLUP]*

      *[HAVING condition]*

   2. 说明：

      fun_name: 聚合函数，sum、count(*)、max、min

      GROUP BY：要进行聚合得字段

      WITH ROLLUP： 可选，是否对分类聚合后的结果进行再汇总

      HAVING ：对分类后的结果再进行条件的过滤

      ***注***：HAVING **vs** WHERE ：HAVING 是对聚合后的结果进行条件的过滤，WHERE 是聚合前对记录进行过滤。

5. 表连接：

    + 内连接

   + 外连接
     	- 左连接
     	- 右连接

6. 子查询：

   ​		in、=（子查询记录数唯一）；

   ​		表连接很多情况下优于子查询。

7. 记录联合：

   ​		union（会对结果进行distinct）、union all（把结果直接合并在一起）

   

### 2. MySQL支持的数据类型

1. CHAR、VARCHAR

   ​		检索时，CHAR列会删除尾部的空格，而VARCHAR会保留

2. BINARY

   ​		保存的是ascii码的16进制表示，且会在值的最后填充“0x00”达到指定的长度

3. ENUM 、 SET

   ​			ENUM ：

   + 忽略大小写；
   + 插入不在ENUM 指定范围内的值时，会插入范围内的第一个值；
   + 只允许取单个值
     ​	SET
   + 一次可以取多个成员
   + 对超出允许值范围的值，不允许注入
   + 对于包含重复成员的集合只取一次，eg. （'a,d,a'）-> ('a,d')


### 3. MySQL中的运算符

##### 比较运算符

1. 不等于：<>、!=

2. <=>: null安全的等于

   ​		null = null ---------- null

   ​        null <> null ---------- null

   ​        null <=> null ---------- 1

##### 逻辑运算与null

+ not null ---------- null
+ 1 and null ---------- null （and 操作数中任何一个为null，返回值为null）
+ or  
  + 1 or null  ---------- 1
  + 0 or null ---------- null 
  + null or null ---------- null 
+ xor : 操作数中任何一个为null，返回值为null

##### 位运算

​		位取反：MySQL中常量数字默认以8个字节存储，对1（63个前导0）取反会得到非常大的数

### 4. 常用函数

##### 字符串函数

<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/MySQL/01_stringFunc.png" width="85%"  style="center"/>

1. LPAD、RPAD   “指定的长度len”为填充后的总长度

   LPAD(str, len, padstr)：在str的左边填充给定的字符padstr到指定的长度len，返回填充后的字符串 

   RPAD(str, len, padstr)：在str的右边填充给定的字符padstr到指定的长度len，返回填充后的字符串 

##### 数值函数

|函数					       |说明|	
| ---- | ---- |
|ABS(x)                 |返回x的绝对值|
|BIN(x)                |返回x的二进制（OCT返回八进制，HEX返回十六进制）|
|CEILING(x)             |返回大于x的最小整数值|
|EXP(x)                |返回值e（自然对数的底）的x次方|
|FLOOR(x)                  |返回小于x的最大整数值|
|GREATEST(x1,x2,...,xn)       |返回集合中最大的值|
|LEAST(x1,x2,...,xn)          |返回集合中最小的值|
|LN(x)                   |返回x的自然对数|
|LOG(x,y)              |返回x的以y为底的对数|
|MOD(x,y)                |返回x/y的模（余数）|
|PI()                 |返回pi的值（圆周率）|
|RAND()                   |返回０到１内的随机值,可以通过提供一个参数(种子)使RAND()随机数生成器生成一个指定的值|
|ROUND(x,y)                |返回参数x的四舍五入的有y位小数的值|
|SIGN(x)               |返回代表数字x的符号的值|
|SQRT(x)               |返回一个数的平方根|
|TRUNCATE(x,y)                |返回数字x截短为y位小数的结果|

##### 流程函数

+ if(value,t,f)  : 如果value是真,返回t;否则返回f 

+ ifnull(value1,value2) : 如果valve1不为空则返回value1,否则返回value2

  + 一般在处理含null值的列的数值运算时，用此函数替换null值

+ case when [value1]

  then [result]...else[defalult]END  ：如果value1是真,返回result1,否则返回defalut 

+ case [expr ] when [value1]

  then[result]...else[default]END ：  如果expr等于value1,返回result,否则返回defalut 

  

  





