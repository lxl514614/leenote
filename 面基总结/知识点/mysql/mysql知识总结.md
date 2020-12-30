## 概念

### DDL 数据定义语言 (Data Definition Language)

#### 关键词: `create`, `alter`,`drop`,`truncate`

```sql
例:
1. 创建数据库
	create database school;
2. 删除数据库
	drop database school;
3. 切换数据库
	use school;
4. 创建表
	create table student(
　　　id int(4) primary key auto_increment,
　　　name varchar(20),
     score int(3)
	);
5. 修改表
	alter table student rename (to) teacher;
　alter table student add password varchar(20);
　alter table student change password pwd varchar(20);
　alter table student modify pwd int;
  alter table student drop pwd;
...
```

#### DDL演化方式

1. copy table

   1. 创建临时表
   2. copy数据到临时表
   3. rename进行交换

   缺点:

   阻塞事务

   占用磁盘空间

2. inplace

   1. 在线更改表,不会拷贝临时表

   缺点

   阻塞事务

3. online_ddl

   1. 在线更改表
   2. 不会拷贝临时表

   不阻塞事务

#### MySql 5.7中的online ddl:(algorithm=inplace)

ALGORITHM=INPLACE，可以避免重建表带来的IO和CPU消耗，保证ddl期间依然有良好的性能和并发。 

ALGORITHM=COPY，需要拷贝原始表，所以不允许并发DML写操作，可读。这种copy方式的效率还是不如 inplace ，因为前者需要记录undo和redo log，而且因为临时占用buffer pool引起短时间内性能受影响。

①：In-Place为Yes是优选项，说明该操作支持INPLACE

②：Copies Table为No是优选项，因为为Yes需要重建表。大部分情况与In-Place是相反的

③：Allows Concurrent DML?为Yes是优选项，说明ddl期间表依然可读写，可以指定 LOCK=NONE（如果操作允许的话mysql自动就是NONE）

④：Allows Concurrent Query?默认所有DDL操作期间都允许查询请求，放在这只是便于参考

#### 在线DDL的限制

```
1. 在alter table时，如果涉及到table copy操作，要确保datadir目录有足够的磁盘空间，能够放的下整张表，因为拷贝表的的操作是直接在数据目录下进行的。
2. 添加索引无需table copy，但要确保tmpdir目录足够存下索引一列的数据（如果是组合索引，当前临时排序文件一合并到原表上就会删除）            
3. 在主从环境下，主库执行alter命令在完成之前是不会进入binlog记录事件，如果允许dml操作则不影响记录时间，所以期间不会导致延迟。然而，由于从库是单个SQL Thread按顺序应用relay log，轮到ALTER语句时直到执行完才能下一条，所以从库会在master ddl完成后开始产生延迟。（pt-osc可以控制延迟时间，所以这种场景下它更合适）
4. During each online DDL ALTER TABLE statement, regardless of the LOCK clause, there are brief periods at the beginning and end requiring an exclusive lock on the table (the same kind of lock specified by the LOCK=EXCLUSIVE clause). Thus, an online DDL operation might wait before starting if there is a long-running transaction performing inserts, updates, deletes, or SELECT … FOR UPDATE on that table; and an online DDL operation might wait before finishing if a similar long-running transaction was started while the ALTER TABLE was in progress.
5. 在执行一个允许并发DML在线 ALTER TABLE时，结束之前这个线程会应用 online log 记录的增量修改，而这些修改是其它thread里产生的，所以有可能会遇到重复键值错误(ERROR 1062 (23000): Duplicate entry)。
6. 涉及到table copy时，目前还没有机制限制暂停ddl，或者限制IO阀值 
7. 在MySQL 5.7.6开始能够通过 performance_schema 观察alter table的进度.一般来说，建议把多个alter语句合并在一起进行，避免多次table rebuild带来的消耗。但是也要注意分组，比如需要copy table和只需inplace就能完成的，应该分两个alter语句。
8. 如果DDL执行时间很长，期间又产生了大量的dml操作，以至于超过了innodb_online_alter_log_max_size变量所指定的大小，会引起DB_ONLINE_LOG_TOO_BIG 错误。默认为 128M，特别对于需要拷贝大表的alter操作，考虑临时加大该值，以此获得更大的日志缓存空间
9. 执行完 ALTER TABLE 之后，最好 ANALYZE TABLE tb1 去更新索引统计信息 
```

#### ONLINE DDL实现过程

```
online ddl主要包括3个阶段，prepare阶段，ddl执行阶段，commit阶段，rebuild方式比no-rebuild方式实质多了一个ddl执行阶段，prepare阶段和commit阶段类似。下面将主要介绍ddl执行过程中三个阶段的流程。
1. Prepare阶段
1.1 创建新的frm文件(与InnoDB无关)
1.2 持有EXCLUSIVE-MDL锁,禁止读写
1.3 根据alter类型,确定执行方式*copy, online-rebild, online-norebuild)
	  假如是Add Index，则选择online-norebuild即INPLACE方式  
1.4 更新数据字段的内存对象
1.5 分配row_log对象记录增量(仅rebuild类型需要)
1.6 生成新的临时ibd文件(仅rebuild类型需要)
2. ddl执行阶段
2.1 降级EXCLUSIVE-MDL锁,允许读写
2.2 扫描old_table的剧集索引每一条记录
2.3 遍历新表的聚集索引和耳机索引,逐一处理
2.4 根据rec构造对应的索引项
2.5 将构造索引项插入sort_buffer块排序
2.6 将sort_buffer块更新到新的索引上
2.7 记录ddl执行过程产生的增量(仅rebuild类型需要)
2.8 重放row_log中的操作到新索引上(no-rebuild数据说实在原表上更新的)
2.9 重放row_log间产生dml操作append到row_log最后一个Block
3. commit阶段
3.1 当前Block为row_log最后一个时,禁止读写,升级到EXCLUSIVE-MDL锁
3.2 重做row_log中最后一部分增量
3.3 更新innodb的数据字典表
3.4 提交事务(刷事务的redo日志)
3.5 修改统计信息
3.6 rename临时idb文件,frm文件
3.7 更新完成
```

#### MySQL 5.6 后版本常见的DLL操作

