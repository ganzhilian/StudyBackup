---
title: part06_视图和limit
date: 2021-08-31 00:16:01
categories: mysql
tags: mysql
---

# limit语句

> 应用场景就是 - 网页中的数据[肯定是mysql中的]肯定是要进行分页的.
>
> ```mysql
> -- 获取表中前m行的数据.
> -- limit m
> mysql> select * from s_emp limit 3;
> ```
>
> ```mysql
> -- limit m,n
> -- m代表的是行下标,从0开始
> -- n代表的是显示的条数
> 
> -- 从第3行开始,显示5条
> mysql> select * from s_emp limit 2,5;
> ```

## 分页的公式

> 场景:
>
> 1. 对于用户而言,点击多少页,比如选择1页,认为是第一页.
>
> pageNow - 当前页.   pageSize - 每页显示条数
>
> ```mysql
> limit (pageNow-1)*pageSize,pageSize
> ```

## sql优化的部分

> limit m,n 
>
> m - 偏移量  n-显示的条数
>
> 如果偏移量比较大,limit语句的查询性能就会低下.
>
> limit优化常见的两种场景
>
> 1. 假设stu表中name列没有添加索引.但是name是从业角度考虑它应该是唯一的.
>
> ```mysql
> select * from stu where name='tom';
> 
> -- 肯定会进行全表扫描的. - 数据量 - 导致查询效率低下
> explain select * from stu where name='tom';
> 
> -- 已经确认name列值肯定是唯一的 - 避免全表扫描
> select * from stu where name='tom' limit 1;
> ```
>
> 2. **前提条件是id主键列是自增且连续**,由于limit m,n随着m[偏移量]增加,导致查询效率低下
>
> ```mysql
> select * from s_emp limit 10000,2;
> 
> 优化
> select * from s_emp where id>10000 limit 2;
> ```



# 视图

> 视图 - "虚拟表",类似于原表的"镜像",之前是怎么操作table的,怎么操作视图view
>
> 优势:
>
> 1. **保证数据的安全性.**
>
> 比如某张表中的隐秘数据比较多,不希望程序员看到你这个原表.比如emp(id,name,salary,birthday).
>
> 隐秘数据比如有salary,birthday.为了保障数据的安全性.db[数据库管理员]可以为emp创建视图出来
>
> ```mysql
> create view emp_view as select id,name from emp;
> 
> 接下来程序员的操作,直接对视图进行操作了[select,insert,update,delete] - > 对原表也会产生影响.
> 但是删除视图,是不会删除原表的.但是删除原表,视图肯定是不存在的.
> ```

> 2. 封装一些比较繁琐的关联查询,在以后再次用到的时候.可以直接查询视图.
> 3. 简化我们查询数据

## 简单分类

> 1. 简单视图 - 视图来自于单张表的查询
> 2. 复杂视图 - 视图来自于关联查询[多张表]

## 创建视图

```mysql
create view 视图名 as select语句;
```

```mysql
为student表创建一个简单视图
create view stu_view as select * from student;
```

## 删除视图

```mysql
drop view 视图名;
```

## 操作视图

> 1. 视图的查询 - DQL
>
> ```mysql
> select * from stu_view;
> 
> ```
>
> 2. 修改原表,看是否对视图造成影响 - 会
>
> ```mysql
> update student set sname='admin' where id=1;
> 
> select * from stu_view;
> |    1 | admin  | 1990-01-01 00:00:00 | 男   |
> 
> ```

> 3. 能不能对简单视图进行dml操作 - 可以
>
> `对视图的dml操作同样也会对原表产生影响`
>
> ```mysql
> update stu_view set sname='success' where id=1;
> 
> ```



## 复杂视图

> 复杂视图代表整个视图的来源是关联表的查询  - 就是用来被查询的.
>
> ```mysql
> -- 查询区域名以及这个区域上的员工的first_name
> drop view region_view;
> create view region_view
> as 
> select r.name,e.first_name from s_region r 
> join s_dept d on d.region_id = r.id
> join s_emp e on e.dept_id = d.id;
> 
> ```
>
> ```mysql
> select * from region_view;
> 
> ```

> `它和简单视图的一个区别在于,复杂视图是不能够执行DML操作的`
>
> ```mysql
> delete from region_view where name = 'North America';
> ERROR 1395 (HY000): Can not delete from join view 'j03s.region_view'
> 
> ```



# with check option

> ```mysql
> drop view stu_view;
> create view stu_view
> as 
> select id,sname from student where id=13;
> 
> select * from stu_view;
> 
> -- 更新视图 - 更新的是视图来源的那条sql的where中的条件列
> update stu_view set id=100 where id=13;
> 
> -- 视图没有了 - 原表中已经没有了 - 视图的更新会同步给原表[对原表产生了影响了]
> mysql> select * from stu_view;
> Empty set (0.00 sec)
> 
> -- 不允许视图去更新自己来源的那条sql中的where条件列.
> drop view stu_view;
> create view stu_view
> as 
> select id,sname from student where id=11 with check option;
> 
> mysql> select * from stu_view;
> +------+--------+
> | id   | sname  |
> +------+--------+
> |   11 | 李四   |
> +------+--------+
> 
> -- 检测with check option的效果 - 更新视图的时候,是不允许更新id列
> update stu_view set id=900 where id=11;
> ERROR 1369 (HY000): CHECK OPTION failed 'j03s.stu_view'
> 
> ```



# 总结面试题

> 1. **DB,DBA,DBS,DBMS,RDBMS** 
> 2. **delete和truncate和drop区别**
>
> ```mysql
> delete - DML
> truncate - DDL
> -- 清空表
> truncate 表名;
> 
> drop  - ddl - 删除表
> 
> ```
>
> 3. 数据库优化 - sql优化
> 4. **悲观锁和乐观锁**
> 5. innodb和myisam区别
> 6. 索引底层原理[innodb和myisam] - b+树
> 7. 聚簇索引和非聚簇索引[辅助索引]
> 8. 事务的隔离级别
> 9. 脏读,**不可重复读**,可重复读**,幻读**
> 10. **DQL查询语句** - 项目
> 11. 存储过程和函数的区别
> 12. 视图的优势
> 13. 三大范式概念
> 14. **SQL按照功能分类[DCL,DTL,DML,DQL,DDL]**