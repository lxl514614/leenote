# mysql知识图谱

## 概念

### 什么是MySQL?

MySQL是最流行的开源SQL数据库管理系统，由Oracle Corporation开发，分发和支持。

- **MySQL是一个数据库管理系统。**

  数据库是数据的结构化集合。从简单的购物清单到图片库，或者企业网络中的大量信息，它都可以是任何东西。要添加，访问和处理存储在计算机数据库中的数据，您需要一个数据库管理系统，例如MySQL Server。由于计算机非常擅长处理大量数据，因此数据库管理系统作为独立实用程序或其他应用程序的一部分，在计算中起着核心作用。

- **MySQL数据库是关系型的。**

  关系数据库将数据存储在单独的表中，而不是将所有数据放在一个大仓库中。数据库结构被组织成针对速度进行了优化的物理文件。具有对象（例如数据库，表，视图，行和列）的逻辑模型提供了灵活的编程环境。您设置规则来管理不同数据字段之间的关系，例如一对一，一对多，唯一，必需或可选，以及 不同表之间的“指针”。数据库执行这些规则，因此，使用设计良好的数据库，您的应用程序将永远不会看到不一致，重复，孤立，孤立，过期或丢失的数据。

  “ MySQL ” 的SQL部分代表 “结构化查询语言”。SQL是用于访问数据库的最常见的标准化语言。根据您的编程环境，您可以直接输入SQL（例如，生成报告），将SQL语句嵌入用另一种语言编写的代码中，或使用隐藏SQL语法的特定于语言的API。

  SQL由ANSI / ISO SQL标准定义。自1986年以来，SQL标准一直在发展，并且存在多个版本。在本手册中，“ SQL-92 ”是指1992年发布的标准，“ SQL：1999 ”是指1999年发布的标准，“ SQL：2003 ”是指标准的当前版本。我们随时使用 “ SQL标准”一词来表示SQL标准的当前版本。

- **MySQL软件是开源的。**

  开源意味着任何人都可以使用和修改该软件。任何人都可以从互联网上下载MySQL软件并使用它而无需支付任何费用。如果愿意，您可以学习源代码并进行更改以适合您的需求。MySQL软件使用GPL（GNU通用公共许可证）http://www.fsf.org/licenses/来定义您在不同情况下可以使用或不可以使用的软件。如果您对GPL不满意，或者需要将MySQL代码嵌入到商业应用程序中，可以从我们这里购买商业许可版本。有关更多信息，请参见MySQL许可概述（http://www.mysql.com/company/legal/licensing/）。

- **MySQL数据库服务器非常快速，可靠，可扩展且易于使用。**

  如果这是您要寻找的，则应尝试一下。MySQL Server可以与其他应用程序，Web服务器等一起轻松地在台式机或笔记本电脑上运行，几乎不需要或根本不需要注意。如果将整台计算机专用于MySQL，则可以调整设置以利用所有可用的内存，CPU能力和I / O容量。MySQL还可以扩展到联网的机器集群。

  MySQL Server最初是为处理大型数据库而开发的，其处理速度比现有解决方案要快得多，并且已经在要求苛刻的生产环境中成功使用了数年。尽管正在不断发展，但MySQL Server如今提供了一组丰富而有用的功能。它的连接性，速度和安全性使MySQL Server非常适合访问Internet上的数据库。

- **MySQL Server可在客户端/服务器或嵌入式系统中使用。**

  MySQL数据库软件是一个客户端/服务器系统，由支持不同后端的多线程SQL Server，几个不同的客户端程序和库，管理工具以及各种应用程序编程接口（API）组成。

  我们还提供MySQL Server作为嵌入式多线程库，您可以将其链接到应用程序中以获得更小，更快，更易于管理的独立产品。

- **可以使用大量的MySQL软件。**

  MySQL Server具有与我们的用户密切合作开发的一组实用功能。您最喜欢的应用程序或语言很可能支持MySQL数据库服务器。

### MySQL主要特点

#### 内部和便携性

- 用C和C ++编写。
- 经过广泛的不同编译器测试。
- 可在许多不同平台上使用。参见 https://www.mysql.com/support/supportedplatforms/database.html。
- 为了便于移植，请使用**CMake进行**配置。
- 已通过Purify（商用内存泄漏检测器）以及GPL工具Valgrind（http://developer.kde.org/~sewardj/）进行了测试。
- 使用具有独立模块的多层服务器设计。
- 设计为使用内核线程完全多线程，以轻松使用多个CPU（如果有）。
- 提供事务性和非事务性存储引擎。
- 使用`MyISAM`索引压缩非常快速的B树磁盘表（）。
- 旨在使其相对容易地添加其他存储引擎。如果要为内部数据库提供SQL接口，这将很有用。
- 使用非常快速的基于线程的内存分配系统。
- 使用优化的嵌套循环联接执行非常快速的联接。
- 实现内存中的哈希表，用作临时表。
- 使用高度优化的类库来实现SQL函数，该类库应尽可能快。通常，查询初始化后根本没有内存分配。
- 提供服务器作为在客户端/服务器网络环境中使用的单独程序，以及作为可嵌入（链接）到独立应用程序中的库。此类应用程序可以隔离使用，也可以在没有网络可用的环境中使用。

#### 资料类型