```
添加/删除索引： 采用Online no-rebuild方式；
修改列名/default值/自增列值：由于只需要修改元数据，因此操作可以很快完成；
添加/删除列/交换列顺序/修改列NULL-NOTNULL属性/修改ROW-FORMAT/添加修改主键： 采用online rebuild方式，由于需要记录格式改变，需要重建表；
修改列类型/Optimize table/转换字符集：采用Offline Copy方式，需要锁表，不允许其他会话读写。
```

#### 将OnLine DDL改为非联机模式

```sqlite
ALTER TABLE table_name XXX ,ALGORITHM=COPY;
```

下表根据官方 [Summary of Online Status for DDL Operations](https://dev.mysql.com/doc/refman/5.6/en/innodb-create-index-overview.html) 整理挑选的常用操作。

- *In-Place*为Yes是优选项，说明该操作支持INPLACE
- *Copies Table*为No是优选项，因为为Yes需要重建表。大部分情况与In-Place是相反的
- *Allows Concurrent DML?*为Yes是优选项，说明ddl期间表依然可读写，可以指定 LOCK=NONE（如果操作允许的话mysql自动就是NONE）
- *Allows Concurrent Query?*默认所有DDL操作期间都允许查询请求，放在这只是便于参考
- *Notes*会对前面几列Yes/No带`*`号的限制说明

- *Notes*会对前面几列Yes/No带`*`号的限制说明

| Operation                       | In-Place? | Copies Table? | Allows Concurrent DML? | Allows Concurrent Query? | Notes                                                        |
| ------------------------------- | --------- | ------------- | ---------------------- | ------------------------ | ------------------------------------------------------------ |
| 添加索引                        | Yes*      | No*           | Yes                    | Yes                      | 对全文索引的一些限制                                         |
| 删除索引                        | Yes       | No            | Yes                    | Yes                      | 仅修改表的元数据                                             |
| OPTIMIZE TABLE                  | Yes       | Yes           | Yes                    | Yes                      | 从 5.6.17开始使用ALGORITHM=INPLACE，当然如果指定了`old_alter_table=1`或mysqld启动带`--skip-new`则将还是COPY模式。如果表上有全文索引只支持COPY |
| 对一列设置默认值                | Yes       | No            | Yes                    | Yes                      | 仅修改表的元数据                                             |
| 对一列修改auto-increment 的值   | Yes       | No            | Yes                    | Yes                      | 仅修改表的元数据                                             |
| 添加 foreign key constraint     | Yes*      | No*           | Yes                    | Yes                      | 为了避免拷贝表，在约束创建时会禁用foreign_key_checks         |
| 删除 foreign key constraint     | Yes       | No            | Yes                    | Yes                      | foreign_key_checks 不影响                                    |
| 改变列名                        | Yes*      | No*           | Yes*                   | Yes                      | 为了允许DML并发, 如果保持相同数据类型，仅改变列名            |
| 添加列                          | Yes*      | Yes*          | Yes*                   | Yes                      | 尽管允许 ALGORITHM=INPLACE ，但数据大幅重组，所以它仍然是一项昂贵的操作。当添加列是auto-increment，不允许DML并发 |
| 删除列                          | Yes       | Yes*          | Yes                    | Yes                      | 尽管允许 ALGORITHM=INPLACE ，但数据大幅重组，所以它仍然是一项昂贵的操作 |
| 修改列数据类型                  | No        | Yes*          | No                     | Yes                      | 修改类型或添加长度，都会拷贝表，而且不允许更新操作           |
| 更改列顺序                      | Yes       | Yes           | Yes                    | Yes                      | 尽管允许 ALGORITHM=INPLACE ，但数据大幅重组，所以它仍然是一项昂贵的操作 |
| 修改ROW_FORMAT 和KEY_BLOCK_SIZE | Yes       | Yes           | Yes                    | Yes                      | 尽管允许 ALGORITHM=INPLACE ，但数据大幅重组，所以它仍然是一项昂贵的操作 |
| 设置列属性NULL 或NOT NULL       | Yes       | Yes           | Yes                    | Yes                      | 尽管允许 ALGORITHM=INPLACE ，但数据大幅重组，所以它仍然是一项昂贵的操作 |
| 添加主键                        | Yes*      | Yes           | Yes                    | Yes                      | 尽管允许 ALGORITHM=INPLACE ，但数据大幅重组，所以它仍然是一项昂贵的操作。 如果列定义必须转化NOT NULL，则不允许INPLACE |
| 删除并添加主键                  | Yes       | Yes           | Yes                    | Yes                      | 在同一个 ALTER TABLE 语句删除就主键、添加新主键时，才允许inplace；数据大幅重组,所以它仍然是一项昂贵的操作。 |
| 删除主键                        | No        | Yes           | No                     | Yes                      | 不允许并发DML，要拷贝表，而且如果没有在同一 ATLER TABLE 语句里同时添加主键则会收到限制 |
| 变更表字符集                    | No        | Yes           | No                     | Yes                      | 如果新的字符集编码不同，重建表                               |

从表看出，In-Place为No，DML一定是No，说明`ALGORITHM=COPY`一定会发生拷贝表，只读。但`ALGORITHM=INPLACEE`也要可能发生拷贝表，但可以并发DML:

- 添加、删除列，改变列顺序
- 添加或删除主键
- 改变行格式ROW_FORMAT和压缩块大小KEY_BLOCK_SIZE
- 改变列NULL或NOT NULL
- 优化表OPTIMIZE TABLE
- 强制 rebuild 该表

### DML数据操纵语言DML(Data Manipulation Language)　

关键词:`insert`,`update`,`delete`

```sql
例:
1. 插入:
	insert into student values(1,'tonbby',99); (插入所有的字段)
	insert into student(id,name) values(01,'tonbby'); (插入指定的字段)
2. 更新
	update student set name = 'tonbby',score = '99' where id = 1;
3. 删除
	delete from tonbby where id = 1;
```

### DQL数据查询语言DQL(Data Query Language)

```sql
select ... from student where 条件 group by 分组字段 having 条件 order by 排序字段
执行顺序：from->where->group by->having->order by->select
```

### DCL数据控制语言DCL(Data Control Language) 

用户，权限，事务。

## MVCC

1. InnoDB支持`MVCC`，与之`MVCC`相对的是`LBCC`
2. MVCC中`读操作`分两类：`Snapshot Read`(`不加锁`)和`Current Read`（`加锁`）
3. MVCC的好处：**`Snapshot Read不加锁`**，`并发性能好`，适用于常规的`JavaWeb`项目（`OLTP`应用）

## 隔离级别

InnoDB支持4种事务隔离级别（`Isolation Level`）

| 隔离级别                 | 描述                                                         |
| :----------------------- | :----------------------------------------------------------- |
| `READ UNCOMMITTED (RUC)` | 可以读取到其他事务中`尚未提交`的内容，生产环境中不会使用     |
| `READ COMMITTED (RC)`    | 可以读取到其他事务中`已经提交`的内容，`Current Read会加锁`，`存在幻读现象`，`Oracle`和`SQL Server`的默认事务隔离级别为`RC` |
| `REPEATABLE READ (RR)`   | 保证事务的`隔离性`，`Current Read会加锁`，同时会加`Gap Lock`，`不存在幻读现象`，`InnoDB`的默认事务隔离级别为`RR` |
| `SERIALIZABLE`           | MVCC退化为`LBCC`，不区分`Snapshot Read`和`Current Read`，`读`操作加`S Lock`，`写`操作加`X Lock`，读写冲突，并发性能差 |

### 查看数据库隔离级别

```sql 
select @@global.tx_isolation;
mysql 8.0
select @@transaction_isolation;
```

### 设置隔离级别

```sql
SET SESSION TX_ISOLATION='READ-COMMITTED';
设置会话隔离级别
set session transaction isolation level REPEATABLE READ;
```

## 锁

### 行锁Record Locks

锁定一个记录上的索引，而不是记录本身。

如果表没有设置索引，InnoDB 会自动在主键上创建隐藏的聚簇索引，因此 Record Locks 依然可以使用。

1. InnoDB实现了两种标准的`行锁`（`Row-Level Lock`）：共享锁（`Shared(S) Lock`）、排它锁（`Exclusive(X) Lock`）
2. `S Lock`：允许事务持有该锁去*`读取一行数据`*
3. `X Lock`：允许事务持有该锁去*`更新或删除一行数据`*

#### `S Lock`与`X Lock`的兼容性

|      | S    | X    |
| :--- | :--- | :--- |
| S    | Y    | N    |
| X    | N    | N    |

### 锁的算法

#### Record Lock

1. `Record Lock`即行锁，用于锁住`Index Record`（索引记录），分为`S Lock`和`X Lock`
2. 如果表中没有`显式定义的主键`或`非NULL唯一的索引`，InnoDB将自动创建`6 Bytes的ROWID`的隐藏主键

#### Gap Lock

1. 用于锁住`Index Record`之间的间隙

   例如当一个事务执行以下语句，其它事务就不能在 t.c 中插入 15。

   SELECT c FROM t WHERE c BETWEEN 10 and 20 FOR UPDATE;

   如果真的要去掉gap lock，可以考虑改用RC隔离级别+binlog_format=row

2. 如果是`通过唯一索引来搜索一行记录`的时候，不需要使用`Gap Lock`，此时`Next-Key`降级为`Record Lock`

3. `Gap S-Lock`与`Gap X-Lock`是兼容的

4. `Gap Lock`只能_**`阻止其他事务在该Gap中插入记录`**_，但**`无法阻止`**其他事务获取`同一个Gap`上的`Gap Lock`

5. 禁用`Gap Lock`的两种方式

   - 将事务隔离级别设置为`READ COMMITTED`
   - 将变量`innodb_locks_unsafe_for_binlog`（已弃用）设置为`1`

#### Next-Key Lock

1. **`Next-Key Lock` = `Record Lock` + `Gap Lock`**, Next-Key Locks 是 MySQL 的 InnoDB 存储引擎的一种锁实现。
2. 若索引a为10、11、13、20，可锁定的区间为 `(-∞, 10]`、`(10, 11]`、`(11, 13]`、`(13, 20]`、`(20, +∞)`
   - 若执行`Select...Where a=13 For Update`，将在`a=13`上有1个`X Lock`和在`(11, 13)`有1个`Gap Lock`
   - `a=13`的下一个键为`a=20`，将在`a=20`有1个`X Lock`，在`(13, 20)`有1个`Gap Lock`
   - 因此，在`a=13`上有1个`X Lock`，在`(11, 20]`上的有1个`Gap Lock`
   - 也可以分解为在`a=13`和`a=20`上有2个`X Lock`，在`(11,13)`和`(13,20)`上有2个`Gap Lock`
3. 在InnoDB默认事务隔离级别`REPEATABLE READ(RR)`下，支持`Next-Key Lock`
4. MVCC 不能解决幻读的问题，Next-Key Locks 就是为了解决这个问题而存在的。在可重复读（REPEATABLE READ）隔离级别下，使用 MVCC + Next-Key Locks 可以解决幻读问题。
5. 当查询的索引含有唯一属性的时候，Next-Key Lock 会进行优化，将其降级为Record Lock，即仅锁住索引本身，不是范围。

```
我总结的加锁规则里面，包含了两个“原则”、两个“优化”和一个“bug”。

原则 1：加锁的基本单位是 next-key lock。希望你还记得，next-key lock 是前开后闭区间。

原则 2：查找过程中访问到的对象才会加锁。

优化 1：索引上的等值查询，给唯一索引加锁的时候，next-key lock 退化为行锁。

优化 2：索引上的等值查询，向右遍历时且最后一个值不满足等值条件的时候，next-key lock 退化为间隙锁。一个 bug：唯一索引上的范围查询会访问到不满足条件的第一个值为止。
```



参考:http://zhongmingmao.me/2017/05/18/innodb-next-key-lock/

## LOG

### binlog

MySQL 的二进制日志 binlog 可以说是 MySQL 最重要的日志，它记录了所有的 `DDL` 和 `DML` 语句（除了数据查询语句select、show等），**以事件形式记录**，还包含语句所执行的消耗的时间，MySQL的二进制日志是事务安全型的。binlog 的主要目的是**复制和恢复**。

binlog详解:http://www.jiangxinlingdu.com/mysql/2019/06/07/binlog.html

#### Binlog日志的两个最重要的使用场景

- **MySQL主从复制**：MySQL Replication在Master端开启binlog，Master把它的二进制日志传递给slaves来达到master-slave数据一致的目的
- **数据恢复**：通过使用 mysqlbinlog工具来使恢复数据

#### 启用 Binlog

一般来说开启binlog日志大概会有1%的性能损耗。

启用binlog，通过配置 `/etc/my.cnf` 或 `/etc/mysql/mysql.conf.d/mysqld.cnf` 配置文件的 `log-bin` 选项：

在配置文件中加入 `log-bin` 配置，表示启用binlog，如果没有给定值，写成 `log-bin=`，则默认名称为主机名。（注：名称若带有小数点，则只取第一个小数点前的部分作为名称）

```sql
[mysqld]
log-bin=my-binlog-name
```

也可以通过 `SET SQL_LOG_BIN=1` 命令来启用 binlog，通过 `SET SQL_LOG_BIN=0` 命令停用 binlog。启用 binlog 之后须重启MySQL才能生效。

#### 常用的Binlog操作命令

```sql
# 是否启用binlog日志
show variables like 'log_bin';

# 查看详细的日志配置信息
show global variables like '%log%';

# mysql数据存储目录
show variables like '%dir%';

# 查看binlog的目录
show global variables like "%log_bin%";

# 查看当前服务器使用的biglog文件及大小
show binary logs;

# 查看主服务器使用的biglog文件及大小

# 查看最新一个binlog日志文件名称和Position
show master status;


# 事件查询命令
# IN 'log_name' ：指定要查询的binlog文件名(不指定就是第一个binlog文件)
# FROM pos ：指定从哪个pos起始点开始查起(不指定就是从整个文件首个pos点开始算)
# LIMIT [offset,] ：偏移量(不指定就是0)
# row_count ：查询总条数(不指定就是所有行)
show binlog events [IN 'log_name'] [FROM pos] [LIMIT [offset,] row_count];

# 查看 binlog 内容
show binlog events;

# 查看具体一个binlog文件的内容 （in 后面为binlog的文件名）
show binlog events in 'master.000003';

# 设置binlog文件保存事件，过期删除，单位天
set global expire_log_days=3; 

# 删除当前的binlog文件
reset master; 

# 删除slave的中继日志
reset slave;

# 删除指定日期前的日志索引中binlog日志文件
purge master logs before '2019-03-09 14:00:00';

# 删除指定日志文件
purge master logs to 'master.000003';
```

#### 写 Binlog 的时机

对支持事务的引擎如InnoDB而言，必须要提交了事务才会记录binlog。binlog 什么时候**刷新到磁盘**跟参数 `sync_binlog` 相关。

- 如果设置为0，则表示MySQL不控制binlog的刷新，由文件系统去控制它缓存的刷新；
- 如果设置为不为0的值，则表示每 `sync_binlog` 次事务，MySQL调用文件系统的刷新操作刷新binlog到磁盘中。
- 设为1是最安全的，在系统故障时最多丢失一个事务的更新，但是会对性能有所影响。

如果 `sync_binlog=0` 或 `sync_binlog大于1`，当发生电源故障或操作系统崩溃时，可能有一部分已提交但其binlog未被同步到磁盘的事务会被丢失，恢复程序将无法恢复这部分事务。

在MySQL 5.7.7之前，默认值 sync_binlog 是0，MySQL 5.7.7和更高版本使用默认值1，这是最安全的选择。一般情况下会设置为100或者0，牺牲一定的一致性来获取更好的性能。

#### Binlog 文件以及扩展

binlog日志包括两类文件:

- 二进制日志索引文件（文件名后缀为.index）用于记录所有有效的的二进制文件
- 二进制日志文件（文件名后缀为.00000*）记录数据库所有的DDL和DML语句事件

binlog是一个二进制文件集合，每个binlog文件以一个4字节的魔数开头，接着是一组Events:

- 魔数：0xfe62696e对应的是0xfebin；
- Event：每个Event包含header和data两个部分；header提供了Event的创建时间，哪个服务器等信息，data部分提供的是针对该Event的具体信息，如具体数据的修改；
- 第一个Event用于描述binlog文件的格式版本，这个格式就是event写入binlog文件的格式；
- 其余的Event按照第一个Event的格式版本写入；
- 最后一个Event用于说明下一个binlog文件；
- binlog的索引文件是一个文本文件，其中内容为当前的binlog文件列表

当遇到以下3种情况时，MySQL会重新生成一个新的日志文件，文件序号递增：

- MySQL服务器停止或重启时
- 使用 `flush logs` 命令；
- 当 binlog 文件大小超过 `max_binlog_size` 变量的值时；

> `max_binlog_size` 的最小值是4096字节，最大值和默认值是 1GB (1073741824字节)。事务被写入到binlog的一个块中，所以它不会在几个二进制日志之间被拆分。因此，如果你有很大的事务，为了保证事务的完整性，不可能做切换日志的动作，只能将该事务的日志都记录到当前日志文件中，直到事务结束，你可能会看到binlog文件大于 max_binlog_size 的情况。

#### Binlog 的日志格式

记录在二进制日志中的事件的格式取决于二进制记录格式。支持三种格式类型：

- STATEMENT：基于SQL语句的复制（statement-based replication, SBR）
- ROW：基于行的复制（row-based replication, RBR）
- MIXED：混合模式复制（mixed-based replication, MBR）

在 `MySQL 5.7.7` 之前，默认的格式是 `STATEMENT`，在 `MySQL 5.7.7` 及更高版本中，默认值是 `ROW`。日志格式通过 `binlog-format` 指定，如 `binlog-format=STATEMENT`、`binlog-format=ROW`、`binlog-format=MIXED`。

##### Statement

每一条会修改数据的sql都会记录在binlog中

优点：不需要记录每一行的变化，减少了binlog日志量，节约了IO, 提高了性能。

缺点：由于记录的只是执行语句，为了这些语句能在slave上正确运行，因此还必须记录每条语句在执行的时候的一些相关信息，以保证所有语句能在slave得到和在master端执行的时候相同的结果。另外mysql的复制，像一些特定函数的功能，slave与master要保持一致会有很多相关问题。

##### Row

5.1.5版本的MySQL才开始支持 `row level` 的复制,它不记录sql语句上下文相关信息，仅保存哪条记录被修改。

优点： binlog中可以不记录执行的sql语句的上下文相关的信息，仅需要记录那一条记录被修改成什么了。所以row的日志内容会非常清楚的记录下每一行数据修改的细节。而且不会出现某些特定情况下的存储过程，或function，以及trigger的调用和触发无法被正确复制的问题.

缺点:所有的执行的语句当记录到日志中的时候，都将以每行记录的修改来记录，这样可能会产生大量的日志内容。

> 注：将二进制日志格式设置为ROW时，有些更改仍然使用基于语句的格式，包括所有DDL语句，例如CREATE TABLE， ALTER TABLE，或 DROP TABLE。

##### Mixed

从5.1.8版本开始，MySQL提供了Mixed格式，实际上就是Statement与Row的结合。
在Mixed模式下，一般的语句修改使用statment格式保存binlog，如一些函数，statement无法完成主从复制的操作，则采用row格式保存binlog，MySQL会根据执行的每一条具体的sql语句来区分对待记录的日志形式，也就是在Statement和Row之间选择一种。

#### mysqlbinlog 命令的使用

服务器以二进制格式将binlog日志写入binlog文件，如何要以文本格式显示其内容，可以使用 mysqlbinlog 命令。

#### Binlog 事件类型

binlog 事件的结构主要有3个版本：

- v1: 在 MySQL 3.23 中使用
- v3: 在 MySQL 4.0.2 到 4.1 中使用
- v4: 在 MySQL 5.0 及以上版本中使用

现在一般不会使用MySQL5.0以下版本，所以下面仅介绍v4版本的binlog事件类型。binlog 的事件类型较多，本文在此做一些简单的汇总

| 事件类型                 | 说明                                                         |
| :----------------------- | :----------------------------------------------------------- |
| UNKNOWN_EVENT            | 此事件从不会被触发，也不会被写入binlog中；发生在当读取binlog时，不能被识别其他任何事件，那被视为UNKNOWN_EVENT |
| START_EVENT_V3           | 每个binlog文件开始的时候写入的事件，此事件被用在MySQL3.23 – 4.1，MYSQL5.0以后已经被 FORMAT_DESCRIPTION_EVENT 取代 |
| QUERY_EVENT              | 执行更新语句时会生成此事件，包括：create，insert，update，delete； |
| STOP_EVENT               | 当mysqld停止时生成此事件                                     |
| ROTATE_EVENT             | 当mysqld切换到新的binlog文件生成此事件，切换到新的binlog文件可以通过执行flush logs命令或者binlog文件大于 `max_binlog_size` 参数配置的大小； |
| INTVAR_EVENT             | 当sql语句中使用了AUTO_INCREMENT的字段或者LAST_INSERT_ID()函数；此事件没有被用在binlog_format为ROW模式的情况下 |
| LOAD_EVENT               | 执行LOAD DATA INFILE 语句时产生此事件，在MySQL 3.23版本中使用 |
| SLAVE_EVENT              | 未使用                                                       |
| CREATE_FILE_EVENT        | 执行LOAD DATA INFILE 语句时产生此事件，在MySQL4.0和4.1版本中使用 |
| APPEND_BLOCK_EVENT       | 执行LOAD DATA INFILE 语句时产生此事件，在MySQL4.0版本中使用  |
| EXEC_LOAD_EVENT          | 执行LOAD DATA INFILE 语句时产生此事件，在MySQL4.0和4.1版本中使用 |
| DELETE_FILE_EVENT        | 执行LOAD DATA INFILE 语句时产生此事件，在MySQL4.0版本中使用  |
| NEW_LOAD_EVENT           | 执行LOAD DATA INFILE 语句时产生此事件，在MySQL4.0和4.1版本中使用 |
| RAND_EVENT               | 执行包含RAND()函数的语句产生此事件，此事件没有被用在binlog_format为ROW模式的情况下 |
| USER_VAR_EVENT           | 执行包含了用户变量的语句产生此事件，此事件没有被用在binlog_format为ROW模式的情况下 |
| FORMAT_DESCRIPTION_EVENT | 描述事件，被写在每个binlog文件的开始位置，用在MySQL5.0以后的版本中，代替了START_EVENT_V3 |
| XID_EVENT                | 支持XA的存储引擎才有，本地测试的数据库存储引擎是innodb，所有上面出现了XID_EVENT；innodb事务提交产生了QUERY_EVENT的BEGIN声明，QUERY_EVENT以及COMMIT声明，如果是myIsam存储引擎也会有BEGIN和COMMIT声明，只是COMMIT类型不是XID_EVENT |
| BEGIN_LOAD_QUERY_EVENT   | 执行LOAD DATA INFILE 语句时产生此事件，在MySQL5.0版本中使用  |
| EXECUTE_LOAD_QUERY_EVENT | 执行LOAD DATA INFILE 语句时产生此事件，在MySQL5.0版本中使用  |
| TABLE_MAP_EVENT          | 用在binlog_format为ROW模式下，将表的定义映射到一个数字，在行操作事件之前记录（包括：WRITE_ROWS_EVENT，UPDATE_ROWS_EVENT，DELETE_ROWS_EVENT） |
| PRE_GA_WRITE_ROWS_EVENT  | 已过期，被 WRITE_ROWS_EVENT 代替                             |
| PRE_GA_UPDATE_ROWS_EVENT | 已过期，被 UPDATE_ROWS_EVENT 代替                            |
| PRE_GA_DELETE_ROWS_EVENT | 已过期，被 DELETE_ROWS_EVENT 代替                            |
| WRITE_ROWS_EVENT         | 用在binlog_format为ROW模式下，对应 insert 操作               |
| UPDATE_ROWS_EVENT        | 用在binlog_format为ROW模式下，对应 update 操作               |
| DELETE_ROWS_EVENT        | 用在binlog_format为ROW模式下，对应 delete 操作               |
| INCIDENT_EVENT           | 主服务器发生了不正常的事件，通知从服务器并告知可能会导致数据处于不一致的状态 |
| HEARTBEAT_LOG_EVENT      | 主服务器告诉从服务器，主服务器还活着，不写入到日志文件中     |

#### Binlog 事件的结构

一个事件对象分为事件头和事件体，事件的结构如下：

```
+=====================================+
| event  | timestamp         0 : 4    |
| header +----------------------------+
|        | type_code         4 : 1    |
|        +----------------------------+
|        | server_id         5 : 4    |
|        +----------------------------+
|        | event_length      9 : 4    |
|        +----------------------------+
|        | next_position    13 : 4    |
|        +----------------------------+
|        | flags            17 : 2    |
|        +----------------------------+
|        | extra_headers    19 : x-19 |
+=====================================+
| event  | fixed part        x : y    |
| data   +----------------------------+
|        | variable part              |
+=====================================+
```

如果事件头的长度是 `x` 字节，那么事件体的长度为 `(event_length - x)` 字节；设事件体中 `fixed part` 的长度为 `y` 字节，那么 `variable part` 的长度为 `(event_length - (x + y))` 字节

#### binlog 的写入机制

binlog 的写入逻辑比较简单：事务执行过程中，先把日志写到 binlog cache，事务提交的时候，再把 binlog cache 写到 binlog 文件中。

一个事务的 binlog 是不能被拆开的，因此不论这个事务多大，也要确保一次性写入。这就涉及到了 binlog cache 的保存问题。

系统给 binlog cache 分配了一片内存，每个线程一个，参数 binlog_cache_size 用于控制单个线程内 binlog cache 所占内存的大小。如果超过了这个参数规定的大小，就要暂存到磁盘。

事务提交的时候，执行器把 binlog cache 里的完整事务写入到 binlog 中，并清空 binlog cache。

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20200616102713321.png" alt="image-20200616102713321" style="zoom:50%;" />

可以看到，每个线程有自己 binlog cache，但是共用同一份 binlog 文件。

- 图中的 write，指的就是指把日志写入到文件系统的 page cache，并没有把数据持久化到磁盘，所以速度比较快。

- 图中的 fsync，才是将数据持久化到磁盘的操作。一般情况下，我们认为 fsync 才占磁盘的 IOPS。

write 和 fsync 的时机，是由参数 sync_binlog 控制的：

- sync_binlog=0 的时候，表示每次提交事务都只 write，不 fsync；
- sync_binlog=1 的时候，表示每次提交事务都会执行 fsync；
- sync_binlog=N(N>1) 的时候，表示每次提交事务都 write，但累积 N 个事务后才 fsync。

因此，在出现 IO 瓶颈的场景里，将 sync_binlog 设置成一个比较大的值，可以提升性能。在实际的业务场景中，考虑到丢失日志量的可控性，一般不建议将这个参数设成 0，比较常见的是将其设置为 100~1000 中的某个数值。

但是，将 sync_binlog 设置为 N，对应的风险是：如果主机发生异常重启，会丢失最近 N 个事务的 binlog 日志。

### redo log

#### redo log的写入机制

redo log的三种状态:

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20200616115051876.png" alt="image-20200616115051876" style="zoom:50%;" />

这三种状态分别是：

1. 存在 redo log buffer 中，物理上是在 MySQL 进程内存中，就是图中的红色部分；
2. 写到磁盘 (write)，但是没有持久化（fsync)，物理上是在文件系统的 page cache 里面，也就是图中的黄色部分；
3. 持久化到磁盘，对应的是 hard disk，也就是图中的绿色部分。

