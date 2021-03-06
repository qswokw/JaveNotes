# 1.数据库操作

 https://blog.csdn.net/liuying8448/article/details/7427716 

## Mysql 的存储引擎,myisam和innodb的区别。

答：

1.MyISAM 是非事务的存储引擎，适合用于频繁查询的应用。表锁，不会出现死锁，适合小数据，小并发。

2.innodb是支持事务的存储引擎，合于插入和更新操作比较多的应用，设计合理的话是行锁（最大区别就在锁的级别上），适合大数据，大并发。

**(1)、问5点不同；**

- 1>.InnoDB支持事物，而MyISAM不支持事物
- 2>.InnoDB支持行级锁，而MyISAM支持表级锁
- 3>.InnoDB支持MVCC, 而MyISAM不支持
- 4>.InnoDB支持外键，而MyISAM不支持
- 5>.InnoDB不支持全文索引，而MyISAM支持。

**(2)、innodb引擎的4大特性**

插入缓冲（insert buffer),二次写(double write),自适应哈希索引(ahi),预读(read ahead)

**(3)、2者selectcount(*)哪个更快，为什么**

myisam更快，因为myisam内部维护了一个计数器，可以直接调取。


作者：Java_老男孩链接：https://juejin.im/post/5cb6c4ef51882532b70e6ff0来源：掘金著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



## 数据表类型有哪些

​    答：MyISAM、InnoDB、HEAP、BOB,ARCHIVE,CSV等。
​    MyISAM：成熟、稳定、易于管理，快速读取。一些功能不支持（事务等），表级锁。
​    InnoDB：支持事务、外键等特性、数据行锁定。空间占用大，不支持全文索引等。

## 问了innodb的事务与日志的实现方式

**(1)、有多少种日志；**

**错误日志**：记录出错信息，也记录一些警告信息或者正确的信息。

**查询日志**：记录所有对数据库请求的信息，不论这些请求是否得到了正确的执行。

**慢查询日志**：设置一个阈值，将运行时间超过该值的所有SQL语句都记录到慢查询的日志文件中。

**二进制日志**：记录对数据库执行更改的所有操作。

**中继日志**：中继日志也是二进制日志，用来给slave 库恢复

**事务日志**：重做日志redo和回滚日志undo

**(2)、事物的4种隔离级别**

**隔离级别**

- 读未提交(RU)
- 读已提交(RC)
- 可重复读(RR)
- 串行

**(3)、事务是如何通过日志来实现的，说得越深入越好。**

事务日志是通过redo和innodb的存储引擎日志缓冲（Innodb log buffer）来实现的，当开始一个事务的时候，会记录该事务的lsn(log sequence number)号; 当事务执行时，会往InnoDB存储引擎的日志的日志缓存里面插入事务日志；当事务提交时，必须将存储引擎的日志缓冲写入磁盘（通过innodb_flush_log_at_trx_commit来控制），也就是写数据前，需要先写日志。这种方式称为“预写日志方式”








## MySQL数据库作发布系统的存储，一天五万条以上的增量，预计运维三年,怎么优化？

a. 设计良好的数据库结构，允许部分数据冗余，尽量避免join查询，提高效率。
b. 选择合适的表字段数据类型和存储引擎，适当的添加索引。
c. mysql库主从读写分离。
d. 找规律分表，减少单表中的数据量提高查询速度。
e。添加缓存机制，比如memcached，apc等。
f. 不经常改动的页面，生成静态页面。
g. 书写高效率的SQL。比如 SELECT * FROM TABEL 改为 SELECT field_1, field_2, field_3 FROM TABLE.

## 如何进行SQL优化？

 **（1）选择正确的存储引擎** 

 **（2）优化字段的数据类型** 

 使用 MEDIUMINT, SMALLINT 或是更小的 TINYINT 会更经济一些。如果你不需要记录时间，使用 DATE 要比 DATETIME 好得多。 

 **（3）为搜索字段添加索引** 

 某个字段你总要会经常用来做搜索，那么最好是为其建立索引 

 **(4)使用 ENUM 而不是 VARCHAR** 

 ENUM 类型是非常快和紧凑的。在实际上，其保存的是 TINYINT，但其外表上显示为字符串。这样一来，用这个字段来做一些选项列表变得相当的完美。例如，性别、民族、部门和状态之类的这些字段的取值是有限而且固定的，那么，你应该使用 ENUM 而不是 VARCHAR。 

 **(5)尽可能的使用 NOT NULL** 

 **(6)固定长度的表会更快** 

 固定长度的表会提高性能，因为MySQL搜寻得会更快一些，因为这些固定的长度是很容易计算下一个数据的偏移量的，所以读取的自然也会很快。而如果字段不是定长的，那么，每一次要找下一条的话，需要程序找到主键。 

