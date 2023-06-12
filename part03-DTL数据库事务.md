---
title: part03_DTL数据库事务
date: 2021-08-31 00:10:34
categories: mysql
tags: mysql
---

# DTL

> Data Transaction Language - 数据事务语言
>
> commit,begin,start transction,savepoint,rollback

# 事务

> 只要使用客户端连接到mysql-server[mysql服务器],就会自动打开一个事务

在MYSQL中,***所有的DML(insert,update,delete)操作***在执行之后都会有一个**commit操作**[隐式的]

**commit - 提交事务**

在ORACLE中,所有的DML(insert,update,delete)操作之后,如果没有手动commit,那么在另外一个事务中是读不到最新的更新数据的.

**DTL,数据事务语言**

**事务的定义:**

就是指**一组相关的SQL操作**,我们所有的操作都是处在事务中的.

执行业务的基本单位是 - 不是sql,而是事务.

```java
java 代码 - 转账

  
public void transfer(int srcid,int targetid,double money){
  //设置不要自动提交模式
  conn.setAutoCommit(false);
  Transaction tx = conn.beginTransaction();//开启一个事务
  try{
    //事务保证原子性  - 要么同时成功,要么同时失败.
    String sql = "update xx set balance = balance-"+money+"where id="+srcid;
    //TODO... 执行sql,默认会自动提交事务的 -commit;
    
    //一旦是手动提交事务,那么上面的update操作仅仅是内存中的操作.尚未提交到db中.

    //其他代码... 出现了异常  , 手动提交 - 进入到catch

    String sql2 = "update xx set balance = balance+"+money+"where id="+targetid;
    //TODO ... 执行sql默认会自动提交事务的 -commit;
    
    tx.commit();
  }catch (Exception e){
    //..
    tx.rollback();//进行一个事务的回滚操作.
  }
  
}
```



控制事务 - 就是控制事务的边界(事务的开始(begin) - 事务的结束(commit,rollback))

***一个事务的结束,代表着下一个事务的开始.***

**注意:**

1. 在数据库中**,执行业务的基本单位是事务,不是以某一条SQL**

2. 数据库在默认情况下,事务都是打开的,也就是说它是一直

3. 处在事务中的,一个事务的结束,代表着下一个事务的开启

4. 执行commit或者rollback指令时,**会结束当前事务**

   **[已经提交的事务将不能够被回滚 - 已经不处于同一个事务中了,而事务与事务之间是隔离的]**

**作用:用来保证数据的平稳性和可预测性.**



# **事务的四大特性(ACID):** - 笔试

1. **Atomicity,原子性**,事务是不可再分割的,要么同时成功,要么同时失败.

   转账业务(账户+money,账户-money - 一组sql - 至于一个事务当中,并且要保证原子性.)

2. **Consistency,一致性**,**事务一旦结束,内存中的数据和数据库中的数据是保持一致的**

   example：A用户和B用户进行转账1000元，A账户减少1000和B账户增加1000的sql语句必须全部执行成功，不存在一半成功，一半失败。
   但是无论如何执行，两个账户金额的改变加起来一定1000元，**这就是一致性(数据的完整性)。**

3. **Isolation,隔离性**,事务之间互不干扰,一多个用户并发访问数据库，比如同时操作一张表。数据库为每一个用户开启事务。**每个事务相互独立，互不干扰。**

4. **Durability,持久性**,事务一旦提交,则内存中的数据持久化[保存,序列化]到数据库中,**永久保存**

   [涉及到数据库的备份和恢复的一些技术,冷备份,热备份...]

   DB - sqlData数据文件以及log目录[日志文件 - 记录了用户的sql操作]

## 事务控制语句

DTL - 事务控制语言.

1. **BEGIN或START TRANSACTION**；显式地开启一个事务；
2. **COMMIT；**也可以使用COMMIT WORK，不过二者是等价的。COMMIT会提交事务，并使已对数据库进行的所有修改成为永久性的；
3. **ROLLBACK；**有可以使用ROLLBACK WORK，不过二者是等价的。回滚会结束用户的事务，并撤销正在进行的所有未提交的修改；
4. SAVEPOINT identifier；SAVEPOINT允许在事务中创建一个保存点，一个事务中可以有多个SAVEPOINT；
5. RELEASE SAVEPOINT identifier；删除一个事务的保存点，当没有指定的保存点时，执行该语句会抛出一个异常；
6. ROLLBACK TO identifier；把事务回滚到标记点；
7. SET TRANSACTION；用来设置事务的隔离级别。InnoDB存储引擎提供事务的隔离级别有***READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ和SERIALIZABLE。***

### rollback - demo - 回滚所有