日志写到 redo log buffer 是很快的，wirte 到 page cache 也差不多，但是持久化到磁盘的速度就慢多了。

为了控制 redo log 的写入策略，InnoDB 提供了 innodb_flush_log_at_trx_commit 参数，它有三种可能取值：

1. 设置为 0 的时候，表示每次事务提交时都只是把 redo log 留在 redo log buffer 中 ;
2. 设置为 1 的时候，表示每次事务提交时都将 redo log 直接持久化到磁盘；
3. 设置为 2 的时候，表示每次事务提交时都只是把 redo log 写到 page cache。

InnoDB 有一个后台线程，每隔 1 秒，就会把 redo log buffer 中的日志，调用 write 写到文件系统的 page cache，然后调用 fsync 持久化到磁盘。

注意，事务执行中间过程的 redo log 也是直接写在 redo log buffer 中的，这些 redo log 也会被后台线程一起持久化到磁盘。也就是说，一个没有提交的事务的 redo log，也是可能已经持久化到磁盘的。

实际上，除了后台线程每秒一次的轮询操作外，还有两种场景会让一个没有提交的事务的 redo log 写入到磁盘中。

1. **一种是，redo log buffer 占用的空间即将达到 innodb_log_buffer_size 一半的时候，后台线程会主动写盘。**注意，由于这个事务并没有提交，所以这个写盘动作只是 write，而没有调用 fsync，也就是只留在了文件系统的 page cache。
2. **另一种是，并行的事务提交的时候，顺带将这个事务的 redo log buffer 持久化到磁盘。**假设一个事务 A 执行到一半，已经写了一些 redo log 到 buffer 中，这时候有另外一个线程的事务 B 提交，如果 innodb_flush_log_at_trx_commit 设置的是 1，那么按照这个参数的逻辑，事务 B 要把 redo log buffer 里的日志全部持久化到磁盘。这时候，就会带上事务 A 在 redo log buffer 里的日志一起持久化到磁盘。