并且，固定长度的表也更容易被缓存和重建。不过，唯一的副作用是，固定长度的字段会浪费一些空间，因为定长的字段无论你用不用，他都是要分配那么多的空间。



常用命令

**(1)、explain**

查询索引相关信息

**(2)、profile**

 查询到 SQL 会执行多少时间, 并看出 CPU/Memory 使用量, 执行过程中 Systemlock, Table lock 花多少时间等等 

##  如何设计一个高并发的系统

① 数据库的优化，包括合理的事务隔离级别、SQL语句优化、索引的优化

② 使用缓存，尽量减少数据库 IO

③ 分布式数据库、分布式缓存

④ 服务器的负载均衡

## 锁的优化策略

① 读写分离

② 分段加锁

③ 减少锁持有的时间

④ 多个线程尽量以相同的顺序去获取资源

等等，这些都不是绝对原则，都要根据情况，比如不能将锁的粒度过于细化，不然可能会出现线程的加锁和释放次数过多，反而效率不如一次加一把大锁。

## 索引的底层实现原理和优化

B+树，经过优化的B+树

主要是在所有的叶子结点中增加了指向下一个叶子节点的指针，因此InnoDB建议为大部分表使用默认自增的主键作为主索引。

## 什么情况下设置了索引但无法使用 

① 以“%”开头的LIKE语句，模糊匹配

② OR语句前后没有同时使用索引

③ 数据类型出现隐式转化（如varchar不加单引号的话可能会自动转换为int型）

## SQL注入漏洞产生的原因？如何防止？

SQL注入产生的原因：程序开发过程中不注意规范书写sql语句和对特殊字符进行过滤，导致客户端可以通过全局变量POST和GET提交一些sql语句正常执行。

防止SQL注入的方式：
开启配置文件中的magic_quotes_gpc 和 magic_quotes_runtime设置

执行sql语句时使用addslashes进行sql语句转换

Sql语句书写尽量不要省略双引号和单引号。

过滤掉sql语句中的一些关键词：update、insert、delete、select、 * 。

提高数据库表和字段的命名技巧，对一些重要的字段根据程序的特点命名，取不易被猜到的。

Php配置文件中设置register_globals为off,关闭全局变量注册

控制错误信息，不要在浏览器上输出错误信息，将错误信息写到日志文件中。

## 完整性约束包括哪些？


答：数据完整性(Data Integrity)是指数据的精确(Accuracy)和可靠性(Reliability)。

分为以下四类：

1) 实体完整性：规定表的每一行在表中是惟一的实体。

2) 域完整性：是指表中的列必须满足某种特定的数据类型约束，其中约束又包括取值范围、精度等规定。

3) 参照完整性：是指两个表的主关键字和外关键字的数据应一致，保证了表之间的数据的一致性，防止了数据丢失或无意义的数据在数据库中扩散。

4) 用户定义的完整性：不同的关系数据库系统根据其应用环境的不同，往往还需要一些特殊的约束条件。用户定义的完整性即是针对某个特定关系数据库的约束条件，它反映某一具体应用必须满足的语义要求。

与表有关的约束：包括列约束(NOT NULL（非空约束）)和表约束(PRIMARY KEY、foreign key、check、UNIQUE)

## 什么是存储过程？用什么来调用？


答：存储过程是一个预编译的SQL语句，优点是允许模块化的设计，就是说只需创建一次，以后在该程序中就可以调用多次。如果某次操作需要执行多次SQL，使用存储过程比单纯SQL语句执行要快。可以用一个命令对象来调用存储过程。

## 如何通俗地理解三个范式？ 


答：第一范式：1NF是对属性的原子性约束，要求属性具有原子性，不可再分解；

第二范式：2NF是对记录的惟一性约束，要求记录有惟一标识，即实体的惟一性； 

第三范式：3NF是对字段冗余性的约束，即任何字段不能由其他字段派生出来，它要求字段没有冗余。。

范式化设计优缺点:

优点:

可以尽量得减少数据冗余，使得更新快，体积小

缺点:对于查询需要多个表进行关联，减少写得效率增加读得效率，更难进行索引优化

