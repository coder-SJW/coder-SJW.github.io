---
title: Mysql事务
date: 2022-02-24 11:08:27
tags:
  - mysql事务
categories: mysql
cover: https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fpic3.zhimg.com%2Fv2-fb87e2201c287892ec1003d11d798ee9_1200x500.jpg&refer=http%3A%2F%2Fpic3.zhimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1648274533&t=0a77d901150f6bdb6e780778cde4748c
top_img: https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fpic3.zhimg.com%2Fv2-fb87e2201c287892ec1003d11d798ee9_1200x500.jpg&refer=http%3A%2F%2Fpic3.zhimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1648274533&t=0a77d901150f6bdb6e780778cde4748c
---

## 1、什么是事务？

> 在mysql中，事务是一组原子性的sql语句，或者说是一个独立的工作单元

**事务有四个特性：**

- 原子性(Atomicity)：一个事务必须被视为不可分割的最小工作单位

- 一致性(Consistency)

- 隔离型(Isolation)

- 持久性(Durability)

## 2、redo log 与 undo log

[如何查看日志](https://www.yisu.com/zixun/131492.html)

### ①redo log	

​	redo log叫做重做日志，用来实现事务的持久性。该日志文件由两部分组成：**重做日志缓冲（redo log buffer）以及重做日志文件（redo log）**,前者是在内存中，后者在磁盘中。当事务提交之后会把所有修改信息都会存到该日志中。

> redo log 有什么作用？

​		mysql 为了提升性能**不会把每次的修改都实时同步到磁盘**，而是会先存到Boffer Pool(缓冲池)里头，把这个当作缓存来用。然后**使用后台线程去做缓冲池和磁盘之间的同步**。

> 总结：redo log是用来恢复数据的，用于保障已提交事务的持久化特性。

### ②undo log

​		undo log 叫做回滚日志，用于记录数据被修改前的信息。**他正好跟前面所说的重做日志所记录的相反，重做日志记录数据被修改后的信息**。undo  log主要记录的是数据的逻辑变化，为了在发生错误时回滚之前的操作，需要将之前的操作都记录下来，然后在发生错误时才可以回滚。

> undo log 有什么作用？

​		**undo log 记录事务修改之前版本的数据信息**，因此假如由于系统错误或者rollback操作而回滚的话可以根据undo log的信息来进行回滚到没被修改前的状态。

> 总结：undo log是用来回滚数据的，用于保障未提交事务的原子性



## 3、事务的实现

### ①原子性的实现

> undo log 的生成

每条数据变更(insert/update/delete)操作都**伴随一条undo log的生成**,并且**回滚日志必须先于数据持久化到磁盘上**

所谓的回滚就是根据回滚日志做逆向操作，比如delete的逆向操作为insert，insert的逆向操作为delete，update的逆向为update等。

根据undo log 进行回滚回滚操作就是要还原到原来的状态，undo log记录了数据被修改前的信息以及新增和被删除的数据信息，根据undo log生成回滚语句，比如：

(1) 如果在回滚日志里有新增数据记录，则生成删除该条的语句

(2) 如果在回滚日志里有删除数据记录，则生成生成该条的语句

(3) 如果在回滚日志里有修改数据记录，则生成修改到原先数据的语句

### ②持久性的实现

事务一旦提交，其所作做的修改会永久保存到数据库中，**此时即使系统崩溃修改的数据也不会丢失**。

MySQL的表数据是存放在磁盘上的，因此想要存取的时候都要经历磁盘IO,然而即使是使用SSD磁盘IO也是非常消耗性能的。为此，为了提升性能InnoDB提供了缓冲池(Buffer Pool)，Buffer Pool中包含了磁盘数据页的映射，可以当做缓存来使用：

读数据：会首先从缓冲池中读取，如果缓冲池中没有，则从磁盘读取在放入缓冲池；

写数据：会首先写入缓冲池，缓冲池中的数据会定期同步到磁盘中

### ③隔离性的实现(相对复杂)

> 隔离性是事务ACID特性里最复杂的一个

​		在SQL标准里定义了四种隔离级别，每一种级别都规定一个事务中的修改，哪些是事务之间可见的，哪些是不可见的。级别越低的隔离级别可以执行越高的并发，但同时实现复杂度以及开销也越大。

Mysql 隔离级别有以下四种(低-->高）：未提交读（Read uncommitted）、已提交读（Read committed）、可重复读（Repeatable read）、串行化（[Serializable](https://mp.weixin.qq.com/s?__biz=MzAxOTc0NzExNg%3D%3D&chksm=80d66e7ab7a1e76c8f8b7be95b8617aba9c4d76c6f95fbedd8d222f6c8912d49686902af7bca&idx=1&mid=2665518649&scene=21&sn=1c2fa1986acf7b8ee0e5a14fbb3da9ae#wechat_redirect) ）

![image-20220224135105016](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220224135105016.png)

​		只要彻底理解了隔离级别以及他的实现原理就相当于理解了ACID里的隔离型。**前面说过原子性，隔离性，持久性的目的都是为了要做到一致性**，但隔离性跟其他两个有所区别，原子性和持久性是为了要实现数据的可性保障靠，比如要做到宕机后的恢复，以及错误后的回滚。

> 如果不考虑隔离性，会发生什么事呢？

> 1、脏读

是指一个事务在处理数据的过程中，读取到另一个为提交事务的数据。

```sql
--原数据
--id    name
--1     lisi
 
--事务1
START TRANSACTION；
updata t_table set name = 'wangwu' where id = 1;    --此时事务2查询id = 1
ROLLBACK；
 
--事务2
select * from t_table where id = 1;        --查询到 id = 1， name = 'wangwu'
```

​		事务1并没有提交,开始只会写入缓冲池，未持久化到磁盘，name 还是 lisi，但是事务2却读到了 name = wangwu，这就是脏读。如果换成A给B转账，B查询到了没有提交的事务，认为已经收到A转过来的钱，那岂不是很恐怖。

> 2、不可重复读

​		不可重复读是指对于数据库中的某个数据，一个事务范围内的多次查询却返回了不同的结果，这是由于在查询过程中，数据被另外一个事务修改并提交了。

```sql
--原数据
--id    name
--1     lisi
 
--事务1
select * from t_table where id = 1;    -- 查询到 id = 1, name = list, 事务2在此时提交
select * from t_table where id = 1;    -- 查询到 id = 1, name = wangwu
 
--事务2
start transaction;
update t_table set name = 'wangwu' where id = 1;
COMMIT;
```

不可重复读和脏读的区别是:

- 脏读读取到的是一个**未提交的数据**

- 不可重复读读取到的是前一个事务**提交的数据**

> 3、幻读

​		幻读是事务非独立执行时发生的一种现象

​		例如事务1对一个表中所有的行的某个数据项做了从“1”修改为“2”的操作，这时事务2又对这个表中插入了一行数据项，而这个数据项的数值还是为“1”并且提交给数据库。而操作事务1的用户如果再查看刚刚修改的数据，会发现还有一行没有修改，其实这行是从事务2中添加的，就好像产生幻觉一样，这就是发生了幻读。

​		幻读和不可重复读都是读取了另一条已经提交的事务（这点就脏读不同），所不同的是不可重复读查询的都是同一个数据项，而幻读针对的是一批数据整体（比如数据的个数）。

```sql
--原数据
--id    name
--1     lisi
 
--事务1
select * from t_table where id = 2;    --返回NULL,此时事务2提交
select * from t_table where id = 2;    --返回id = 2, name = wangwu
 
--事务2
insert into t_table values(2,'wangwu');
COMMIT;
```

​		不可重复读和幻读是初学者不易分清的概念，我也是看了详细的解读才明白的，总的来说，解决不可重复读的方法是 **锁行**，解决幻读的方式是 **锁表**。



> 查询和设置数据库的隔离级别

```sql
select @@tx_isolation;
+-----------------+
| @@tx_isolation  |
+-----------------+
| REPEATABLE-READ |
+-----------------+

set tx_isolation='隔离级别';    --read-uncommitted    read-committed    repeatable-read    serializable
set tx_isolation='read-uncommitted';
 
select @@tx_isolation;
+------------------+
| @@tx_isolation   |
+------------------+
| READ-UNCOMMITTED |
+------------------+
```