这里需要说明的是，我们介绍两阶段提交的时候说过，时序上 redo log 先 prepare， 再写 binlog，最后再把 redo log commit。

如果把 innodb_flush_log_at_trx_commit 设置成 1，那么 redo log 在 prepare 阶段就要持久化一次，因为有一个崩溃恢复逻辑是要依赖于 prepare 的 redo log，再加上 binlog 来恢复的。

每秒一次后台轮询刷盘，再加上崩溃恢复这个逻辑，InnoDB 就认为 redo log 在 commit 的时候就不需要 fsync 了，只会 write 到文件系统的 page cache 中就够了。

通常我们说 MySQL 的“双 1”配置，指的就是 sync_binlog 和 innodb_flush_log_at_trx_commit 都设置成 1。也就是说，一个事务完整提交前，需要等待两次刷盘，一次是 redo log（prepare 阶段），一次是 binlog。

这时候，你可能有一个疑问，这意味着我从 MySQL 看到的 TPS 是每秒两万的话，每秒就会写四万次磁盘。但是，我用工具测试出来，磁盘能力也就两万左右，怎么能实现两万的 TPS？

解释这个问题，就要用到组提交（group commit）机制了。

这里，我需要先和你介绍日志逻辑序列号（log sequence number，LSN）的概念。LSN 是单调递增的，用来对应 redo log 的一个个写入点。每次写入长度为 length 的 redo log， LSN 的值就会加上 length。