> 前提 - 设置成手动提交事务
>
> `注意:1. 如果没有设置事务点 - rollabck撤销所有的dml操作的`
>
> ![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/rollback.png) 

> `注意的是rollback只能撤销/回滚尚未提交的事务.如果一个事务已经提交了,那么是回滚不了的`
>
> ![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/r2.png) 



### 设置事务点

> 命令 - savepoint 事务点名称;
>
> rollback to 事务点名称.
>
> `效果就是会撤销到设置事务点下方的dml操作.事务点之前的dml操作将不会撤销`
>
> ![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/r3.png) 



## MYSQL 事务处理主要有两种方法

## 自动开始事务

> 1. 当我们mysql客户端连接到mysql服务器 - 自动开启
> 2. 当执行完sql语句之后,自动会commit[事务的提交 - 意味着当前事务已经结束]
> 3. 一个事务的结束意味着下一个事务的开始.
> 4. **commit/rollback - 结束事务.**

## 手动开启事务

> 执行dml操作之后,就不会自动提交事务[commit],需要手动commit

> 1. 用 BEGIN, ROLLBACK, COMMIT来实现
>
> 1-1. **BEGIN 开始一个事务**
>
> 1-2. **start transaction - 手动开始一个事务**
>
> 2. ROLLBACK 事务回滚
> 3. COMMIT 事务确认

### demo01

> A事务 - 执行了delete操作,但是还没有commit.
>
> 但是在A事务当中,查询的时候,是可以查询到delete的效果的
>
> mysql>begin;
>
> ![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/tx01.png) 

> 但是在另外一个事务B中,是看不到A事务中尚未提交的数据的[仍然是可以看到id=2这条数据的]
>
> ![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/tx02.png) 

`对A事务进行一个commit操作`

```mysql
mysql> commit;
Query OK, 0 rows affected (0.01 sec)
```

对于B事务来讲,分俩种情况.第一种情况是B一开始的事务还未提交,看到的效果是

```mysql
mysql> select * from video_copy;
+----+--------------+-------+---------------------+-------------+
| id | title        | likes | create_time         | tbl_user_id |
+----+--------------+-------+---------------------+-------------+
|  1 | java无敌     |   200 | 2021-08-19 13:38:38 |           1 |
|  2 | python不错   |   200 | 2021-08-19 13:39:45 |           1 |
+----+--------------+-------+---------------------+-------------+
2 rows in set (0.00 sec)
```

`B事务提交,也结束了当前的事务,自动开启一个新的事务,在新的事务中重新再查一次`,可以看到A事务提交的结果了.

```mysql
mysql> commit;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from video_copy;
+----+------------+-------+---------------------+-------------+
| id | title      | likes | create_time         | tbl_user_id |
+----+------------+-------+---------------------+-------------+
|  1 | java无敌   |   200 | 2021-08-19 13:38:38 |           1 |
+----+------------+-------+---------------------+-------------+
1 row in set (0.00 sec)   
```

2. 直接用 SET 来改变 MySQL 的自动提交模式: 

   2-1. **SET AUTOCOMMIT=0 禁止自动提交**

   2-2. SET AUTOCOMMIT=1 开启自动提交

   注意:设置成禁止自动提交的操作的话,这个操作仅仅是针对当前的会话窗口有效.



# 多事务的并发处理机制

原因:多个事务同时操作一个表中的**同一行数据(争抢临界资源)**,如果这些操作是.修改操作的话,就会产生并发问题,如果不处理,则会造成数据不一致的情况.

数据库可能产生的并发问题包括:

> 1. `脏读`
>
> 简介:一个事务读取到了另外一个事务中尚未提交的数据.
>
> > 是指一个事务正在访问数据,并且对这个数据进行修改,而这种修改
> > 还没有提交到数据库中,而另一个事务也访问了这个数据,并且使用了这个数据
> > 解决方法:一个事务在修改数据时,该数据不能被其他事务访问
> >
> > ```mysql
> > 所有的数据库都是拒绝脏读 - "会读到脏数据".
> > ```

> 2. `不可重复读`
>
> > 是指一个事务多次读取同一条记录,如果此时另一个事务也访问并且
> > 修改了该数据,则就会出现多次读取出现数据不一致的情况,原来的
> > 数据变成了不可重复读取的数据
> > 解决方法:只有在修改事务完全提交过后才可以读取到数据

> 3. `幻读`
>
> > 是指一个事务修改表中的多行记录[比如查询也是可以的],但是此时另一个事务对该表格进行
> > 了**插入数据**的操作,则第一个事务会发现表格中会出现没有被修改的行
> > 就像发生了幻觉一样
> > 解决方法:在一个事务提交数据之前,其他事务不能添加数据

