### 日志文件

**错误日志(errorlog)** 

默认是开启的，而且从**5.5.7**以后无法关闭错误日志，错误日志记录了运行过程中**遇到的所有严重的错误 信息**,以及 MySQL**每次启动和关闭的详细信息**。 

**二进制日志(bin log)** 

记录数据变化 

binlog记录了**数据库所有的ddl语句和dml语句，但不包括select语句内容**，语句以事件的形式保存，描 述了数据的变更顺序，binlog还包括了每个更新语句的执行时间信息。如果是DDL语句，则直接记录到 binlog日志，而**DML语句，必须通过事务提交才能记录到binlog日志中**。 生产中开启 

数据备份、恢复、主从 

**通用查询日志(general query log)** 

啥都记录 耗性能 生产中不开启 **慢查询日志(slow query log)** 

SQL调优 定位慢的 select **默认是关闭的。** 需要通过以下设置进行开启: 

```properties
#开启慢查询日志
slow_query_log=ON
#慢查询的阈值
long_query_time=3
#日志记录文件如果没有给出file_name值， 默认为主机名，后缀为-slow.log。如果给出了文件名，但不 是绝对路径名，文件则写入数据目录。
slow_query_log_file=file_name
```

记录执行时间超过**long_query_time**秒的所有查询，便于收集查询时间比较长的SQL语句 



**重做日志(redo log)**

**回滚日志(undo log)**

**中继日志(relay log)** 

![image-20191112214925911](/Users/lee/Library/Application Support/typora-user-images/image-20191112214925911.png)

看日志开启情况: 

```sql
show variables like 'log_%';
```

### 数据文件

```sql
SHOW VARIABLES LIKE '%datadir%';
```

#### InnoDB数据文件 

**.frm文件:**主要存放与表相关的数据信息,主要包括**表结构的定义信息** 

**.ibd:**使用**独享表空间**存储**表数据和索引**信息，一张表对应一个ibd文件。 

**ibdata文件:**使用**共享表空间**存储**表数据和索引**信息，所有表共同使用一个或者多个ibdata文 件。 

#### MyIsam数据文件

**.frm文件:**主要存放与表相关的数据信息,主要包括**表结构的定义信息** 

**.myd文件:**主要用来存储**表数据信息**。

 **.myi文件:**主要用来存储**表数据文件中任何索引的数据树。** 



## 二、MySQL索引篇

### 索引介绍

#### 索引是什么

官方介绍索引是帮助MySQL**高效获取数据**的**数据结构**。更通俗的说，数据库索引好比是一本书前面的目 

录，能**加快数据库的查询速度**。 

#### 索引的优势和劣势

**优势:**

**可以提高数据检索的效率，降低数据库的IO成本**，类似于书的目录。 -- 检索 

通过**索引列对数据进行排序**，降低数据排序的成本，降低了CPU的消耗。 --排序 

- 被索引的列会自动进行排序，包括【单列索引】和【组合索引】，只是组合索引的排序要复杂一 些。
- 如果按照索引列的顺序进行排序，对应order by语句来说，效率就会提高很多。
-  where 索引列 在存储引擎层 处理 索引下推 ICP 
- 覆盖索引 select 字段 字段是索引

 **劣势:** 

- 索引会占据磁盘空间 

- 索引虽然会提高查询效率，但是会降低更新表的效率**。比如每次对表进行增删改操作， MySQL不仅要保存数据，还有保存或者更新对应的索引文件。 



### 索引的分类

**单列索引**

**组合索引**

**全文索引**

**空间索引**

**位图索引** 	Oracle索引的使用



### 索引的使用

**创建索引** 

- 单列索引之普通索引 

```mysql
CREATE INDEX index_name ON table(column(length)) ;
ALTER TABLE table_name ADD INDEX index_name (column(length)) ;
```

- 单列索引之唯一索引 

```mysql
CREATE UNIQUE INDEX index_name ON table(column(length)) ;
alter table table_name add unique index index_name(column);
```



- 单列索引之全文索引 

```mysql
CREATE FULLTEXT INDEX index_name ON table(column(length)) ;
alter table table_name add fulltext index_name(column)
```

- 组合索引 

```mysql
ALTER TABLE article ADD INDEX index_titme_time (title(50),time(10)) ;
```



**删除索引**

```mysql
DROP INDEX index_name ON table
```

**查看索引** 

```mysql
SHOW INDEX FROM table_name \G
```



### 索引原理分析

#### 索引的存储结构

**索引存储结构** 

- 索引是在**存储引擎中实现**的，也就是说不同的存储引擎，会使用不同的索引

- **MyISAM和InnoDB存储引擎**:只支持**B+ TREE索引**， 也就是说**默认使用BTREE，不能够更换** 

- MEMORY/HEAP存储引擎:支持HASH和BTREE索引 

**B树和B+树** 

数据结构示例网站: 

https://www.cs.usfca.edu/~galles/visualization/Algorithms.html 

**B树图示** 

B树是为了磁盘或其它存储设备而设计的一种多叉(下面你会看到，相对于二叉，B树每个内结点有多个 分支，即多叉)平衡查找树。 多叉平衡 

![image-20191112224137880](/Users/lee/Library/Application Support/typora-user-images/image-20191112224137880.png)



- B树的高度一般都是在2-4这个高度，树的高度直接影响IO读写的次数。 

- 如果是三层树结构---支撑的数据可以达到20G，如果是四层树结构---支撑的数据可以达到几十T 

B和B+的区别 

B树和B+树的最大区别在于**非叶子节点是否存储数据**的问题。 

B树是非叶子节点和叶子节点都会存储数据。 

B+树只有叶子节点才会存储数据，而且存储的数据都是在一行上，而且这些数据都是有指针指向 的，也就是有顺序的。 

#### **非聚集索引(MyISAM)** 

**主键索引** 

![image-20191112224342941](/Users/lee/Library/Application Support/typora-user-images/image-20191112224342941.png)

![image-20191112224725617](/Users/lee/Library/Application Support/typora-user-images/image-20191112224725617.png)

**辅助索引(次要索引)** 

![image-20191112224742171](/Users/lee/Library/Application Support/typora-user-images/image-20191112224742171.png)

#### 聚集索引(InnoDB)

**主键索引** 

![image-20191112224809875](/Users/lee/Library/Application Support/typora-user-images/image-20191112224809875.png)

主键:
1、建主键 

2、没建主键

找唯一字段 当主键 

自动生成伪列 当主键 

主键创建 

自增整数

 不要用大字符串比如 uuid ---- 》 雪花算法 snowflakes 

**辅助索引(次要索引)** 

![image-20191112224851713](/Users/lee/Library/Application Support/typora-user-images/image-20191112224851713.png)