反范式化:

优点:可以减少表得关联，可以更好得进行索引优化

缺点:数据冗余以及数据异常，数据得修改需要更多的成本

## 什么是基本表？什么是视图？


答：基本表是本身独立存在的表，在 SQL 中一个关系就对应一个表。 视图是从一个或几个基本表导出的表。视图本身不独立存储在数据库中，是一个虚表 

**试述视图的优点？**


答：(1) 视图能够简化用户的操作 (2) 视图使用户能以多种角度看待同一数据； (3) 视图为数据库提供了一定程度的逻辑独立性； (4) 视图能够对机密数据提供安全保护。

## char和varchar的区别？


答：是一种固定长度的类型，varchar则是一种可变长度的类型，它们的区别是： 

char(M)类型的数据列里，每个值都占用M个字节，如果某个长度小于M，MySQL就会在它的右边用空格字符补足．（在检索操作中那些填补出来的空格字符将被去掉）在varchar(M)类型的数据列里，每个值只占用刚好够用的字节再加上一个用来记录其长度的字节（即总长度为L+1字节）． 

varchar得适用场景:

字符串列得最大长度比平均长度大很多 2.字符串很少被更新，容易产生存储碎片 3.使用多字节字符集存储字符串

Char得场景:

  存储具有近似得长度（md5值,身份证，手机号）,长度比较短小得字符串（因为varchar需要额外空间记录字符串长度），更适合经常更新得字符串，更新时不会出现页分裂得情况，避免出现存储碎片，获得更好的io性能

> **如何区分FLOAT和DOUBLE？**

以下是FLOAT和DOUBLE的区别：

- 浮点数以8位精度存储在FLOAT中，并且有四个字节。
- 浮点数存储在DOUBLE中，精度为18位，有八个字节。

> **在Mysql中ENUM的用法是什么？**

ENUM是一个字符串对象，用于指定一组预定义的值，并可在创建表时使用。

