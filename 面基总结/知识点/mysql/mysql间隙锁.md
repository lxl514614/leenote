# MySQL篇--间隙锁

谈及到锁的使用, 就不得不提一个概念:**隔离级别**

谈到锁之前,我们先要清楚锁的作用, 锁的作用就是用来解决事务在并发执行过程中出现的问题

事务在并发执行过程中会导致的问题如下:

1. 脏读(Drity Read): 当一个事务允许读取另外一个事务修改但未提交的数据时, 就可能发生脏读.
2. 不可重复读(Non-repeatable read): 当一行数据获取两遍得到不同的结果表示发生了`不可重复读`.
3. 幻读(Phantom Read): 当两个完全相同的查询语句执行得到不同的结果集.

为解决以上几个问题,ANSI/ISO SQL定义的标准隔离级别如下:

1. 可序列化(Serializable): 最高的隔离级别
2. 可重复读(Repeatable reads):会产生幻读
3. 读已提交(Read Committed): 会出现"不可重复读"
4. 读未提交(Read Uncommitted): 会产生脏读

## 什么是幻读?

了解间隙锁之前首先要清楚什么是`幻读`

当在正在运行的事务中，两个相同的语句获得不同的值时，就会发生幻像读取，因为其他某个事务已修改了表的行。

如果仅执行SELECT，则不会发生幻像读取。仅当您执行UPDATE或DELETE或SELECT FOR UPDATE时，它们才会发生。InnoDB为只读SELECT提供了REPEATABLE READ，但是尽管您选择了事务隔离级别（仅考虑两个最常见的隔离级别，REPEATABLE READ和READ COMMITTED），但它的行为就像您对所有写查询使用READ COMMITTED一样。

**如何解决幻读? 这就需要到了间隙锁**

## 一. 什么是间隙锁(gap lock)?

```
A place in an InnoDB index data structure where new values could be inserted.
```

mysql官方文档对gap lock的描述: https://dev.mysql.com/doc/refman/8.0/en/innodb-locking.html#innodb-gap-locks 

间隙锁定是对索引记录之间的间隙的锁定，或者是对第一个或最后一个索引记录之前的间隙的锁定。因为有间隙锁,当两次运行相同的查询时，无论对该表进行其他会话修改如何，都将获得相同的结果。这使读取保持一致，从而使服务器之间的复制保持一致。如果两次执行SELECT * FROM id> 1000 FOR UPDATE，则期望两次获得相同的值。为此，InnoDB使用排他锁锁定WHERE子句找到的所有索引记录，并使用共享的间隙锁锁定它们之间的间隙。

gap就是索引树中插入新记录的空隙。相应的gap lock就是加在gap上的锁，还有一个next-key锁，是记录+记录前面的gap的组合的锁。

### Next-Key锁

Next-Key锁由行锁 + Gap锁组成。

<font color = "red">mysql的Repeatable reads隔离级别下,就是通过gap lock 和next-key lock解决幻读问题. </font>

## 二. gap锁或next-key锁的作用

简单讲就是防止幻读。通过锁阻止特定条件的新记录的插入，因为插入时也要获取gap锁(Insert Intention Locks)。

### 三. 什么时候会取得gap lock或next-key lock?

这和隔离级别有关,只在`REPEATABLE READ`或以上的隔离级别下的特定操作才会取得gap lock或nextkey lock。

*locking reads，UPDATE和DELETE时，除了对唯一索引的唯一搜索外都会获取gap锁或next-key锁。即锁住其扫描的范围。*

#### innodb是如何加间隙锁的?

1. 当一个涉及到为数据加锁的语句执行时候，会为涉及到的数据加行锁，同时为整个表的数据之间加间隙锁（gap lock）,这个行锁+间隙锁也叫`next-key-lock`。
2. 间隙锁是个左开，右闭的空间。比如一张表有如下数据
3. 间隙锁只会锁插入语句，其他的操作是不互斥的。

#### 加锁原则及优化

1. **原则 1：加锁的基本单位是 next-key lock。希望你还记得，next-key lock 是前开后闭区间。**
2. **原则 2：查找过程中访问到的对象才会加锁。**
3. **优化 1：索引上的等值查询，给唯一索引加锁的时候，next-key lock 退化为行锁。**
4. **优化 2：索引上的等值查询，向右遍历时且最后一个值不满足等值条件的时候，next-key lock 退化为间隙锁。**

下面通过一个例子来描述下加锁过程:

- 表初始化:

```sql
CREATE TABLE z (
  id INT PRIMARY KEY ,
  b  INT,
  KEY b(b)
) ENGINE = InnoDB DEFAULT CHARSET = utf8;

INSERT INTO z (id, b)
VALUES (1, 2),(3, 4),(5, 6),(7, 8),(9, 10);
```

- session A

```sql
begin;
select * from z where b = 6 for update;
```

- session B

```sql
INSERT INTO z VALUES (2, 4);/*success*/
INSERT INTO z VALUES (2, 8);/*blocked*/
INSERT INTO z VALUES (4, 4);/*blocked*/
INSERT INTO z VALUES (4, 8);/*blocked*/
INSERT INTO z VALUES (8, 4);/*blocked*/
INSERT INTO z VALUES (8, 8);/*success*/
INSERT INTO z VALUES (0, 4);/*blocked*/
INSERT INTO z VALUES (-1, 4);/*success*/
```

分别在session B执行上面insert会出现上述状况. 下面就来具体分下其原因.

#### 加锁过程

1. 在索引 b 上的等值查询，给索引 b 加上了 next-key lock (4, 6]；因为b是普通索引,索引向右遍历，会查到b=8才放弃, 加锁原则为查找过程中访问到的对象才会加锁。且最后一个值不满足条件时退化为间隙锁；所以会再加上间隙锁 (6,8)；所以索引 b 上的 next-key lock 的范围是(b=4,id=3)到(b=6,id=5)这个左开右闭区间和(b=6,id=5)到(b=8,id=7)这个开区间
2. for update 会给 b = 6 这一行加上行锁；因此 (b=6,id=5) 这一行上有行锁

- 如图所示，此时索引 b 上的锁：

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20200620170703039.png" alt="image-20200620170703039" style="zoom:50%;" />

- 图中绿色部分即为被锁范围；索引会根据 b 和 id 的值进行排序，插入不同的值，锁的范围是不一样的；分别插入 (b=4,id=2) 和(b=4,id=4)，插入的位置如图所示：

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20200620170722283.png" alt="image-20200620170722283" style="zoom:50%;" />

- 因为索引是有序的，此时，由于记录(b=4,id=3)的存在，(b=4,id=2)不在锁的范围内，可以插入，但(b=4,id=4)在锁的范围内，所以插入时需要等待锁释放，被 blocked

- 对于其他(id,b)的值(2,8),(4,8),(8,4),(8,8)也是同样的道理；因此，得出以下结论：

  - id <= 2 时，b 索引锁范围为 (4,8]
  - 2 < id < 8 时，b 索引锁范围为 [4,8]
  - a >= 8 时，b 索引锁范围为 [4,8)

  <img src="/Users/lee/Library/Application Support/typora-user-images/image-20200620170809486.png" alt="image-20200620170809486" style="zoom:50%;" />