- 许多数据类型：有符号/无符号整数1，2，3，4，和8个字节长，[`FLOAT`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html)， [`DOUBLE`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html)， [`CHAR`](https://dev.mysql.com/doc/refman/8.0/en/char.html)， [`VARCHAR`](https://dev.mysql.com/doc/refman/8.0/en/char.html)， [`BINARY`](https://dev.mysql.com/doc/refman/8.0/en/binary-varbinary.html)， [`VARBINARY`](https://dev.mysql.com/doc/refman/8.0/en/binary-varbinary.html)， [`TEXT`](https://dev.mysql.com/doc/refman/8.0/en/blob.html)， [`BLOB`](https://dev.mysql.com/doc/refman/8.0/en/blob.html)， [`DATE`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)， [`TIME`](https://dev.mysql.com/doc/refman/8.0/en/time.html)， [`DATETIME`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)， [`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)， [`YEAR`](https://dev.mysql.com/doc/refman/8.0/en/year.html)， [`SET`](https://dev.mysql.com/doc/refman/8.0/en/set.html)， [`ENUM`](https://dev.mysql.com/doc/refman/8.0/en/enum.html)，和开放GIS空间类型。请参见[第11章，*数据类型*](https://dev.mysql.com/doc/refman/8.0/en/data-types.html)。
- 固定长度和可变长度的字符串类型。

#### 陈述和功能

- 查询[`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html)列表和 `WHERE`子句中的 完全运算符和函数支持 。例如：

  ```sql
  mysql> SELECT CONCAT(first_name, ' ', last_name)
      -> FROM citizen
      -> WHERE income/dependents > 10000 AND age > 30;
  ```

- 完全支持SQL`GROUP BY`和 `ORDER BY`子句。支持基函数（[`COUNT()`](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_count)， [`AVG()`](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_avg)， [`STD()`](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_std)， [`SUM()`](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_sum)， [`MAX()`](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_max)， [`MIN()`](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_min)，和 [`GROUP_CONCAT()`](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_group-concat)）。

- 支持标准SQL和ODBC语法，`LEFT OUTER JOIN`并 `RIGHT OUTER JOIN`同时支持它们。

- 支持标准SQL要求的表和列别名。

- 支持[`DELETE`](https://dev.mysql.com/doc/refman/8.0/en/delete.html)， [`INSERT`](https://dev.mysql.com/doc/refman/8.0/en/insert.html)， [`REPLACE`](https://dev.mysql.com/doc/refman/8.0/en/replace.html)，和 [`UPDATE`](https://dev.mysql.com/doc/refman/8.0/en/update.html)以返回更改（受影响）的行数，或返回通过连接到服务器时设置标志，而不是匹配的行的数量。

- 支持特定于MySQL的[`SHOW`](https://dev.mysql.com/doc/refman/8.0/en/show.html) 语句，该语句检索有关数据库，存储引擎，表和索引的信息。支持 `INFORMATION_SCHEMA`数据库，根据标准SQL实现。

- 一条[`EXPLAIN`](https://dev.mysql.com/doc/refman/8.0/en/explain.html)语句，显示优化器如何解析查询。

- 函数名称与表或列名称的独立性。例如，`ABS`是一个有效的列名。唯一的限制是对于函数调用，函数名称和其后的“ `(`”之间不允许有空格 。请参见 [第9.3节“关键字和保留字”](https://dev.mysql.com/doc/refman/8.0/en/keywords.html)。

- 您可以在同一条语句中引用来自不同数据库的表。

#### 安全

- 特权和密码系统，非常灵活和安全，并且可以进行基于主机的验证。
- 连接服务器时，通过对所有密码通信进行加密来实现密码安全。

#### 可扩展性和限制

- 支持大型数据库。我们将MySQL Server与包含5000万条记录的数据库一起使用。我们也知道使用MySQL Server的用户有200,000个表和大约5,000,000,000行。
- 每个表最多支持64个索引。每个索引可以包含1到16列或部分列。[`InnoDB`](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html)表的最大索引宽度为767字节或3072字节。请参见[第15.22节“ InnoDB限制”](https://dev.mysql.com/doc/refman/8.0/en/innodb-limits.html)。[`MyISAM`](https://dev.mysql.com/doc/refman/8.0/en/myisam-storage-engine.html)表的最大索引宽度为 1000字节。请参见 [第16.2节“ MyISAM存储引擎”](https://dev.mysql.com/doc/refman/8.0/en/myisam-storage-engine.html)。索引可使用的柱的前缀[`CHAR`](https://dev.mysql.com/doc/refman/8.0/en/char.html)， [`VARCHAR`](https://dev.mysql.com/doc/refman/8.0/en/char.html)， [`BLOB`](https://dev.mysql.com/doc/refman/8.0/en/blob.html)，或 [`TEXT`](https://dev.mysql.com/doc/refman/8.0/en/blob.html)列类型。

#### 连接性

- 客户端可以使用多种协议连接到MySQL Server：
  - 客户端可以在任何平台上使用TCP / IP套接字进行连接。
  - 在Windows系统上，如果在[`named_pipe`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_named_pipe)启用系统变量的情况下启动服务器，则客户端可以使用命名管道进行连接 。如果在[`shared_memory`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_shared_memory)启用系统变量的情况下启动，Windows服务器还支持共享内存连接 。客户端可以使用该[`--protocol=memory`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_protocol)选项通过共享内存进行连接 。
  - 在Unix系统上，客户端可以使用Unix域套接字文件进行连接。
- MySQL客户端程序可以用多种语言编写。用C编写的客户端库可用于用C或C ++编写的客户端，或提供C绑定的任何语言的客户端。
- 提供了C，C ++，Eiffel，Java，Perl，PHP，Python，Ruby和Tcl的API，使MySQL客户端可以用多种语言编写。请参阅[第29章，*连接器和API*](https://dev.mysql.com/doc/refman/8.0/en/connectors-apis.html)。
- 连接器/ ODBC（MyODBC）接口为使用ODBC（开放数据库连接）连接的客户端程序提供MySQL支持。例如，您可以使用MS Access连接到MySQL服务器。客户端可以在Windows或Unix上运行。连接器/ ODBC源可用。支持所有ODBC 2.5功能，以及许多其他功能。参见《 [MySQL Connector / ODBC开发人员指南》](https://dev.mysql.com/doc/connector-odbc/en/)。
- Connector / J接口为使用JDBC连接的Java客户端程序提供MySQL支持。客户端可以在Windows或Unix上运行。连接器/ J源可用。参见《 [MySQL Connector / J 5.1开发人员指南》](https://dev.mysql.com/doc/connector-j/5.1/en/)。
- MySQL Connector / NET使开发人员可以轻松创建需要与MySQL安全，高性能数据连接的.NET应用程序。它实现了必需的ADO.NET接口，并集成到ADO.NET感知工具中。开发人员可以使用他们选择的.NET语言来构建应用程序。MySQL Connector / NET是用100％纯C＃编写的完全托管的ADO.NET驱动程序。参见《 [MySQL Connector / NET开发人员指南》](https://dev.mysql.com/doc/connector-net/en/)。

#### 本土化

- 服务器可以用多种语言向客户端提供错误消息。请参见[第10.12节“设置错误消息语言”](https://dev.mysql.com/doc/refman/8.0/en/error-message-language.html)。
- 几个不同的字符集，包括全面支持 `latin1`（CP1252） ，`german`， `big5`，`ujis`一些Unicode字符集等。例如，在表名和列名中允许使用斯堪的纳维亚字符“ `å`”， “ `ä`”和 “ `ö`”。
- 所有数据都保存在所选字符集中。
- 排序和比较是根据默认字符集和排序规则完成的。可以在启动MySQL服务器时更改此[设置](https://dev.mysql.com/doc/refman/8.0/en/charset-server.html)（请参见 [第10.3.2节“服务器字符集和排序规则”](https://dev.mysql.com/doc/refman/8.0/en/charset-server.html)）。要查看非常高级的排序示例，请查看Czech排序代码。MySQL Server支持许多不同的字符集，可以在编译时和运行时指定它们。
- 服务器时区可以动态更改，并且各个客户端可以指定自己的时区。请参见 [第5.1.15节“ MySQL服务器时区支持”](https://dev.mysql.com/doc/refman/8.0/en/time-zone-support.html)。

#### 客户和工具

- MySQL包括几个客户端程序和实用程序。这些包括命令行程序（例如 [**mysqldump**](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)和 [**mysqladmin**](https://dev.mysql.com/doc/refman/8.0/en/mysqladmin.html)）以及图形程序（例如 [MySQL Workbench）](https://dev.mysql.com/doc/refman/8.0/en/workbench.html)。
- MySQL Server内置了对SQL语句的支持，以检查，优化和修复表。这些语句可通过 [**mysqlcheck**](https://dev.mysql.com/doc/refman/8.0/en/mysqlcheck.html)客户端[**从命令行使用**](https://dev.mysql.com/doc/refman/8.0/en/mysqlcheck.html)。MySQL还包括 [**myisamchk**](https://dev.mysql.com/doc/refman/8.0/en/myisamchk.html)，这是一个非常快的命令行实用程序，用于在`MyISAM` 表上执行这些操作。请参阅[第4章，*MySQL程序*](https://dev.mysql.com/doc/refman/8.0/en/programs.html)。

## MySQL架构

### MYISAM

- `MyISAM不支持事务和行锁`，在MySQL5.1之前的版本是默认的存储引擎，有一个缺陷是崩溃后无法恢复。
- 优点是对于只读的数据，或者表比较小，可以忍受修复操作，可以继续使用
- MyISAM会将表存储在两个文件中:数据文件和索引文件，分别以.MYD和.MYI为扩展名
- MyISAM表可以包含动态或者静态(长度固定)行。MySQL会根据表的定义来决定采用何种行格式。MyISAM表可以存储的行记录数，一般受限于可用的磁盘空间,或者操作系统中单个文件的最大尺寸。
- `MyISAM对整张表加锁，而不是针对行`。读取时会对需要读到的所有表加共享锁,写入时则对表加排他锁。但是在表有读取查询的同时，也可以往表中插入新的记录(这被称为并发插入)

### INNODB

https://www.jianshu.com/p/d4cc0ea9d097

https://juejin.cn/post/6844904190477598733

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20201230222014927.png" alt="image-20201230222014927" style="zoom:50%;" />

大家都知道mysql中数据是存储在物理磁盘上的，而真正的数据处理又是在内存中执行的。由于磁盘的读写速度非常慢，如果每次操作都对磁盘进行频繁读写的话，那么性能一定非常差。为了上述问题，**InnoDB将数据划分为若干页，以页作为磁盘与内存交互的基本单位，一般页的大小为16KB**。这样的话，一次性至少读取1页数据到内存中或者将1页数据写入磁盘。通过减少内存与磁盘的交互次数，从而提升性能。

其实，这本质上就是一种典型的缓存设计思想，一般缓存的设计基本都是从`时间维度`或者`空间维度`进行考量的：

1. `时间维度`：如果一条数据正在在被使用，那么在接下来一段时间内大概率还会再被使用。可以认为`热点数据缓存`都属于这种思路的实现。
2. `空间维度`：如果一条数据正在在被使用，那么存储在它附近的数据大概率也会很快被使用。`InnoDB的数据页`和`操作系统的页缓存`则是这种思路的体现。

- InnDB是Mysql默认的事务型存储引擎。它被设计用来处理大量的短期(short-lived) 事务，短期事务大部分情况是正常提交的，很少会被回滚。InnoDB的性能和自动崩溃恢复特性，使得它在非事务型存储的需求中也很流行
- InnoDB的数据存储在表空间(tablespace) 中，表空间是由InnoDB管理的-个黑盒子，由一系列的数据文件组成。
- InnoDB采用`MVCC`来支持高并发，并且实现了四个标准的隔离级别。其默认级别是 REPEATABLE READ (`可重复读`) ,并且通过`间隙锁`(next-key locking)策略防止`幻读`的出现。间隙锁使得InnoDB不仅仅锁定查询涉及的行，还会对索引中的间隙进行锁定，以防止幻影行的插入。
- InnoDB表是基于聚簇索引建立的。聚簇素引对主键查询有很高的性能，不过它的二级索引(secondary index，非主键索引)中必须包含主键列，所以如果主键列很大的话，其他的所有索引都会很大。因此，若表上的索引较多的话，主键应当尽可能的小。
- InnoDB内部做了很多优化，包括从磁盘读取数据时采用的可预测性预读，能够自动在 内存中创建hash索引以加速读操作的自适应哈希索引(adaptive hash index),以及能够 加速插入操作的插入缓冲区(insert buffer)等

### MEMORY

> MEMORY 存储引擎将表中的数据存储到内存中，为查询和引用其他表数据提供快速访问。MEMORY 主要特性有：

1. MEMORY 表的每个表可以多达 32 个索引，每个索引 16 列，以及 500B 的最大键长度。
2. MEMORY 存储引擎引擎执行 HASH 和 BTREE 索引。
3. 可以在一个 MEMORY 表中有非唯一键。
4. MEMORY 不支持 BLOB 或 TEXT 列。
5. MEMORY 表使用一个固定的记录长度格式。
6. MEMORY 支持 AUTO_INCREMENT 列和对包含 NULL 值的列索引。
7. MEMORY 表内容被存在内存中，内存是 MEMORY 表和服务器在查询处理时的空闲中创建的内部表共享。
8. MEMORY 表在所有客户端之间共享（就像其他任何非 TEMPORARY 表）。
9. 当不再需要 MEMORY 表的内容时，要释放被 MEMORY 表使用的内存，应该执行 DELETE FROM 或 TRUNCATE TABLE，或者删除整个表（使用 DROP TABLE)。

### MYISAM和INNODB区别

**MyISAM与InnoDB区别**

|                                                              | **MyISAM**                                                   | **Innodb**                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 存储结构                                                     | 每张表被存放在三个文件：frm-表格定义、MYD(MYData)-数据文件、MYI(MYIndex)-索引文件 | 所有的表都保存在同一个数据文件中（也可能是多个文件，或者是独立的表空间文件），InnoDB表的大小只受限于操作系统文件的大小，一般为2GB |
| 存储空间                                                     | MyISAM可被压缩，存储空间较小                                 | InnoDB的表需要更多的内存和存储，它会在主内存中建立其专用的缓冲池用于高速缓冲数据和索引 |
| 可移植性、备份及恢复                                         | 由于MyISAM的数据是以文件的形式存储，所以在跨平台的数据转移中会很方便。在备份和恢复时可单独针对某个表进行操作 | 免费的方案可以是拷贝数据文件、备份 binlog，或者用 mysqldump，在数据量达到几十G的时候就相对痛苦了 |
| 文件格式                                                     | 数据和索引是分别存储的，数据.MYD，索引.MYI                   | 数据和索引是集中存储的，.ibd                                 |
| 记录存储顺序                                                 | 按记录插入顺序保存                                           | 按主键大小有序插入                                           |
| 外键                                                         | 不支持                                                       | 支持                                                         |
| 事务                                                         | 不支持                                                       | 支持                                                         |
| 锁支持（锁是避免资源争用的一个机制，MySQL锁对用户几乎是透明的） | 表级锁定                                                     | 行级锁定、表级锁定，锁定力度小并发能力高                     |
| SELECT                                                       | MyISAM更优                                                   |                                                              |
| INSERT、UPDATE、DELETE                                       |                                                              | InnoDB更优                                                   |
| select count(*)                                              | myisam更快，因为myisam内部维护了一个计数器，可以直接调取。   |                                                              |
| 索引的实现方式                                               | B+树索引，myisam 是堆表                                      | B+树索引，Innodb 是索引组织表                                |
| 哈希索引                                                     | 不支持                                                       | 支持                                                         |
| 全文索引                                                     | 支持                                                         | 不支持                                                       |

## MySQL的文件

### 数据文件

`MySQL`中，将表的结构定义存放在以`frm`为后缀的文件中。

在`InnoDB`中，所有的数据存储结构如下：

![image-20200811152424609](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c29c03c33b504034ab1da1df20fe36a2~tplv-k3u1fbpfcp-zoom-1.image)

**表空间**:

表空间是`InnoDB`存储引擎逻辑的最高层，所有的数据都存放在表空间中。

在`InnoDB`中，表空间可以设置为共享表空间和独立表空间。

- 共享表空间： 所有数据存放在同一个表空间中
- 独立表空间： 每张表的数据单独放在一个表空间中，（仅仅是数据，索引，插入缓冲单独存放，回滚信息，事务信息等还是放在原来的共享表空间中）

**段**:表空间由各个段组成，一般分为数据段，索引段，回滚段等。

**区**:区是由连续页组成的空间，每个区大小1`MB`,多个区便组成了一个段。

**页**

多个页组成了一个区，一般来说，一页的大小为`16K`,因此64个页组成了一个区，可以通过参数设置页的大小为4K，8K,16K等，页是数据文件管理的最小单位，也是文件空间分配的最小单位

`InnoDB`中常见的页有：

- 数据页(`B-tree Node`)
- `undo`页（`undo Log Page`）
- 系统页（`System Page`）
- ...

### 日志文件

MySQL Server有几个日志，可以帮助您了解正在发生的活动。

| 日志类型              | 记录的信息                                                   |
| :-------------------- | :----------------------------------------------------------- |
| 错误记录              | 启动，运行或停止[**mysqld**](https://dev.mysql.com/doc/refman/8.0/en/mysqld.html)遇到的问题 |
| 通用查询日志          | 建立的客户连接和从客户那里收到的陈述                         |
| 二进制日志            | 更改数据的语句（也用于复制）                                 |
| 中继日志              | 从复制源服务器收到的数据更改                                 |
| 慢查询日志            | [`long_query_time`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_long_query_time)执行耗时超过几秒钟的查询 |
| DDL日志（元数据日志） | DDL语句执行的元数据操作                                      |

默认情况下，除Windows上的错误日志外，没有启用任何日志。（DDL日志总是在需要时创建，并且没有用户可配置的选项；请参阅[DDL日志](https://dev.mysql.com/doc/refman/5.7/en/ddl-log.html)。）以下特定于日志的部分提供有关启用日志记录的服务器选项的信息。



默认情况下，服务器为数据目录中所有已启用的日志写入文件。您可以通过刷新日志来强制服务器关闭并重新打开日志文件（或在某些情况下切换到新的日志文件）。发出[`FLUSH LOGS`](https://dev.mysql.com/doc/refman/8.0/en/flush.html#flush-logs)语句时会发生日志刷新 ；使用 或参数执行 [**mysqladmin**](https://dev.mysql.com/doc/refman/8.0/en/mysqladmin.html) ; 或使用 或 选项执行 [**mysqldump**](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)。请参见 [第13.7.8.3节“ FLUSH语句”](https://dev.mysql.com/doc/refman/8.0/en/flush.html)，[第4.5.2节“ ](https://dev.mysql.com/doc/refman/8.0/en/mysqladmin.html)[**mysqladmin**](https://dev.mysql.com/doc/refman/8.0/en/mysqladmin.html)[ -MySQL服务器管理程序”](https://dev.mysql.com/doc/refman/8.0/en/mysqladmin.html)和 [第4.5.4节“ ](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)[**mysqldump-**](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)[数据库备份程序”](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)`flush-logsrefresh`[`--flush-logs`](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html#option_mysqldump_flush-logs)[`--master-data`](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html#option_mysqldump_master-data)。另外，当二进制日志的大小达到[`max_binlog_size`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_max_binlog_size)系统变量的值时，将刷新该二进制日志 。

您可以在运行时控制常规查询日志和慢速查询日志。您可以启用或禁用日志记录，或更改日志文件名。您可以告诉服务器将常规查询条目和慢速查询条目写入日志表和/或日志文件。有关详细信息，请参见 [第5.4.1节“选择常规查询日志和慢速查询日志输出目的地”](https://dev.mysql.com/doc/refman/8.0/en/log-destinations.html)，[第5.4.3节“常规查询日志”](https://dev.mysql.com/doc/refman/8.0/en/query-log.html)和 [第5.4.5节“慢速查询日志”](https://dev.mysql.com/doc/refman/8.0/en/slow-query-log.html)。

中继日志仅用于副本，以保存来自复制源服务器的数据更改，这些更改也必须在副本上进行。有关中继日志内容和配置的讨论，请参见 [第17.2.4.1节“中继日志”](https://dev.mysql.com/doc/refman/8.0/en/replica-logs-relaylog.html)。

#### 错误日志ERROR LOG

通常情况下记录的不光是错误信息，也包括mysql启动和关闭过程中的信息，以及启动复制线程时的信息。

记录Mysql运行过程中的Error、Warning、Note等信息，系统出错或者某条记录出问题可以查看Error日志。

- Mysql的错误日志默认以hostname.err存放在Mysql的日志目录，可以通过以下语句查看：

  

  ```sql
  mysql> show variables like "log_error";
  +---------------+-----------------------------------------+
  | Variable_name | Value                                   |
  +---------------+-----------------------------------------+
  | log_error     | /home/QAuser/local/mysql/logs/error.log |
  +---------------+-----------------------------------------+
  ```

- 修改错误日志的地址可以在/etc/my.cnf中添加--log-error = [filename]来开启mysql错误日志。

#### 查询日志

这是最不应该记录的日志。因为一个非常繁忙的数据库服务器，其查询会有很多，每一次都记录日志会导致系统性能下降。而且还会有额外的空间开销。MySQL默认没有开启这个日志。注意不仅仅是SELECT。

记录mysql的日常日志，包括查询、修改、更新等的每条sql。

- 先查看mysql是否启用了查询日志: **show global variables like "%genera%"**

  

  ```sql
  mysql> show global variables like "%genera%";
  +------------------+---------------------------------------------+
  | Variable_name    | Value                                       |
  +------------------+---------------------------------------------+
  | general_log      | OFF                                         |
  | general_log_file | /home/QAuser/local/mysql/data/ALYBJ59-6.log |
  +------------------+---------------------------------------------+
  ```

  

- 查询日志的输出文件可以在**/etc/my.cnf** 中添加**general-log-file = [filename]**

- Mysql打开general log日志后，所有的查询语句都可以在general log文件中输出，如果打开，文件会非常大，建议调试的时候打开，平时关闭

  

  ```csharp
  mysql> set global general_log = on;
  Query OK, 0 rows affected (0.01 sec)
  
  mysql> set global general_log = off;
  Query OK, 0 rows affected (0.01 sec)
  ```

- **注意：**

  如果打开了日志功能，但是没有写入日志，那就有可能是mysql对日志文件的权限不够，所以需要指定权限

#### 二进制日志 BIN LOG

`binlog` 用于记录数据库执行的写入性操作(不包括查询)信息，以二进制的形式保存在磁盘中。`binlog` 是 `mysql`的逻辑日志，并且由 `Server` 层进行记录，使用任何存储引擎的 `mysql` 数据库都会记录 `binlog` 日志。

- **逻辑日志**：可以简单理解为记录的就是sql语句 。
- **物理日志**：`mysql` 数据最终是保存在数据页中的，物理日志记录的就是数据页变更 。

`binlog` 是通过追加的方式进行写入的，可以通过 `max_binlog_size` 参数设置每个 `binlog`
 文件的大小，当文件大小达到给定值之后，会生成新的文件来保存日志。

##### binlog使用场景

在实际应用中， `binlog` 的主要使用场景有两个，分别是 **主从复制** 和 **数据恢复** 。

1. **主从复制** ：在 `Master` 端开启 `binlog` ，然后将 `binlog` 发送到各个 `Slave` 端， `Slave` 端重放 `binlog` 从而达到主从数据一致。
2. **数据恢复** ：通过使用 `mysqlbinlog` 工具来恢复数据。

##### binlog刷盘时机

对于 `InnoDB` 存储引擎而言，只有在事务提交时才会记录 `biglog` ，此时记录还在内存中，那么 `biglog`
 是什么时候刷到磁盘中的呢？`mysql` 通过 `sync_binlog` 参数控制 `biglog` 的刷盘时机，取值范围是 `0-N`
 ：

- 0：不去强制要求，由系统自行判断何时写入磁盘；
- 1：每次 `commit` 的时候都要将 `binlog` 写入磁盘；
- N：每N个事务，才会将 `binlog` 写入磁盘。

从上面可以看出， `sync_binlog` 最安全的是设置是 `1` ，这也是 `MySQL 5.7.7`
 之后版本的默认值。但是设置一个大一些的值可以提升数据库性能，因此实际情况下也可以将值适当调大，牺牲一定的一致性来获取更好的性能。

##### binlog日志格式

`binlog` 日志有三种格式，分别为 `STATMENT` 、 `ROW` 和 `MIXED` 。

> 在 `MySQL 5.7.7` 之前，默认的格式是 `STATEMENT` ， `MySQL 5.7.7` 之后，默认值是 `ROW`。日志格式通过 `binlog-format` 指定。

- `STATMENT`：基于`SQL` 语句的复制( `statement-based replication, SBR` )，每一条会修改数据的sql语句会记录到 `binlog` 中  。
- 优点：不需要记录每一行的变化，减少了 binlog 日志量，节约了  IO  , 从而提高了性能；
- 缺点：在某些情况下会导致主从数据不一致，比如执行sysdate() 、  slepp()  等 。
- `ROW`：基于行的复制(`row-based replication, RBR` )，不记录每条sql语句的上下文信息，仅需记录哪条数据被修改了 。
- 优点：不会出现某些特定情况下的存储过程、或function、或trigger的调用和触发无法被正确复制的问题 ；
- 缺点：会产生大量的日志，尤其是`alter table` 的时候会让日志暴涨
- `MIXED`：基于`STATMENT` 和 `ROW` 两种模式的混合复制( `mixed-based replication, MBR` )，一般的复制使用 `STATEMENT` 模式保存 `binlog` ，对于 `STATEMENT` 模式无法复制的操作使用 `ROW` 模式保存 `binlog`
- 优点： binlog中可以不记录执行的sql语句的上下文相关的信息，仅需要记录那一条记录被修改成什么了。所以row的日志内容会非常清楚的记录下每一行数据修改的细节。而且不会出现某些特定情况下的存储过程，或function，以及trigger的调用和触发无法被正确复制的问题.
- 缺点:所有的执行的语句当记录到日志中的时候，都将以每行记录的修改来记录，这样可能会产生大量的日志内容。

##### binlog配置

查看mysql中二进制文件的配置情况：show variables like "%log_bin%";

```ruby
mysql> show variables like "%log_bin%";
+---------------------------------+-------+
| Variable_name                   | Value |
+---------------------------------+-------+
| log_bin                         | OFF   |
| log_bin_basename                |       |
| log_bin_index                   |       |
| log_bin_trust_function_creators | OFF   |
| log_bin_use_v1_row_events       | OFF   |
| sql_log_bin                     | ON    |
+---------------------------------+-------+
```

log_bin : 用于设定是否启用二进制日志, 由此看是未开启

- 配置文件仍然是在 /etc/my.cnf 中， 修改/etc/my.cnf， 增加日志文件目录：

  ```cpp
  log_bin = /tmp/mysql-bin.log
  ```

- 重启mysql ：

  ```bash
  bash-3.2# mysql.server start;
  Starting MySQL
  . ERROR! The server quit without updating PID file (/usr/local/Cellar/mysql/5.7.9/data/mysql.pid).
  ```

- 又报错，查看错误日志，我的配置在/tmp/mysql.log

  ```jsx
  151224 00:37:34 mysqld_safe Starting mysqld daemon with databases from /usr/local/var/mysql
  2015-12-23T16:37:34.643998Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
  2015-12-23T16:37:34.644124Z 0 [Warning] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
  2015-12-23T16:37:34.644129Z 0 [Warning] 'NO_AUTO_CREATE_USER' sql mode was not set.
  2015-12-23T16:37:34.644189Z 0 [Warning] Insecure configuration for --secure-file-priv: Current value does not restrict location of generated files. Consider setting it to a valid, non-empty path.
  2015-12-23T16:37:34.644226Z 0 [Note] /usr/local/Cellar/mysql/5.7.9/bin/mysqld (mysqld 5.7.9-log) starting as process 24268 ...
  2015-12-23T16:37:34.646468Z 0 [Warning] Setting lower_case_table_names=2 because file system for /usr/local/var/mysql/ is case insensitive
  2015-12-23T16:37:34.646945Z 0 [ERROR] You have enabled the binary log, but you haven't provided the mandatory server-id. Please refer to the proper server start-up parameters documentation
  2015-12-23T16:37:34.646978Z 0 [ERROR] Aborting
  
  2015-12-23T16:37:34.646991Z 0 [Note] Binlog end
  2015-12-23T16:37:34.647068Z 0 [Note] /usr/local/Cellar/mysql/5.7.9/bin/mysqld: Shutdown complete
  
  151224 00:37:34 mysqld_safe mysqld from pid file /usr/local/Cellar/mysql/5.7.9/data/mysql.pid ended
  ```

重点：

**You have enabled the binary log, but you haven't provided the mandatory server-id. Please refer to the proper server start-up parameters documentation**

说明需要配置一个server-id， 再拿这句话百度，果然是这样。所以在 配置文件/etc/my.cn中添加 server-id = 1，再重启mysql，解决问题。而且在配置的bin-log同级目录增加了mysql-bin.000001  mysql-bin.index   mysql-bin.log 三个文件，前两个是自动生成。

参数：

log_bin：设置此参数表示启用binlog功能，并指定路径名称

log_bin_index：设置此参数是指定二进制索引文件的路径与名称

binlog_do_db：此参数表示只记录指定数据库的二进制日志
 binlog_ignore_db：此参数表示不记录指定的数据库的二进制日志

max_binlog_cache_size：此参数表示binlog使用的内存最大的尺寸

binlog_cache_size：此参数表示binlog使用的内存大小，可以通过状态变量binlog_cache_use和binlog_cache_disk_use来帮助测试。binlog_cache_use：使用二进制日志缓存的事务数量

binlog_cache_disk_use:使用二进制日志缓存但超过binlog_cache_size值并使用临时文件来保存事务中的语句的事务数量

max_binlog_size：Binlog最大值，最大和默认值是1GB，该设置并不能严格控制Binlog的大小，尤其是Binlog比较靠近最大值而又遇到一个比较大事务时，为了保证事务的完整性，不可能做切换日志的动作，只能将该事务的所有SQL都记录进当前日志，直到事务结束

sync_binlog：这个参数直接影响mysql的性能和完整性

sync_binlog=0：
 当事务提交后，Mysql仅仅是将binlog_cache中的数据写入Binlog文件，但不执行fsync之类的磁盘        同步指令通知文件系统将缓存刷新到磁盘，而让Filesystem自行决定什么时候来做同步，这个是性能最好的。
 sync_binlog=n，在进行n次事务提交以后，Mysql将执行一次fsync之类的磁盘同步指令，同志文件系统将Binlog文件缓存刷新到磁盘。
 Mysql中默认的设置是sync_binlog=0，即不作任何强制性的磁盘刷新指令，这时性能是最好的，但风险也是最大的。一旦系统绷Crash，在文件系统缓存中的所有Binlog信息都会丢失

- 登录mysql，再次查看bin-log的状态，属于启用状态

  ```ruby
  mysql> show variables like "%log_bin%";
  +---------------------------------+----------------------+
  | Variable_name                   | Value                |
  +---------------------------------+----------------------+
  | log_bin                         | ON                   |
  | log_bin_basename                | /tmp/mysql-bin       |
  | log_bin_index                   | /tmp/mysql-bin.index |
  | log_bin_trust_function_creators | OFF                  |
  | log_bin_use_v1_row_events       | OFF                  |
  | sql_log_bin                     | ON                   |
  +---------------------------------+----------------------+
  ```

- binlog的删除
  binlog的删除可以手工删除或自动删除

自动删除binlog

通过binlog参数（expire_logs_days ）来实现mysql自动删除binlog

```ruby
    mysql> show binary logs;
    +------------------+-----------+
    | Log_name         | File_size |
    +------------------+-----------+
    | mysql-bin.000001 |       869 |
    +------------------+-----------+
    1 row in set (0.00 sec)
    
    mysql> show variables like 'expire_logs_days' ;
    +------------------+-------+
    | Variable_name    | Value |
    +------------------+-------+
    | expire_logs_days | 0     |
    +------------------+-------+
    1 row in set (0.00 sec)
    
    mysql> ;set global expire_logs_days=3;
    ERROR: 
    No query specified
    
    Query OK, 0 rows affected (0.00 sec)
    
    mysql> show variables like 'expire_logs_days' ;
    +------------------+-------+
    | Variable_name    | Value |
    +------------------+-------+
    | expire_logs_days | 3     |
    +------------------+-------+
    1 row in set (0.00 sec)
```

手工删除binlog

```csharp
    mysql> reset master;   //删除master的binlog
    mysql> reset slave;    //删除slave的中继日志
    mysql> purge master logs before '2012-03-30 17:20:00';  //删除指定日期以前的日志索引中binlog日志文件
    mysql> purge master logs to 'mysql-bin.000001';   //删除指定日志文件的日志索引中binlog日志文件
```

或者直接用操作系统命令直接删除

```dart
    mysql> set sql_log_bin=1/0; //如果用户有super权限，可以启用或禁用当前会话的binlog记录
    mysql> show master logs; //查看master的binlog日志 
    mysql> show binary logs; //查看master的binlog日志
    mysql> show master status; //用于提供master二进制日志文件的状态信息
    mysql> show slave hosts; //显示当前注册的slave的列表。不以--report-host=slave_name选项为开头的slave不会显示在本列表中
```

- blog查看：通过mysqlbinlog 查看日志文件

  ```bash
  bash-3.2# mysqlbinlog /tmp/mysql-bin.log 
  ```

#### REDO LOG

##### 为什么需要redo log

我们都知道，事务的四大特性里面有一个是**持久性** ，具体来说就是**只要事务提交成功，那么对数据库做的修改就被永久保存下来了，不可能因为任何原因再回到原来的状态** 。那么 `mysql`是如何保证一致性的呢？最简单的做法是在每次事务提交的时候，将该事务涉及修改的数据页全部刷新到磁盘中。但是这么做会有严重的性能问题，主要体现在两个方面：

1. 因为 `Innodb` 是以 `页` 为单位进行磁盘交互的，而一个事务很可能只修改一个数据页里面的几个字节，这个时候将完整的数据页刷到磁盘的话，太浪费资源了！
2. 一个事务可能涉及修改多个数据页，并且这些数据页在物理上并不连续，使用随机IO写入性能太差！

因此 `mysql` 设计了 `redo log` ， **具体来说就是只记录事务对数据页做了哪些修改** ，这样就能完美地解决性能问题了(相对而言文件更小并且是顺序IO)。

##### redo log基本概念

`redo log` 包括两部分：一个是内存中的日志缓冲( `redo log buffer` )，另一个是磁盘上的日志文件( `redo log file` )。`mysql` 每执行一条 `DML` 语句，先将记录写入 `redo log buffer` ，后续某个时间点再一次性将多个操作记录写到 `redo log file` 。这种 **先写日志，再写磁盘** 的技术就是 `MySQL`里经常说到的 `WAL(Write-Ahead Logging)` 技术。

在计算机操作系统中，用户空间( `user space` )下的缓冲区数据一般情况下是无法直接写入磁盘的，中间必须经过操作系统内核空间(`kernel space` )缓冲区( `OS Buffer` )。因此， `redo log buffer` 写入 `redo log file` 实际上是先写入 `OS Buffer` ，然后再通过系统调用 `fsync()` 将其刷到 `redo log file`中，过程如下：

<img src="https://static001.geekbang.org/resource/image/5a/28/5ae7d074c34bc5bd55c82781de670c28.png" alt="img" style="zoom:50%;" />

`mysql` 支持三种将 `redo log buffer` 写入 `redo log file` 的时机，可以通过 `innodb_flush_log_at_trx_commit` 参数配置，各参数值含义如下：

| 参数值              | 含义                                                         |
| ------------------- | ------------------------------------------------------------ |
| 0（延迟写）         | 事务提交时不会将 `redo log buffer` 中日志写入到 `os buffer` ，而是每秒写入 `os buffer` 并调用 `fsync()` 写入到 `redo log file` 中。也就是说设置为0时是(大约)每秒刷新写入到磁盘中的，当系统崩溃，会丢失1秒钟的数据。 |
| 1（实时写，实时刷） | 事务每次提交都会将 `redo log buffer` 中的日志写入 `os buffer` 并调用 `fsync()` 刷到 `redo log file` 中。这种方式即使系统崩溃也不会丢失任何数据，但是因为每次提交都写入磁盘，IO的性能较差。 |
| 2（实时写，延迟刷） | 每次提交都仅写入到 `os buffer` ，然后是每秒调用 `fsync()` 将 `os buffer`中的日志写入到 `redo log file` 。 |

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20210109233605579.png" alt="image-20210109233605579" style="zoom:50%;" />

##### redo log记录形式

前面说过， `redo log` 实际上记录数据页的变更，而这种变更记录是没必要全部保存，因此 `redo log`
 实现上采用了大小固定，循环写入的方式，当写到结尾时，会回到开头循环写日志。如下图：

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20210109233635232.png" alt="image-20210109233635232" style="zoom:50%;" />

同时我们很容易得知， 在innodb中，既有`redo log` 需要刷盘，还有 `数据页` 也需要刷盘， `redo log`存在的意义主要就是降低对 `数据页` 刷盘的要求。在上图中， `write pos` 表示 `redo log` 当前记录的 `LSN` (逻辑序列号)位置， `check point` 表示 **数据页更改记录** 刷盘后对应 `redo log` 所处的 `LSN`(逻辑序列号)位置。`write pos` 到 `check point` 之间的部分是 `redo log` 空着的部分，用于记录新的记录；`check point` 到 `write pos` 之间是 `redo log` 待落盘的数据页更改记录。当 `write pos`追上 `check point`时，会先推动 `check point` 向前移动，空出位置再记录新的日志。

启动 `innodb` 的时候，不管上次是正常关闭还是异常关闭，总是会进行恢复操作。因为 `redo log`记录的是数据页的物理变化，因此恢复的时候速度比逻辑日志(如 `binlog` )要快很多。重启 `innodb` 时，首先会检查磁盘中数据页的 `LSN` ，如果数据页的 `LSN` 小于日志中的 `LSN` ，则会从 `checkpoint` 开始恢复。还有一种情况，在宕机前正处于
 `checkpoint` 的刷盘过程，且数据页的刷盘进度超过了日志页的刷盘进度，此时会出现数据页中记录的 `LSN` 大于日志中的 `LSN`
 ，这时超出日志进度的部分将不会重做，因为这本身就表示已经做过的事情，无需再重做。

##### redo log与binlog区别

| redo log | binlog                                                       |
| -------- | ------------------------------------------------------------ |
| 文件大小 | `redo log` 的大小是固定的。                                  |
| 实现方式 | `redo log` 是 `InnoDB` 引擎层实现的，并不是所有引擎都有。    |
| 记录方式 | redo log 采用循环写的方式记录，当写到结尾时，会回到开头循环写日志。 |
| 适用场景 | `redo log` 适用于崩溃恢复(crash-safe)                        |

由 `binlog` 和 `redo log` 的区别可知：`binlog` 日志只用于归档，只依靠 `binlog` 是没有 `crash-safe` 能力的。但只有 `redo log` 也不行，因为 `redo log` 是 `InnoDB`
 特有的，且日志上的记录落盘后会被覆盖掉。因此需要 `binlog` 和 `redo log`
 二者同时记录，才能保证当数据库发生宕机重启时，数据不会丢失。

#### UNDO LOG

数据库事务四大特性中有一个是 **原子性** ，具体来说就是 **原子性是指对数据库的一系列操作，要么全部成功，要么全部失败，不可能出现部分成功的情况**。实际上， **原子性** 底层就是通过 `undo log` 实现的。`undo log` 主要记录了数据的逻辑变化，比如一条 `INSERT` 语句，对应一条 `DELETE` 的 `undo log` ，对于每个 `UPDATE` 语句，对应一条相反的 `UPDATE` 的 `undo log` ，这样在发生错误时，就能回滚到事务之前的数据状态。同时， `undo log` 也是 `MVCC`(多版本并发控制)实现的关键。

http://mysql.taobao.org/monthly/2015/04/01/

https://jimmy2angel.github.io/2019/05/07/InnoDB-undo-log/

#### 慢查询日志 SLOW LOG

记录Mysql 慢查询的日志
 修改配置文件 /etc/my.cnf

##### 1. Mysql 慢查询配置相关命令：

- 查看日志功能是否开启：show variables like "%slow%";

  

  ```ruby
  mysql> show variables like "%slow%";
  +---------------------------+----------------------------------------------------+
  | Variable_name             | Value                                              |
  +---------------------------+----------------------------------------------------+
  | log_slow_admin_statements | OFF                                                |
  | log_slow_slave_statements | OFF                                                |
  | slow_launch_time          | 2                                                  |
  | slow_query_log            | OFF                                                |
  | slow_query_log_file       | /usr/local/var/mysql/tongkundeMacBook-Pro-slow.log |
  +---------------------------+----------------------------------------------------+
  ```

  slow_query_log 配置为OFF ， 说明未开启慢日志

- 打开慢日志功能：set global slow_query_log = on;

  ```csharp
  mysql> set global slow_query_log = on;
  Query OK, 0 rows affected (0.06 sec)
  ```

- 查看下默认设置的慢查询的时间：show variables like "%long_query%";

  ```ruby
  mysql> show variables like "%long_query%";
  +-----------------+-----------+
  | Variable_name   | Value     |
  +-----------------+-----------+
  | long_query_time | 10.000000 |
  +-----------------+-----------+
  ```

  可以看出，默认是10秒，按照这个配置，数据得上n亿才能达到，为了测试我们修改一下

##### 2. 修改Mysql配置文件的方式

- 打开/etc/my.cnf , 加入慢查询配置文件

  ```cpp
  slow-query-log = 1
  slow-query-log-file = /tmp/mysql-slow.log
  long_query_time = 1 #设置满请求时间, 设置查多少秒的查询算是慢查询
  ```

  保存退出后要重启mysql

  ```css
  mysql.server restart;
  ```

- 通过mysql命令查看配置：

  ```ruby
  mysql> show variables like "%slow%";
  +---------------------------+---------------------+
  | Variable_name             | Value               |
  +---------------------------+---------------------+
  | log_slow_admin_statements | OFF                 |
  | log_slow_slave_statements | OFF                 |
  | slow_launch_time          | 2                   |
  | slow_query_log            | OFF                 |
  | slow_query_log_file       | /tmp/mysql-slow.log |
  +---------------------------+---------------------+
  ```

  这里显示慢日志功能还未开启

- 打开慢日志功能

  ```csharp
  mysql> set global slow_query_log = on;
  ERROR 29 (HY000): File '/tmp/mysql-slow.log' not found (Errcode: 13 - Permission denied)
  ```

  恩，报错了，说明什么呢，权限不够，那就给权限，退出mysql 执行：

  ```cpp
   chown mysql:mysql /tmp/mysql-slow.log 
  ```

  回到mysql，再次打开慢日志：

  ```csharp
  mysql> set global slow_query_log = on;
  Query OK, 0 rows affected (0.00 sec)
  ```

  ok, 解决

### 总结日志相关的服务器参数

1. expire_logs_days={0..99}
   设定二进制日志的过期天数，超出此天数的二进制日志文件将被自动删除。默认为0，表示不启用过期自动删除功能。如果启用此功能，自动删除工作通常发生在MySQL启动时或FLUSH日志时。作用范围为全局，可用于配置文件，属动态变量。
2. general_log={ON|OFF}
   设定是否启用查询日志，默认值为取决于在启动mysqld时是否使用了–general_log选项。如若启用此项，其输出位置则由–log_output选项进行定义，如果log_output的值设定为NONE，即使用启用查询日志，其也不会记录任何日志信息。作用范围为全局，可用于配置文件，属动态变量。
3. general_log_file=FILE_NAME
   查询日志的日志文件名称，默认为“hostname.log”。作用范围为全局，可用于配置文件，属动态变量。

4. binlog-format={ROW|STATEMENT|MIXED}
   指定二进制日志的类型，默认为STATEMENT。如果设定了二进制日志的格式，却没有启用二进制日志，则MySQL启动时会产生警告日志信息并记录于错误日志中。作用范围为全局或会话，可用于配置文件，且属于动态变量。
5. log={YES|NO}
   是否启用记录所有语句的日志信息于一般查询日志(general query log)中，默认通常为OFF。MySQL 5.6已经弃用此选项。
6. log-bin={YES|NO}
   是否启用二进制日志，如果为mysqld设定了–log-bin选项，则其值为ON，否则则为OFF。其仅用于显示是否启用了二进制日志，并不反应log-bin的设定值。作用范围为全局级别，属非动态变量。
7. log_bin_trust_function_creators={TRUE|FALSE}
   此参数仅在启用二进制日志时有效，用于控制创建存储函数时如果会导致不安全的事件记录二进制日志条件下是否禁止创建存储函数。默认值为0，表示除非用户除了CREATE ROUTING或ALTER ROUTINE权限外还有SUPER权限，否则将禁止创建或修改存储函数，同时，还要求在创建函数时必需为之使用DETERMINISTIC属性，再不然就是附带READS SQL DATA或NO SQL属性。设置其值为1时则不启用这些限制。作用范围为全局级别，可用于配置文件，属动态变量。
8. log_error=/PATH/TO/ERROR_LOG_FILENAME
   定义错误日志文件。作用范围为全局或会话级别，可用于配置文件，属非动态变量。
9. log_output={TABLE|FILE|NONE}
   定义一般查询日志和慢查询日志的保存方式，可以是TABLE、FILE、NONE，也可以是TABLE及FILE的组合(用逗号隔开)，默认为TABLE。如果组合中出现了NONE，那么其它设定都将失效，同时，无论是否启用日志功能，也不会记录任何相关的日志信息。作用范围为全局级别，可用于配置文件，属动态变量。
10. log_query_not_using_indexes={ON|OFF}
    设定是否将没有使用索引的查询操作记录到慢查询日志。作用范围为全局级别，可用于配置文件，属动态变量。
11. log_slave_updates
    用于设定复制场景中的从服务器是否将从主服务器收到的更新操作记录进本机的二进制日志中。本参数设定的生效需要在从服务器上启用二进制日志功能。
12. log_slow_queries={YES|NO}
    是否记录慢查询日志。慢查询是指查询的执行时间超出long_query_time参数所设定时长的事件。MySQL 5.6将此参数修改为了slow_query_log。作用范围为全局级别，可用于配置文件，属动态变量。
13. log_warnings=#
    设定是否将警告信息记录进错误日志。默认设定为1，表示启用；可以将其设置为0以禁用；而其值为大于1的数值时表示将新发起连接时产生的“失败的连接”和“拒绝访问”类的错误信息也记录进错误日志。
14. long_query_time=#
    设定区别慢查询与一般查询的语句执行时间长度。这里的语句执行时长为实际的执行时间，而非在CPU上的执行时长，因此，负载较重的服务器上更容易产生慢查询。其最小值为0，默认值为10，单位是秒钟。它也支持毫秒级的解析度。作用范围为全局或会话级别，可用于配置文件，属动态变量。
15. max_binlog_cache_size{4096 .. 18446744073709547520}
    二进定日志缓存空间大小，5.5.9及以后的版本仅应用于事务缓存，其上限由max_binlog_stmt_cache_size决定。作用范围为全局级别，可用于配置文件，属动态变量。
16. max_binlog_size={4096 .. 1073741824}
    设定二进制日志文件上限，单位为字节，最小值为4K，最大值为1G，默认为1G。某事务所产生的日志信息只能写入一个二进制日志文件，因此，实际上的二进制日志文件可能大于这个指定的上限。作用范围为全局级别，可用于配置文件，属动态变量。

17. max_relay_log_size={4096..1073741824}
    设定从服务器上中继日志的体积上限，到达此限度时其会自动进行中继日志滚动。此参数值为0时，mysqld将使用max_binlog_size参数同时为二进制日志和中继日志设定日志文件体积上限。作用范围为全局级别，可用于配置文件，属动态变量。
18. innodb_log_buffer_size={262144 .. 4294967295}
    设定InnoDB用于辅助完成日志文件写操作的日志缓冲区大小，单位是字节，默认为8MB。较大的事务可以借助于更大的日志缓冲区来避免在事务完成之前将日志缓冲区的数据写入日志文件，以减少I/O操作进而提升系统性能。因此，在有着较大事务的应用场景中，建议为此变量设定一个更大的值。作用范围为全局级别，可用于选项文件，属非动态变量。
19. innodb_log_file_size={108576 .. 4294967295}
    设定日志组中每个日志文件的大小，单位是字节，默认值是5MB。较为明智的取值范围是从1MB到缓存池体积的1/n，其中n表示日志组中日志文件的个数。日志文件越大，在缓存池中需要执行的检查点刷写操作就越少，这意味着所需的I/O操作也就越少，然而这也会导致较慢的故障恢复速度。作用范围为全局级别，可用于选项文件，属非动态变量。
20. innodb_log_files_in_group={2 .. 100}
    设定日志组中日志文件的个数。InnoDB以循环的方式使用这些日志文件。默认值为2。作用范围为全局级别，可用于选项文件，属非动态变量。
21. innodb_log_group_home_dir=/PATH/TO/DIR
    设定InnoDB重做日志文件的存储目录。在缺省使用InnoDB日志相关的所有变量时，其默认会在数据目录中创建两个大小为5MB的名为ib_logfile0和ib_logfile1的日志文件。作用范围为全局级别，可用于选项文件，属非动态变量。
22. relay_log=file_name
    设定中继日志的文件名称，默认为host_name-relay-bin。也可以使用绝对路径，以指定非数据目录来存储中继日志。作用范围为全局级别，可用于选项文件，属非动态变量。
23. relay_log_index=file_name
    设定中继日志的索引文件名，默认为为数据目录中的host_name-relay-bin.index。作用范围为全局级别，可用于选项文件，属非动态变量。
24. relay-log-info-file=file_name
    设定中继服务用于记录中继信息的文件，默认为数据目录中的relay-log.info。作用范围为全局级别，可用于选项文件，属非动态变量。
25. relay_log_purge={ON|OFF}
    设定对不再需要的中继日志是否自动进行清理。默认值为ON。作用范围为全局级别，可用于选项文件，属动态变量。
26. relay_log_space_limit=#
    设定用于存储所有中继日志文件的可用空间大小。默认为0，表示不限定。最大值取决于系统平台位数。作用范围为全局级别，可用于选项文件，属非动态变量。
27. slow_query_log={ON|OFF}
    设定是否启用慢查询日志。0或OFF表示禁用，1或ON表示启用。日志信息的输出位置取决于log_output变量的定义，如果其值为NONE，则即便slow_query_log为ON，也不会记录任何慢查询信息。作用范围为全局级别，可用于选项文件，属动态变量。
28. slow_query_log_file=/PATH/TO/SOMEFILE
    设定慢查询日志文件的名称。默认为hostname-slow.log，但可以通过–slow_query_log_file选项修改。作用范围为全局级别，可用于选项文件，属动态变量。
29. sql_log_bin={ON|OFF}
    用于控制二进制日志信息是否记录进日志文件。默认为ON，表示启用记录功能。用户可以在会话级别修改此变量的值，但其必须具有SUPER权限。作用范围为全局和会话级别，属动态变量。
30. sql_log_off={ON|OFF}
    用于控制是否禁止将一般查询日志类信息记录进查询日志文件。默认为OFF，表示不禁止记录功能。用户可以在会话级别修改此变量的值，但其必须具有SUPER权限。作用范围为全局和会话级别，属动态变量。
31. sync_binlog=#
    设定多久同步一次二进制日志至磁盘文件中，0表示不同步，任何正数值都表示对二进制每多少次写操作之后同步一次。当autocommit的值为1时，每条语句的执行都会引起二进制日志同步，否则，每个事务的提交会引起二进制日志同步。



## MySQL索引原理

https://tech.meituan.com/2014/06/30/mysql-index.html

https://www.cnblogs.com/itwild/p/13703259.html

https://www.hollischuang.com/archives/3818

### 索引目的

在MySQL中，索引(`Index`)是帮助高效获取数据的数据结构。这种数据结构MySQL中最常用的就是B+树(`B+Tree`)。

> Indexes are used to find rows with specific column values quickly. Without an index, MySQL must begin with the first row and then read through the entire table to find the relevant rows.

就好比给你一本书和一篇文章标题，如果没有目录，让你找此标题对应的文章，可能需要从第一页翻到最后一页；如果有目录大纲，你可能只需要在目录页寻找此标题，然后迅速定位文章。

这里我们可以把`书(book)`看成是MySQL中的`table`，把`文章(article)`看成是`table`中的一行记录，即`row`，`文章标题(title)`看成`row`中的一列`column`，`目录`自然就是对`title`列建立的索引`index`了，这样`根据文章标题从书中检索文章`就对应sql语句`select * from book where title = ?`，相应的，书中每增加一篇文章(即`insert into book (title, ...) values ('华山论剑', ...)`)，都需要维护一下`目录`，这样才能从目录中找到新增的文章`华山论剑`，这一操作对应的是MySQL中每插入(`insert`)一条记录需要维护`title`列的索引树(`B+Tree`)。

### 索引结构

提到索引，我们可能会立马想到下面几种数据结构来实现。

#### 哈希表

哈希虽然能够提供`O(1)`的单数据行的查询性能，但是对于`范围查询`和`排序`却无法很好支持，需全表扫描。

#### 红黑树

红黑树(`Red Black Tree`)是一种自平衡二叉查找树，在进行插入和删除操作时通过特定操作保持二叉查找树的平衡，从而获得较高的查找性能。

一般来说，索引本身也很大，往往不可能全部存储在内存中，因此索引往往以索引文件的形式存储的磁盘上。这样的话，索引查找过程中就要产生磁盘I/O消耗，相对于内存存取，I/O存取的消耗远远高于内存，所以评价一个数据结构作为索引的优劣最重要的指标就是查找过程中磁盘I/O次数。换句话说，`索引的结构组织要尽量减少查找过程中磁盘I/O的次数。`

在这里，磁盘I/O的次数取决于树的高度，所以，在数据量较大时，`红黑树会因树的高度较大而造成磁盘IO较多`，从而影响查询效率。

#### B-Tree

B树中的B代表平衡(`Balance`)，而不是二叉(`Binary`)，B树是从平衡二叉树演化而来的。

为了降低树的高度(也就是减少磁盘I/O次数)，把原来`瘦高`的树结构变得`矮胖`，B树会在`每个节点存储多个元素`(红黑树每个节点只会存储一个元素)，并且节点中的元素从左到右递增排列。如下图所示：

[![B-Tree结构图](https://img2020.cnblogs.com/blog/1546632/202008/1546632-20200830195348368-1304078258.png)](https://img2020.cnblogs.com/blog/1546632/202008/1546632-20200830195348368-1304078258.png)

`B-Tree`在查询的时候比较次数其实不比二叉查找树少，但在内存中的大小比较、二分查找的耗时相比磁盘IO耗时几乎可以忽略。 `B-Tree大大降低了树的高度`，所以也就极大地提升了查找性能。

#### B+Tree

`B+Tree`是在`B-Tree`基础上进一步优化，使其更适合实现存储索引结构。InnoDB存储引擎就是用`B+Tree`实现其索引结构。

`B-Tree`结构图中可以看到每个节点中不仅包含数据的`key`值，还有`data`值。而每一个节点的存储空间是有限的，如果`data`值较大时将会导致每个节点能存储的`key`的数量很小，这样会导致B-Tree的高度变大，增加了查询时的磁盘I/O次数，进而影响查询性能。在`B+Tree`中，所有`data`值都是按照键值大小顺序存放在同一层的叶子节点上，而`非叶子节点上只存储key值信息`，这样可以增大每个非叶子节点存储的`key`值数量，降低B+Tree的高度，提高效率。

[![B+Tree结构图](https://img2020.cnblogs.com/blog/1546632/202008/1546632-20200830201413134-394816073.png)](https://img2020.cnblogs.com/blog/1546632/202008/1546632-20200830201413134-394816073.png)

#### 为什么使用B+树

**这里补充一点相关知识** 在计算机中，磁盘往往不是严格按需读取，而是每次都会预读，即使只需要一个字节，磁盘也会从这个位置开始，顺序向后读取一定长度的数据放入内存。这样做的理论依据是计算机科学中著名的`局部性原理`：

> 当一个数据被用到时，其附近的数据也通常会马上被使用。

由于磁盘顺序读取的效率很高（不需要寻道时间，只需很少的旋转时间），因此对于具有局部性的程序来说，预读可以提高I/O效率。预读的长度一般为页(`page`)的整数倍。

#### 磁盘IO与预读

前面提到了访问磁盘，那么这里先简单介绍一下磁盘IO和预读，磁盘读取数据靠的是机械运动，每次读取数据花费的时间可以分为寻道时间、旋转延迟、传输时间三个部分，寻道时间指的是磁臂移动到指定磁道所需要的时间，主流磁盘一般在5ms以下；旋转延迟就是我们经常听说的磁盘转速，比如一个磁盘7200转，表示每分钟能转7200次，也就是说1秒钟能转120次，旋转延迟就是1/120/2 = 4.17ms；传输时间指的是从磁盘读出或将数据写入磁盘的时间，一般在零点几毫秒，相对于前两个时间可以忽略不计。那么访问一次磁盘的时间，即一次磁盘IO的时间约等于5+4.17 = 9ms左右，听起来还挺不错的，但要知道一台500 -MIPS的机器每秒可以执行5亿条指令，因为指令依靠的是电的性质，换句话说执行一次IO的时间可以执行40万条指令，数据库动辄十万百万乃至千万级数据，每次9毫秒的时间，显然是个灾难。下图是计算机硬件延迟的对比图，供大家参考：

![various-system-software-hardware-latencies](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2014/7f46a0a4.png)

考虑到磁盘IO是非常高昂的操作，计算机操作系统做了一些优化，当一次IO时，不光把当前磁盘地址的数据，而是把相邻的数据也都读取到内存缓冲区内，因为局部预读性原理告诉我们，当计算机访问一个地址的数据的时候，与其相邻的数据也会很快被访问到。每一次IO读取的数据我们称之为一页(page)。具体一页有多大数据跟操作系统有关，一般为4k或8k，也就是我们读取一页内的数据时候，实际上才发生了一次IO，这个理论对于索引的数据结构设计非常有帮助。

#### 操作系统中的B+树

`页`是计算机管理存储器的逻辑块，硬件及操作系统往往将主存和磁盘存储区分割为连续的大小相等的块，每个存储块称为一页(许多操作系统的页默认大小为`4KB`)，主存和磁盘以页为单位交换数据。当程序要读取的数据不在主存中时，会触发一个缺页异常，此时操作系统会向磁盘发出读盘信号，磁盘会找到数据的起始位置并向后连续读取一页或几页载入内存中，然后异常返回，程序继续运行。(如下命令可以查看操作系统的默认页大小)

```sh
Copy$ getconf PAGE_SIZE
4096
```

数据库系统的设计者巧妙利用了磁盘预读原理，将一个节点的大小设为操作系统的页大小的整数倍，这样每个节点只需要一次I/O就可以完全载入。

`InnoDB`存储引擎中也有页(`Page`)的概念，页是其磁盘管理的最小单位。InnoDB存储引擎中默认每个页的大小为16KB。

```sh
Copymysql> show variables like 'innodb_page_size';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| innodb_page_size | 16384 |
+------------------+-------+
1 row in set (0.01 sec)
```

一般表的主键类型为`INT`（占4个字节）或`BIGINT`（占8个字节），指针类型也一般为4或8个字节，也就是说一个页（B+Tree中的一个节点）中大概存储`16KB/(8B+8B)=1K`个键值（因为是估值，为方便计算，这里的K取值为`10^3`）。也就是说一个深度为3的B+Tree索引可以维护`10^3 * 10^3 * 10^3 = 10亿`条记录。

`B+Tree`的高度一般都在2到4层。mysql的InnoDB存储引擎在设计时是将根节点常驻内存的，也就是说查找某一键值的行记录时最多只需要1到3次磁盘I/O操作。

随机I/O对于MySQL的查询性能影响会非常大，而顺序读取磁盘中的数据会很快，由此我们也应该尽量减少随机I/O的次数，这样才能提高性能。在`B-Tree`中由于所有的节点都可能包含目标数据，我们总是要从根节点向下遍历子树查找满足条件的数据行，这会带来大量的随机I/O，而`B+Tree`所有的数据行都存储在叶子节点中，而这些叶子节点通过`双向链表`依次按顺序连接，当我们在B+树遍历数据(比如说`范围查询`)时可以直接在多个叶子节点之间进行跳转，保证`顺序`、`倒序`遍历的性能。

另外，对以上提到的数据结构不熟悉的朋友，这里推荐一个在线数据结构可视化演示工具，有助于快速理解这些数据结构的机制：`https://www.cs.usfca.edu/~galles/visualization/Algorithms.html`

#### b+树性质

- 通过上面的分析，我们知道IO次数取决于b+数的高度h，假设当前数据表的数据为N，每个磁盘块的数据项的数量是m，则有h=㏒(m+1)N，当数据量N一定的情况下，m越大，h越小；而m = 磁盘块的大小 / 数据项的大小，磁盘块的大小也就是一个数据页的大小，是固定的，如果数据项占的空间越小，数据项的数量越多，树的高度越低。这就是为什么每个数据项，即索引字段要尽量的小，比如int占4字节，要比bigint8字节少一半。这也是为什么b+树要求把真实的数据放到叶子节点而不是内层节点，一旦放到内层节点，磁盘块的数据项会大幅度下降，导致树增高。当数据项等于1时将会退化成线性表。

- 当b+树的数据项是复合的数据结构，比如(name,age,sex)的时候，b+数是按照从左到右的顺序来建立搜索树的，比如当(张三,20,F)这样的数据来检索的时候，b+树会优先比较name来确定下一步的所搜方向，如果name相同再依次比较age和sex，最后得到检索的数据；但当(20,F)这样的没有name的数据来的时候，b+树就不知道下一步该查哪个节点，因为建立搜索树的时候name就是第一个比较因子，必须要先根据name来搜索才能知道下一步去哪里查询。比如当(张三,F)这样的数据来检索时，b+树可以用name来指定搜索方向，但下一个字段age的缺失，所以只能把名字等于张三的数据都找到，然后再匹配性别是F的数据了， 这个是非常重要的性质，即索引的最左匹配特性。

### 索引类型

#### 主键索引

它是一种特殊的唯一索引，不允许有空值。一般是在建表的时候同时创建主键索引。



![img](https://user-gold-cdn.xitu.io/2019/1/2/1680e095d0593ef7?imageslim)



当然也可以用 ALTER 命令。记住：一个表只能有一个主键。

##### MyISAM索引实现

`MyISAM`引擎使用`B+Tree`作为索引结构时叶子节点的`data`域存放的是数据记录的地址。如下图所示：

[<img src="https://img2020.cnblogs.com/blog/1546632/202009/1546632-20200919221036646-2109444544.png" alt="MyISAM主键索引原理图" style="zoom:50%;" />

由上图可以看出：MyISAM索引文件和数据文件是分离的，索引文件仅保存数据记录的地址，因此MyISAM的索引方式也叫做`非聚集`的，之所以这么称呼是为了与InnoDB的`聚集索引`区分。

##### InnoDB索引实现

`InnoDB`的`主键索引`也使用`B+Tree`作为索引结构时的实现方式却与MyISAM截然不同。`InnoDB的数据文件本身就是索引文件`。在InnoDB中，表数据文件本身就是按`B+Tree`组织的一个索引结构，这棵树的叶子节点`data`域保存了完整的数据记录，这个索引的`key`是数据表的主键，因此InnoDB表数据文件本身就是主索引。

[<img src="https://img2020.cnblogs.com/blog/1546632/202009/1546632-20200919222451055-995072120.png" alt="InnoDB主键索引原理图" style="zoom:50%;" />

`InnoDB`存储引擎中的主键索引(`primary key`)又叫做聚集索引(`clustered index`)。因为InnoDB的数据文件本身要按主键聚集，所以InnoDB要求表必须有主键（MyISAM可以没有），如果没有显式指定，则MySQL系统会自动选择一个可以唯一标识数据记录的列作为主键，如果不存在这种列，则MySQL自动为InnoDB表生成一个隐含字段作为主键，这个字段长度为6个字节，类型为长整形。(详情见官方文档：`https://dev.mysql.com/doc/refman/5.7/en/innodb-index-types.html`)

聚集索引这种实现方式使得按主键搜索十分高效，直接能查出整行数据。

在InnoDB中，用非单调递增的字段作为主键不是个好主意，因为InnoDB数据文件本身是一棵`B+Tree`，非单增的主键会造成在插入新记录时数据文件为了`维持B+Tree的特性`而频繁的分裂调整，十分低效，因而使用`递增`字段作为主键则是一个很好的选择。

#### 非主键索引

##### 唯一索引

唯一索引列的值必须唯一，但允许有空值。如果是组合索引，则列值的组合必须唯一。可以在创建表的时候指定，也可以修改表结构，如：

ALTER TABLE `table_name` ADD UNIQUE (`column`)

##### 普通索引

这是最基本的索引，它没有任何限制。可以在创建表的时候指定，也可以修改表结构，如：

ALTER TABLE `table_name` ADD INDEX index_name (`column`)

##### MyISAM索引实现

MyISAM中，主键索引和非主键索引（`Secondary key`，也有人叫做`辅助索引`）在结构上没有任何区别，只是主键索引要求key是唯一的，而辅助索引的key可以重复。这里不再多加叙述。

##### InnoDB索引实现

InnoDB的非主键索引`data`域存储相应记录`主键的值`。换句话说，InnoDB的所有非主键索引都引用主键的值作为data域。如下图所示：

[![InnoDB非主键索引原理图](https://img2020.cnblogs.com/blog/1546632/202009/1546632-20200919225813936-1490118290.png)](https://img2020.cnblogs.com/blog/1546632/202009/1546632-20200919225813936-1490118290.png)

由上图可知：使用非主键索引搜索时需要检索两遍索引，首先检索非主键索引获得主键(`primary key`)，然后用主键到`主键索引树`中检索获得完整记录。

那么为什么非主键索引结构叶子节点存储的是主键值，而不像主键索引那样直接存储完整的一行数据，这样就能避免回表二次检索？显然，这样做一方面节省了大量的存储空间，另一方面多份冗余数据，更新数据的效率肯定低下，另外保证数据的一致性是个麻烦事。

到了这里，也很容易明白为什么`不建议使用过长的字段作为主键`，因为所有的非主键索引都引用主键值，过长的主键值会让非主键索引变得过大。

#### 组合索引

组合索引，即一个索引包含多个列。可以在创建表的时候指定，也可以修改表结构，如：

ALTER TABLE `table_name` ADD INDEX index_name(`column1`, `column2`, `column3`)

官方文档：`https://dev.mysql.com/doc/refman/5.7/en/multiple-column-indexes.html`

比如`INDEX idx_book_id_hero_name (book_id, hero_name) USING BTREE`，即对`book_id, hero_name`两列建立了一个联合索引。

> A multiple-column index can be considered a sorted array, the rows of which contain values that are created by concatenating the values of the indexed columns.

联合索引是多列按照次序一列一列比较大小，拿`idx_book_id_hero_name`这个联合索引来说，先比较`book_id`，book_id小的排在左边，book_id大的排在右边，book_id相同时再比较`hero_name`。如下图所示：

[![InnoDB联合索引原理图](https://img2020.cnblogs.com/blog/1546632/202009/1546632-20200920111026527-1672463564.png)](https://img2020.cnblogs.com/blog/1546632/202009/1546632-20200920111026527-1672463564.png)

了解了联合索引的结构，就能引入`最左前缀法则`：

> If the table has a multiple-column index, any leftmost prefix of the index can be used by the optimizer to look up rows. For example, if you have a three-column index on (col1, col2, col3), you have indexed search capabilities on (col1), (col1, col2), and (col1, col2, col3).

就是说联合索引中的多列是按照列的次序排列的，如果查询的时候不能满足列的次序，比如说where条件中缺少`col1 = ?`，直接就是`col2 = ? and col3 = ?`，那么就走不了联合索引，从上面联合索引的结构图应该能明显看出，只有`col2`列无法通过索引树检索符合条件的数据。

根据最左前缀法则，我们知道对`INDEX idx_book_id_hero_name (book_id, hero_name)`来说，`where book_id = ? and hero_name = ?`的查询来说，肯定可以走索引，但是如果是`where hero_name = ? and book_id = ?`呢，表面上看起来不符合最左前缀法则啊，但MySQL优化器会根据已有的索引，调整查询条件中这两列的顺序，让它符合最左前缀法则，走索引，这里也就回答了上篇《一文学会MySQL的explain工具》中为什么用`show warnings`命令查看时，`where`中的两个过滤条件`hero_name`、`book_id`先后顺序被调换了。

至于对联合索引中的列进行范围查询等各种情况，都可以先想联合索引的结构是如何创建出来的，然后看过滤条件是否满足最左前缀法则。比如说范围查询时，范围列可以用到索引（必须是最左前缀），但是范围列后面的列无法用到索引。同时，索引最多用于一个范围列，因此如果查询条件中有两个范围列则无法全用到索引。

#### 全文索引

全文索引（也称全文检索）是目前搜索引擎使用的一种关键技术。它能够利用分词技术等多种算法智能分析出文本文字中关键字词的频率及重要性，然后按照一定的算法规则智能地筛选出我们想要的搜索结果。

可以在创建表的时候指定，也可以修改表结构，如：ALTER TABLE `table_name` ADD FULLTEXT (`column`)

## MySQL事务

## MySQL锁和MVCC

## MySQL执行流程

## MySQL优化