LSN 也会写到 InnoDB 的数据页中，来确保数据页不会被多次执行重复的 redo log。关于 LSN 和 redo log、checkpoint 的关系，我会在后面的文章中详细展开。

如图所示，是三个并发事务 (trx1, trx2, trx3) 在 prepare 阶段，都写完 redo log buffer，持久化到磁盘的过程，对应的 LSN 分别是 50、120 和 160。

<img src="https://static001.geekbang.org/resource/image/93/cc/933fdc052c6339de2aa3bf3f65b188cc.png" alt="img" style="zoom:50%;" />

从图中可以看到，

1. trx1 是第一个到达的，会被选为这组的 leader；
2. 等 trx1 要开始写盘的时候，这个组里面已经有了三个事务，这时候 LSN 也变成了 160；
3. trx1 去写盘的时候，带的就是 LSN=160，因此等 trx1 返回时，所有 LSN 小于等于 160 的 redo log，都已经被持久化到磁盘；
4. 这时候 trx2 和 trx3 就可以直接返回了。

所以，一次组提交里面，组员越多，节约磁盘 IOPS 的效果越好。但如果只有单线程压测，那就只能老老实实地一个事务对应一次持久化操作了。

在并发更新场景下，第一个事务写完 redo log buffer 以后，接下来这个 fsync 越晚调用，组员可能越多，节约 IOPS 的效果就越好。