**不可重复读的重点是修改，同样的条件，你读取过的数据，再次读取出来发现值不一样了幻读的重点在于新增或者删除**

## 事务隔离级别

> 1. READ UNCOMMITTED
>
> > 这是事务最低的隔离级别，**它允许另外一个事务可以看到这个事务未提交的数据**。
> > 解决第一类丢失更新的问题，但是会出现脏读、不可重复读.

> 2. READ COMMITTED
>
> > 保**证一个事务修改的数据提交后才能被另外一个事务读取**，即另**外一个事务不能读取该事务未提交的数据**。
> > 解决第一类丢失更新和脏读的问题，但会出现不可重复读.

> 3. **REPEATABLE READ**
>
> **简介:mysql默认的事务的隔离级别.**
>
> > 保证一个事务相同条件下前后两次获取的数据是一致的
> > 解决第一类丢失更新，脏读、不可重复读.

> 4. SERIALIZABLE - 串行化
>
> > 事务被处理为顺序执行。解决所有问题 - 有点类似于**锁表.**
>
> > 性能及其低下.

**提醒：**
**Mysql默认的事务隔离级别为repeatable_read**

```mysql
mysql>set session transaction isolation level read uncommitted;
mysql>select @@tx_isolation;
```

## 总结

| 事务隔离级别                            | 脏读 | 不可重复读 | 幻读 |
| --------------------------------------- | ---- | ---------- | ---- |
| 读未提交(read uncommitted)              | √    | √          | √    |
| 读已提交-(read committed)  - 不可重复读 | ×    | √          | √    |
| 可重复读(repeatable read)               | ×    | ×          | √    |
| 串行化(serializable)                    | ×    | ×          | ×    |



# 事务的演示

## 准备工作

```mysql
drop table account;
create table account(
	id int(7) primary key,
  name varchar(20),
  balance double(7,2)
);
insert into account values(1,'admin',1000.00),(2,'tom',2000.00);
```

## 脏读

> 1. 设置事务的隔离级别READ UNCOMMITTED - 一个事务才可以读取到另外一个事务尚未提交的数据.
>
> ```mysql
> set session transaction isolation level read uncommitted;
> ```
>
> ![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/zd.png) 

​			`总结-一旦事务设置成了读未提交的隔离级别.产生脏读以及不可重复读的效果`

​			`不可重复读 - 在同一个事务中,多次读取到的数据不一样`

## READ COMMITTED 

> set session transaction isolation level read committed;
>
> 1. 一个事务只能读取到另外一个事务中已经提交的数据
> 2. 不会出现脏读了.但是仍然会出现**不可重复读**[一定是在同一个事务中多次读取到的结果是不一样的].

> ![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/rc.png) 

## **REPEATABLE READ**

> 可重复读 - 在同一个事务中,多次读取到的数据是一样的[不关心其他事务做了什么] - mysql的默认的隔离级别
>
> set session transaction isolation level repeatable read;
>
> ![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/ke.png) 

## SERIALIZABLE

> 最高的隔离级别 - 效率最低的 - 类似于锁表
>
> set session transaction isolation level serializable;
>
> A事务在查看表的时候.其他事务是不能对这张表进行任何的DML[insert,update,delete]操作 .
>
> ![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/tx.png) 

# 不可重复读和幻读区别

> set session transaction isolation level READ COMMITTED
>
> **读已提交 - 才可能产生不可重复读.**

## 不可重复读

> **针对"update"**
>
> ![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/rc.png) 

> 右边的窗口 - A事务,左边的窗口是 - B事务
>
> A事务在查看某张表[查看一次①]的同时.B事务同时在对**这张表进行update操作**.**并且B事务手动提交了事务**
>
> 这个时候A事务在当前事务中[自己的事务还未提交]再次查看这张表.多次读取出现不一样的数据 - 不可重复读.

## 幻读

> **针对"insert和delete"**

> ![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/huan.png) 

`左边的窗口 - A事务,右边的窗口 - B事务`

> A事务在更新某张表的时候/或者查看这张表,B事务同时正在对这张表进行**insert操作****.并且B事务手动提交了事务**
>
> 这个时候A事务继续查看这张表[确认一下自己的更新动作是否完成],这个时候发现表中**"居然好像还有未被更新的数据"**,
>
> 该条数据实际上是B事务已经插入进去的 - 对于A事务就好像产生了幻觉一样.



# 总结

1. 事务的四大特性[ACID]
2. DTL命令
3. 解释四个隔离级别,默认的隔离级别是可重复读
4. 脏读,不可重复读,幻读.