Create table size(name ENUM('Smail,'Medium','Large');

> **TIMESTAMP在UPDATE CURRENT_TIMESTAMP数据类型上做什么？**

创建表时TIMESTAMP列用Zero更新。只要表中的其他字段发生更改，UPDATE CURRENT_TIMESTAMP修饰符就将时间戳字段更新为当前时间。

​    `update_time` timestamp not null default current_timestamp on update current_timestamp comment '修改时间', 

 当执行update操作是，并且字段有ON UPDATE CURRENT_TIMESTAMP属性。则字段无论值有没有变化，它的值也会跟着更新为当前UPDATE操作时的时间。 

## MySQL的复制原理以及流程

基本原理流程，3个线程以及之间的关联；

**主**：binlog线程——记录下所有改变了数据库数据的语句，放进master上的binlog中；

**从**：io线程——在使用start slave 之后，负责从master上拉取 binlog 内容，放进 自己的relay log中；

**从**：sql执行线程——执行relay log中的语句；




## mysql的varchar(N)和int(N)的含义及其与char区别

 **1）varchar与char的区别**
Varchar存储可变长字符串，小于255字节时需要1个额外字节(大于255需要2个额外字节)存储长度，最大长度为65532字节(所有列总和)；
char存储定长(right padding)，读取时会截断末尾空格，长度最大为255字符；

**2）varchar(30)中30的涵义**
最大存储30个字符；varchar(5)和(200)存储hello所占空间一样，但后者在排序时会消耗更多内存，因为order by col采用fixed_length计算col长度(memory引擎也一样) 

 **3）int（20）中20的涵义**
20表示最大显示宽度为20，但仍占4字节存储，存储范围不变；
create table int_test(a int zerofill NOT NULL auto_increment, PRIMARY KEY (a));
create table int_test_4(a int(4) zerofill NOT NULL auto_increment, PRIMARY KEY (a));

select * from int_test;
+------------+
| a      |
+------------+
| 0000000001 |
| 0000000002 |
| 0000000003 |
| 2147483648 |
+------------+

select * from int_test_4;
+------------+
| a      |
+------------+
|    0001 |
|    0002 |
|    0003 |
| 2147483648 |
+------------+

**4）为什么MySQL这样设计？**
对大多数应用没有意义，只是规定一些工具用来显示字符的个数；int(1)和int(20)存储和计算均一样； 

 **请写出下面MySQL数据类型表达的意义（int(0)、char(16)、varchar(16)、datetime、text）** 

## MySQL binlog的几种日志录入格式以及区别

**Statement**：每一条会修改数据的sql都会记录在binlog中。

**优点**：不需要记录每一行的变化，减少了binlog日志量，节约了IO，提高性能。

**缺点**：由于记录的只是执行语句，为了这些语句能在slave上正确运行，因此还必须记录每条语句在执行的时候的 一些相关信息，以保证所有语句能在slave得到和在master端执行时候相同 的结果。另外mysql 的复制,像一些特定函数功能，slave可与master上要保持一致会有很多相关问题(如sleep()函数， last_insert_id()，以及user-defined functions(udf)会出现问题).

**Row**:不记录sql语句上下文相关信息，仅保存哪条记录被修改。

**优点**： binlog中可以不记录执行的sql语句的上下文相关的信息，仅需要记录那一条记录被修改成什么了。

**缺点**:所有的执行的语句当记录到日志中的时候，都将以每行记录的修改来记录，这样可能会产生大量的日志内容,比 如一条update语句，修改多条记录，则binlog中每一条修改都会有记录，这样造成binlog日志量会很大，特别是当执行alter table之类的语句的时候，由于表结构修改，每条记录都发生改变，那么该表每一条记录都会记录到日志中。

**Mixedlevel**: 是以上两种level的混合使用，一般的语句修改使用statment格式保存binlog，如一些函数，statement无法完成主从复制的操作，则 采用row格式保存binlog,MySQL会根据执行的每一条具体的sql语句来区分对待记录的日志形式，也就是在Statement和Row之间选择 一种.

## MySQL数据库cpu飙升到500%的话他怎么处理？

- 1、列出所有进程 show processlist,观察所有进程 ,多秒没有状态变化的(干掉)
- 2、查看超时日志或者错误日志 (做了几年开发,一般会是查询以及大批量的插入会导致cpu与i/o上涨,当然不排除网络状态突然断了,导致一个请求服务器只接受到一半，比如where子句或分页子句没有发送,,当然的一次被坑经历)

## 表中有大字段X(例如：text类型)，且字段X不会经常更新，以读为为主，请问

拆带来的问题：连接消耗 + 存储拆分空间；不拆可能带来的问题：查询性能；

## MySQL中InnoDB引擎的行锁是通过加在什么上完成(或称实现)的？为什么是这样子的？

InnoDB是基于索引来完成行锁

例: select * from tab_with_index where id = 1 for update;

for update 可以根据条件来完成行锁锁定,并且 id 是有索引键的列,

如果 id 不是索引键那么InnoDB将完成表锁,,并发将无从谈起

## 开放性问题：据说是腾讯的

一个6亿的表a，一个3亿的表b，通过外间tid关联，你如何最快的查询出满足条件的第50000到第50200中的这200条数据记录。

- 1、如果A表TID是自增长,并且是连续的,B表的ID为索引

```
select * from a,b where a.tid = b.id and a.tid>500000 limit 200;
复制代码
```

- 2、如果A表的TID不是连续的,那么就需要使用覆盖索引.TID要么是主键,要么是辅助索引,B表ID也需要有索引。

```
select * from b , (select tid from a limit 50000,200) a where b.id = a .tid;
```

## 什么是存储过程？有哪些优缺点？

存储过程是一些预编译的SQL语句。

- 1、更加直白的理解：存储过程可以说是一个记录集，它是由一些T-SQL语句组成的代码块，这些T-SQL语句代码像一个方法一样实现一些功能（对单表或多表的增删改查），然后再给这个代码块取一个名字，在用到这个功能的时候调用他就行了。
- 2、存储过程是一个预编译的代码块，执行效率比较高,一个存储过程替代大量T_SQL语句 ，可以降低网络通信量，提高通信速率,可以一定程度上确保数据安全

## 简单说一说drop、delete与truncate的区

SQL中的drop、delete、truncate都表示删除，但是三者有一些差别

- 1、delete和truncate只删除表的数据不删除表的结构
- 2、速度,一般来说: drop> truncate >delete
- 3、delete语句是dml,这个操作会放到rollback segement中,事务提交之后才生效;
- 4、如果有相应的trigger,执行的时候将被触发. truncate,drop是ddl, 操作立即生效,原数据不放到rollback segment中,不能回滚. 操作不触发trigger.

**drop、delete与truncate分别在什么场景之下使用？**

- 1、不再需要一张表的时候，用drop
- 2、想删除部分数据行时候，用delete，并且带上where子句
- 3、保留表而删除所有数据的时候用truncate

## 说一说三个范式。

- 第一范式（1NF）：数据库表中的字段都是单一属性的，不可再分。这个单一属性由基本类型构成，包括整型、实数、字符型、逻辑型、日期型等。
- 第二范式（2NF）：数据库表中不存在非关键字段对任一候选关键字段的部分函数依赖（部分函数依赖指的是存在组合关键字中的某些字段决定非关键字段的情况），也即所有非关键字段都完全依赖于任意一组候选关键字。
- 第三范式（3NF）：在第二范式的基础上，数据表中如果不存在非关键字段对任一候选关键字段的传递函数依赖则符合第三范式。所谓传递函数依赖，指的是如 果存在"A → B → C"的决定关系，则C传递函数依赖于A。因此，满足第三范式的数据库表应该不存在如下依赖关系： 关键字段 → 非关键字段 x → 非关键字段y

### 数据库的乐观锁和悲观锁是什么？

数据库管理系统（DBMS）中的并发控制的任务是确保在多个事务同时存取数据库中同一数据时不破坏事务的隔离性和统一性以及数据库的统一性。乐观并发控制(乐观锁)和悲观并发控制（悲观锁）是并发控制主要采用的技术手段。

**悲观锁**：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作

**乐观锁**：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。

## **什么是触发器，MySQL中都有哪些触发器？**

触发器是指一段代码，当触发某个事件时，自动执行这些代码。在MySQL数据库中有如下六种触发器：

- 1、Before Insert
- 2、After Insert
- 3、Before Update
- 4、After Update
- 5、Before Delete
- 6、After Delete

## **请说明varchar和text的区别**

- varchar可指定字符数，text不能指定，内部存储varchar是存入的实际字符数+1个字节（n<=255）或2个字节(n>255)，text是实际字符数+2个字节。
- text类型不能有默认值。
- varchar可直接创建索引，text创建索引要指定前多少个字符。varchar查询速度快于text,在都创建索引的情况下，text的索引几乎不起作用。
- 查询text需要创建临时表。

## **索引对性能的影响：**

- 大大减少服务器需要扫描的数据量。
- 帮助服务器避免排序和临时表。
- 将随机I/O变顺序I/O。
- 大大提高查询速度。
- 降低写的速度（不良影响）。
- 磁盘占用（不良影响）。

## **MySQL索引的注意事项**

1、联合索引遵循前缀原则

```
KEY(a,b,c)WHERE a = 1 AND b = 2 AND c = 3WHERE a = 1 AND b = 2WHERE a = 1#以上SQL语句可以用到索引WHERE b = 2 AND c = 3WHERE a = 1 AND c = 3#以上SQL语句用不到索引
```

2、LIKE查询，%不能在前

```
WHERE name LIKE "%wang%"#以上语句用不到索引，可以用外部的ElasticSearch、Lucene等全文搜索引擎替代。
```

3、列值为空（NULL）时是可以使用索引的，但MySQL难以优化引用了可空列的查询,它会使索引、索引统计和值更加复杂。可空列需要更多的储存空间，还需要在MySQL内部进行特殊处理。

4、如果MySQL估计使用索引比全表扫描更慢，会放弃使用索引，例如：表中只有100条数据左右。对于SQL语句WHERE id > 1 AND id < 100，MySQL会优先考虑全表扫描。

5、如果关键词or前面的条件中的列有索引，后面的没有，所有列的索引都不会被用到。

6、列类型是字符串，查询时一定要给值加引号，否则索引失效，例如：列name varchar(16)，存储了字符串"100"WHERE name = 100;以上SQL语句能搜到，但无法用到索引。

**总结：不能让索引范围太大，不能让索引参与运算，最左匹配原则**

## **列值为NULL时，查询是否会用到索引？**

在MySQL里NULL值的列也是走索引的。当然，如果计划对列进行索引，就要尽量避免把它设置为可空，MySQL难以优化引用了可空列的查询,它会使索引、索引统计和值更加复杂。

##  一张表里ID是自增主键，当insert17条记录后，删除了第15、16、17条记录，在重启mysql，再插入一条记录，这条记录的ID是多少？ 

 如果时myisam是18，因为myism表会把自增数据最大id记录到数据文件里，重启mysql自增主键的最大ID也不会丢失。

如果是innodb,是15.InnoDB只是把自增主键最大记录到内存中，所以重启数据库或是对表进行OPTIMZE操作都会导致最大ID丢失