为了让一次 fsync 带的组员更多，MySQL 有一个很有趣的优化：拖时间。在介绍两阶段提交的时候，我曾经给你画了一个图，现在我把它截过来。

<img src="https://static001.geekbang.org/resource/image/98/51/98b3b4ff7b36d6d72e38029b86870551.png" alt="img" style="zoom:50%;" />

图中，我把“写 binlog”当成一个动作。但实际上，写 binlog 是分成两步的：

1. 先把 binlog 从 binlog cache 中写到磁盘上的 binlog 文件；调
2. 用 fsync 持久化。

MySQL 为了让组提交的效果更好，把 redo log 做 fsync 的时间拖到了步骤 1 之后。也就是说，上面的图变成了这样：

<img src="https://static001.geekbang.org/resource/image/5a/28/5ae7d074c34bc5bd55c82781de670c28.png" alt="img" style="zoom:50%;" />

这么一来，binlog 也可以组提交了。在执行图 5 中第 4 步把 binlog fsync 到磁盘时，如果有多个事务的 binlog 已经写完了，也是一起持久化的，这样也可以减少 IOPS 的消耗。不过通常情况下第 3 步执行得会很快，所以 binlog 的 write 和 fsync 间的间隔时间短，导致能集合到一起持久化的 binlog 比较少，因此 binlog 的组提交的效果通常不如 redo log 的效果那么好。如果你想提升 binlog 组提交的效果，可以通过设置 binlog_group_commit_sync_delay 和 binlog_group_commit_sync_no_delay_count 来实现。binlog_group_commit_sync_delay 参数，表示延迟多少微秒后才调用 fsync;binlog_group_commit_sync_no_delay_count 参数，表示累积多少次以后才调用 fsync。

#### redo log buffer

#### sort buffer



WAL机制

binlog_format查看:

show variables like 'binlog_format';

今天我们从上一篇文章的课后问题说起，提到了全表扫描的加锁方式。我们发现即使给所有的行都加上行锁，仍然无法解决幻读问题，因此引入了间隙锁的概念。

```sql
select city,name,age from t where city='杭州' order by name limit 1000 ;
```



<img src="/Users/lee/Library/Application Support/typora-user-images/image-20200609211427589.png" alt="image-20200609211427589" style="zoom:50%;" />

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20200609211505734.png" alt="image-20200609211505734" style="zoom:50%;" />



索引破坏

对索引字段做函数操作，可能会破坏索引值的有序性，因此优化器就决定放弃走树搜索功能。

第二个例子是隐式类型转换，第三个例子是隐式字符编码转换，它们都跟第一个例子一样，因为要求在索引字段上做函数操作而导致了全索引扫描。



<font color="red">**在删除数据的时候尽量加 limit。这样不仅可以控制删除数据的条数，让操作更安全，还可以减小加锁的范围**。</font>

**锁就是加在索引上的，这是 InnoDB 的一个基础设定，需要你在分析问题的时候要一直记得。**



mysql高可用总结:
1、主备延迟，就是在同一个事务在备库执行完成的时间和主库执行完成的时间之间的差值，包括主库事务执行完成时间和将binlog发送给备库，备库事务的执行完成时间的差值。每个事务的seconds_behind_master延迟时间，每个事务的 binlog 里面都有一个时间字段，用于记录主库上的写入时间，备库取出当前正在执行的事务的时间字段的值，计算它与当前系统时的差值。
2、主备延迟的来源①首先，有些部署条件下，备库所在机器的性能要比主库所在的机器性能差，原因多个备库部署在同一台机器上，大量的查询会导致io资源的竞争，解决办法是配置”双1“，redo log和binlog都只write fs page cache②备库的压力大，产生的原因大量的查询操作在备库操作，耗费了大量的cpu，导致同步延迟，解决办法，使用一主多从，多个从减少备的查询压力③大事务，因为如果一个大的事务的dml操作导致执行时间过长，将其事务binlog发送给备库，备库也需执行那么长时间，导致主备延迟，解决办法尽量减少大事务，比如delete操作，使用limit分批删除，可以防止大事务也可以减少锁的范围。
④大表的ddl，会导致主库将其ddl binlog发送给备库，备库解析中转日志，同步，后续的dml binlog发送过来，需等待ddl的mdl写锁释放，导致主备延迟。
3、可靠性优先策略，①判断备库 B 现在的 seconds_behind_master如果小于某个值（比如 5 秒）继续下一步，否则持续重试这一步，②把主库 A 改成只读状态，即把 readonly 设置为 true，③判断备库 B 的 seconds_behind_master的值，直到这个值变成 0 为止； 把备库 B 改成可读写也就是把 readonly 设置为 false； 把业务请求切换到备库，个人理解如果发送过来的binlog在中转日志中有多个事务，业务不可用的时间，就是多个事务被运用的总时间。如果非正常情况下，主库掉电，会导致出现的问题，如果备库和主库的延迟时间短，在中转日志运用完成，业务才能正常使用，如果在中转日志还未运用完成，切换为备库会导致之前完成的事务，”数据丢失“，但是在一些业务场景下不可接受。
4、可用性策略，出现的问题：在双m，且binlog_format=mixed，会导致主备数据不一致，使用使用 row 格式的 binlog 时，数据不一致的问题更容易发现，因为binlog row会记录字段的所有值。





Online DDL的过程是这样的：

1. 拿MDL写锁
2. 降级成MDL读锁
3. 真正做DDL
4. 升级成MDL写锁
5. 释放MDL锁

1、2、4、5如果没有锁冲突，执行时间非常短。第3步占用了DDL绝大部分时间，这期间这个表可以正常读写数据，是因此称为“online ”





```sql
select * from performance_schema.file_summary_by_event_name where event_name = 'wait/io/file/innodb/innodb_log_file'\G;
*************************** 1. row ***************************
               EVENT_NAME: wait/io/file/innodb/innodb_log_file
               
           -- 第一组 是所有 IO 类型的统计。
               COUNT_STAR: 1177 -- 所有IO总次数
           SUM_TIMER_WAIT: 317204745096
           MIN_TIMER_WAIT: 831744
           AVG_TIMER_WAIT: 269502384
           MAX_TIMER_WAIT: 6518794056
           
           -- 第二组 是读操作的统计
               COUNT_READ: 8
           SUM_TIMER_READ: 506501088
           MIN_TIMER_READ: 831744
           AVG_TIMER_READ: 63312408
           MAX_TIMER_READ: 353021520
 SUM_NUMBER_OF_BYTES_READ: 70656 -- 总共从 redo log 里读了多少个字节。

					-- 第三组 统计的是写操作。
              COUNT_WRITE: 616
          SUM_TIMER_WRITE: 63666227064
          MIN_TIMER_WRITE: 4446000
          AVG_TIMER_WRITE: 103354224
          MAX_TIMER_WRITE: 2735696760
SUM_NUMBER_OF_BYTES_WRITE: 755200

					 -- 第四组 其他类型数据的统计
               COUNT_MISC: 553
           SUM_TIMER_MISC: 253032016944
           MIN_TIMER_MISC: 1291392
           AVG_TIMER_MISC: 457562256
           MAX_TIMER_MISC: 6518794056
```



**MySQL 是“边读边发的”**



#### join是否可以使用

1. 如果可以使用被驱动表的索引，join 语句还是有其优势的；
2. 不能使用被驱动表的索引，只能使用 Block Nested-Loop Join 算法，这样的语句就尽量不要使用；
3. 在使用 join 的时候，应该让小表做驱动表。

#### MYSQL查询算法

##### NLJ

##### BNL

##### BKA

##### BNK

#### 临时表

在实际应用中，临时表一般用于处理比较复杂的计算逻辑。由于临时表是每个线程自己可见的，所以不需要考虑多个线程执行同一个处理逻辑时，临时表的重名问题。在线程退出的时候，临时表也能自动删除，省去了收尾和异常处理的工作。

在 binlog_format='row’的时候，临时表的操作不记录到 binlog 中，也省去了不少麻烦，这也可以成为你选择 binlog_format 时的一个考虑因素。

普通表和临时表区别:

建表语法: 

普通表: reate table …

临时表: create temporary table ...

临时表特点:

1. 建表语法是` create temporary table ...`
2. 一个临时表只能被创建它的 session 访问，对其他线程不可见。所以，图中 session A 创建的临时表 t，对于 session B 就是不可见的。
3. 临时表可以与普通表同名。
4. session A 内有同名的临时表和普通表的时候，show create 语句，以及增删改查语句访问的是临时表。
5. show tables 命令不显示临时表。
6. 临时表只能被创建它的 session 访问，所以在这个 session 结束的时候，会自动删除临时表。



group by 的几种实现算法，从中可以总结一些使用的指导原则：

1. 如果对 group by 语句的结果没有排序要求，要在语句后面加 order by null；
2. 尽量让 group by 过程用上表的索引，确认方法是 explain 结果里没有 Using temporary 和 Using filesort；
3. 如果 group by 需要统计的数据量不大，尽量只使用内存临时表；也可以通过适当调大 tmp_table_size 参数，来避免用到磁盘临时表；
4. 如果数据量实在太大，使用 SQL_BIG_RESULT 这个提示，来告诉优化器直接使用排序算法得到 group by 的结果。

内部临时表，和sort buffer,join buffer一样，都用来存放语句执行过程中的中间数据，辅助语句的执行。
使用用法 using temporary。

使用场景：1 数据一边查询，一边直接得到结果，不需要额外内存。比如：group by 需要计算。

​     2 join_buffer 是无序数组（单字段，可以重复），sort_buffer 有序数组，内部临时表是二维结构

3 用到二维表的特性需要用到内部临时表，比如 distinct ，group by

优化：1 group by 字段加索引

   2 扩大临时表内存大小

   3 如果数据量比较大，直接使用磁盘临时表

   4 如果分组之后，不需要排序，手工 order by null



group by 的顺序

1 创建一个内部临时表，初始化字段 a(分组字段) b(数量)

2 扫描表，一个个的判断，a字段值在临时表不存在，则插入(2,1)，如果存在，比如（2,1），则加1，成为（2,2）

3 分组之后，默认会进行排序，如果不需要顺序，用 order by null。