---
title: part01_DQL_查询
date: 2021-08-31 00:07:54
categories: mysql
tags: mysql
---

# 数据库

> **数据库(Database - 简称DB)**

> 数据按照一定的数据结构来进行组织,存储和管理的仓库.
>
> 学习的数据库mysql - 目前最主流的**关系型数据库**[免费的.现在属于oracle公司的]
>
> 安装的版本>=5.7.x -> 发展[mysql5.5.x,mysql5.6.x,**mysql.5.7.x,mysql8.x**] - 版本不一样,语法存在差异.

# SQL

> SQL - Structured Query Language - 结构化查询语言.
>
> 作用:
>
> 1. 脚本文件 - 以.sql为脚本的文件
> 2. 我们本质上就是通过sql语法/命令来操作我们的DB

## 按照功能来进行分类

> 1. DQL(Data Query Language) - 数据查询语言 - 也是整个sql的核心 - 最难的
>
> select
>
> 2. DML(Data Manipulation Language) - 数据操纵语言
>
> insert update delete
>
> 3. DDL(Data definition Language) - 数据定义语言
>
> **create** alter drop
>
> 4. DCL(Data Control Language) - 数据控制语言
>
> grant[授权],revoke[取消权限]
>
> 5. DTL(Data Transaction Language) - 数据事务语言
>
> commit savepoint rollback

## 脚本文件

> mysql中的script文件 - 是以.sql为后缀的文件.

> mysql - 数据库-**由若干张表组成的仓库**,数据是存储在一张二维**表[关系]**中的
>
> 如果一个数据库存储数据的格式是一个行和列组成表 - **关系[表格]型数据库**

> 1. **生成若干张表**
> 2. **表中插入若干条数据**

# 准备工作

##登录mysql

> 打开cmd
>
> mysql -u用户名 -p密码
>
> ```cmd
> admindeMacBook-Pro:~ admin$ mysql -uroot -proot
> ```

## 创建仓库

> MYSQL就是通过仓库的形式来管理我们的数据的.
>
> 语法:CREATE DATABASE 仓库名;  
>
> sql的语法大小写是不敏感的.
>
> ```mysql
> mysql> create database j03s;
> Query OK, 1 row affected (0.00 sec)
> ```

## 查看所有的仓库

```mysql
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| j03s               |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
```

## 使用仓库

```mysql
mysql> use j03s
```

## 查看所有的表

```mysql
mysql> show tables;
```

## 导入脚本

```mysql
mysql> source sql脚本文件的绝对路径;
```

```mysql
mysql> source /Users/admin/Desktop/j03s_mysql_all/教学脚本/mysql_cn.sql;
```

`该脚本文件中的表`

```mysql
+----------------+
| Tables_in_j03s |
+----------------+
| s_customer     |
| s_dept         |
| s_emp          |
| s_ord          |
| s_region       |
+----------------+
```

`继续导入 - 面试中常常使用到的一个脚本`

```mysql
+----------------+
| Tables_in_j03s |
+----------------+
| course         |
| sc             |
| student        |
| teacher        |
+----------------+

```

## 确认数据的编码

> 建议修改编码之后,建议重启mysql服务
>
> 右击计算机 - 管理 - 服务 - 服务和应用程序 - 右边的窗口 - mysql - 右击重启

> ```mysql
> mysql> show variables like 'char%';
> 
> ```
>
> ```mysql
> +--------------------------+-----------------------------------------------------------+
> | Variable_name            | Value                                                     |
> +--------------------------+-----------------------------------------------------------+
> | character_set_client     | utf8                                                      |
> | character_set_connection | utf8                                                      |
> | character_set_database   | utf8                                                      |
> | character_set_filesystem | binary                                                    |
> | character_set_results    | utf8                                                      |
> | character_set_server     | utf8                                                      |
> | character_set_system     | utf8                                                      |
> | character_sets_dir       | /usr/local/mysql-5.7.28-macos10.14-x86_64/share/charsets/ |
> +--------------------------+-----------------------------------------------------------+
> 
> ```

```mysql
需要对每一项进行单独的设置
mysql>set character_set_client=utf8;

```

### mysql插入中文乱码

> 1. 检测上面的编码
> 2. **my.ini文件在哪里**
>
> ```ini
> [client]
> # 设置mysql客户端默认字符集
> default-character-set=utf8
>  
> [mysqld]
> # 服务端使用的字符集默认为8比特编码的latin1字符集
> character-set-server=utf8
> 
> ```
>
> 3. 本身自己的cmd的编码
> 4. 如果还有问题,卸载重来.

# 简单Query

> 属于DQL命令 - select命令
>
> query语法
>
> 查询的本质
>
> 1. 查什么
> 2. 到哪里查[必须先使用数据库,然后才能查看这个库下的表]
>
> ```mysql
> -- 查询整张表的某些列
> SELECT 列1,列2,列n FROM 表名;
> -- 查询整张表中的所有列,*代表所有的列
> SELECT * FROM 表名;
> 
> ```

## 查询表结构

```mysql
desc 表名;

mysql> desc student;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(10)     | YES  |     | NULL    |       |
| sname | varchar(10) | YES  |     | NULL    |       |
| age   | datetime    | YES  |     | NULL    |       |
| sex   | varchar(10) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+

```

## 查询练习

> 1. 查询student表中的姓名sname
>
> ```mysql
> mysql> select sname from student;
> 
> ```
>
> 2. 查询student表中的学生信息
>
> ```mysql
> mysql> select * from student;
> 
> ```
>
> 3. 查询s_emp员工表中的first_name和last_name
>
> ```mysql
> mysql> select first_name,last_name from s_emp;
> 
> ```
>
> 4. 查询客户[s_customer]的姓名以及客户的电话号码
>
> ```mysql
> mysql> select name,phone from s_customer;
> 
> ```
>
> 5. 查询s_emp中的title列
>
> ```mysql
> mysql> select title from s_emp;
> 
> ```

> 6. 排重关键字distinct 列名
>
> ```mysql
> mysql> select distinct title from s_emp;
> 
> ```

# 条件查询

> query-conditional语法
>
> 1. 如果查询语句后面不加where条件,查询的是整张表中的数据
> 2. 需要对数据进行筛选,需要使用条件查询
> 3. 语法
>
> ```mysql
> -- 查询满足条件表达式的数据
> SELECT 列1,列2,列n
> FROM 表名
> WHERE 条件表达式;
> 
> ```

## 运算符

> 1. 比较运算符
>
>    > =,>,<,>=,<=,!=,<>
>
> 2. 其他运算符
>
>    > 2-1. in(集合列表)
>    >
>    > 2-2. not in(集合列表)
>    >
>    > 2-3. between ... and ... 在 ... 和 ...区间 - 是一个闭区间
>
> 3. is null - 判断某列是否为空,注意判断是否为空,不能使用=
>
> 4. is not null - 判断某列是否不为空,不能使用!=

### 逻辑运算符

> 1. not
> 2. and - 用来连接多个条件的,多个条件同时为true,才能返回结果
> 3. or  - 用来连接多个条件的,只要有一个条件成立,就会返回结果.

## 条件查询练习

> 1. 查询工资高于1200的员工的信息[id,first_name,salary]
>
> ```mysql
> mysql> select id,first_name,salary from s_emp where salary>1200;
> 
> ```
>
> 2. 把部门编号为41,42,43的员工信息查询出来
>
> ```mysql
> -- 写法使用in关键字 - db设计,表列命名不能使用mysql关键字
> mysql> select id,first_name,dept_id from s_emp where dept_id in(41,42,43);
> 
> -- 写法 - 使用or逻辑运算符
> mysql> select id,first_name,dept_id from s_emp where dept_id=41 or dept_id=42 or dept_id=43;
> 
> ```
>
> 3. 查询工资在1200~1450之间的员工的信息
>
> ```mysql
> -- between .. and .. 闭区间
> mysql> select id,first_name,salary from s_emp where salary between 1200 and 1450;
> 
> -- and逻辑运算符
> mysql> select id,first_name,salary from s_emp where salary>=1200 and salary<=1450;
> 
> ```
>
> 4. 查询title[职称]为Stock Clerk的员工信息
>
> ```mysql
> -- 注意title列的数据类型varchar(25) - 字符串
> -- mysql中使用单引号来表示字符串
> mysql> select id,first_name,title from s_emp where title='Stock Clerk';
> 
> -- 具体存储的值全部换成小写 - 也是可以查询出来的.
> -- mysql5.7.x版本不仅仅是sql语法忽略了大小写,表中查询也是忽略的 - 但是不建议这样做.
> mysql> select id,first_name,title from s_emp where title='stock clerk';
> 
> ```
>
> 5. 查询有提成[commission_pct]的员工信息
>
> ```mysql
> mysql> select id,first_name,commission_pct from s_emp where commission_pct is not null;
> 
> ```



# 列别名和列的计算

> 1. 我们可以给列重新命名
>
> ```mysql
> -- 语法一:列1 列别名1 - 推荐使用的语法
> mysql> SELECT 列1 列别名1,列2 列别名2 FROM 表名 [WHERE条件查询];
> 
> mysql> select id 编号,first_name 名字,salary 工资 from s_emp;
> 
> -- 语法二: 列1 as 列别名1 - 不推荐使用
> mysql>select id as 编号,first_name as 名字,salary as 工资 from s_emp;
> 
> -- 语法三: mysql中可以使用双引号来保持/包裹格式
> -- 列1 "列别名1"
> mysql> select id "编号",first_name "名字",salary "工资" from s_emp;
> 
> -- 需求:希望列别名每个汉字间距一点[空一格]
> mysql> select id "编 号",first_name "名 字",salary "工 资" from s_emp;
> 
> ```
>
> 2. mysql中的列支持计算的
>
> ```mysql
> -- 查询员工的年薪
> mysql> select first_name,salary*12 年薪 from s_emp;
> 
> ```
>
> `练习`
>
> ```mysql
> -- 查询员工的年薪,但是要包括提成
> mysql> select first_name,salary*12*(1+commission_pct/100) 年薪 from s_emp;
> 
> ```

> 3. 以上的查询结果 - 有提成的员工全部查询成功,没有提成的员工,计算年薪都是null
>
> ***在mysql中null值直接进行计算,结果还是null值***,因此一般需要对null值进行处理,如果是数字列为null.需要作为0来进行处理
>
> mysql中内置了很多函数 - **空值置换函数 - coalesce(m,n)** 计算规则类似于java中的三元操作符**m==null?n:m**
>
> ```mysql
> mysql> select first_name,salary*12*(1+coalesce(commission_pct,0)/100) 年薪 from s_emp;
> 
> ```

# 模糊查询

> 使用like关键字
>
> 1. % - 占位符 - 代表任意**多个**任意字符
> 2. _ 占位符 - 代表任意**单个**任意字符

## 练习

> 1. 比如查询teacher表中所有的李姓老师
>
> ```mysql
> mysql> select * from teacher where tname like '李%';
> 
> ```
>
> 2. 比如查询first_name第三个字母是e的员工信息
>
> ```mysql
> mysql> select first_name from s_emp where first_name like '__e%';
> 
> ```

> 3. 查询职称title中包含VP的并且工资高于1200的员工信息
>
> ```mysql
> mysql> select first_name,title,salary from s_emp where title like '%VP%' and salary>1200;
> 
> ```
>
> 4. 查询first_name以M开头的员工信息
>
> ```mysql
> mysql> select first_name from s_emp where first_name like 'M%';
> 
> ```
>
> 5. 查询first_name中不包含字母n的员工信息
>
> ```mysql
> mysql> select first_name from s_emp where first_name not like '%n%';
> 
> ```

> 6. 找出非41,42,43部门的员工
>
> ```mysql
> mysql> select first_name,dept_id from s_emp where dept_id not in(41,42,43);
> 
> ```



## 转义字符

> **了解即可**
>
> ```mysql
> mysql> update s_emp set first_name='Car_men' where id=1;
> mysql> update s_emp set first_name='Ma_rk' where id=4;
> 
> ```
>
> 需求 - 查询first_name中包含_的员工信息
>
> ```mysql
> mysql> select first_name from s_emp where first_name like '%_%';
> 
> 默认的转义字符
> mysql> select first_name from s_emp where first_name like '%\_%';
> 
> 自定义转义字符
> mysql> select first_name from s_emp where first_name like '%?_%' escape '?';
> 
> ```



# 列的拼接功能

> s_emp表中first_name[名]和last_name[姓]
>
> **db设计满足三大范式.第一范式保证列的原子性 - 列不可再分割.**
>
> 姓名 -姓一列+名一列

> ```mysql
> -- 拼接的过程也使用到了内置函数concat(...) - 可变长列表
> mysql> select concat(last_name,'  ',first_name) 姓名 from s_emp;
> 
> ```

# 排序

> ```mysql
> -- 排序的语法
> SELECT 列1,列2,列n
> FROM 表名
> [WHERE 条件表达式]
> [ORDER BY 列名] [asc | desc];
> 
> ```

## 练习

> 1. 查看员工的信息,找出工资大于1200的,并且根据salary来升序
>
> `order by + 列的名称`
>
> ```mysql
> -- asc可以省略不写
> mysql> select first_name,salary from s_emp where salary>1200 order by salary;
> 
> ```
>
> ```mysql
> -- 如果是降序的,desc不能省略的
> mysql> select first_name,salary from s_emp where salary>1200 order by salary desc;
> 
> ```
>
> 2. `order by + 列的序号` - 推荐使用的.
>
> 列的序号是查询语句中的列的序号,列的序号是从1开始的
>
> ```mysql
> mysql> select first_name,salary from s_emp where salary>1200 order by 2;
> 
> ```
>
> 3. `order by + 列别名`
>
> ```mysql
> mysql> select first_name,salary _sal from s_emp where salary>1200 order by _sal desc;
> 
> ```
>
> 4. 查看员工的信息,找出工资大于1200的,并且根据salary来降序.如果salary一样,那么继续根据first_name来进行一个升序.
>
> ```mysql
> mysql> select first_name,salary from s_emp where salary>1200 order by 2 desc,1;
> 
> ```
>
> 5. 找出年薪高于21000的员工的信息[包括提成的],并且根据年薪降序排.
>
> ```mysql
> mysql> select first_name,salary*12*(1+coalesce(commission_pct,0)/100) year_salary from s_emp where salary*12*(1+coalesce(commission_pct,0)/100)>21000 order by 2 desc;
> 
> ```

> 6. 为高级查询作个铺垫 - 子查询语法 - 精髓 - 就是把查询语句得到的表看成是真正的"临时表".
>
> ```mysql
> -- 分解  - "临时表"
> select first_name,salary*12*(1+coalesce(commission_pct,0)/100) year_salary from s_emp
> 
> ```
>
> ```mysql
> -- 整合
> -- select * from (另外一个查询语句) 表别名
> select * from (select first_name,salary*12*(1+coalesce(commission_pct,0)/100) year_salary from s_emp) core_ where year_salary>21000 order by 2 desc;
> 
> ```

# 高级查询-part01 - 最关键

> 多表查询,表和表之间的关系 - 通过**外键列**来维护的.
>
> RDBMS专业术语
>
> 1. 主键列 
> 2. **外键列** - 确定好外键列是进行多表查询的前提,通常外键列是交给多的一方进行维护的

## 关联查询

### 传统的写法

> ***如果没有直接关系的俩张表,它们是不能够直接进行一个join操作.***

> 1. 错误的写法❌  - 笛卡尔积
>
> ```mysql
> -- 查询员工(s_emp)的id,first_name以及这个员工所在的部门(s_dept)的id以及部门的名称
> mysql> select id,first_name,id,name from s_emp,s_dept;
>             
> ERROR 1052 (23000): Column 'id' in field list is ambiguous
>             
> 如果查询的结果中出现多个表中同名的列,请你给表取别名,通过表别名.列名
> mysql> select e.id,e.first_name,d.id,d.name from s_emp e,s_dept as d;
>             
> 总共出现了300条 = 25 * 12
> 
> ```

> 2. 进行筛选 - 去除错误的笛卡尔积
>
> ```mysql
> mysql> select e.id,e.first_name,d.id,d.name from s_emp e,s_dept as d where e.dept_id = d.id;
> 
> ```
>
> 3. 练习
>
> 3-1. 找出外键列
>
> 3-2. 去除笛卡尔积
>
> ```mysql
> -- 找出区域在'Asia'的所有的员工信息
> mysql> select e.first_name,r.name from s_emp e,s_dept d,s_region r
>        where r.name='Asia' and r.id = d.region_id and e.dept_id=d.id;
>             
> -- 缺点 - 如果表的数量一旦增多,发现哪些表和哪些之间是否有直接的关系.我们体验不会太好
> 
> ```

> 4. 查看学生的姓名,课程的名字,以及该课程授课的老师的名字以及该学生在这门课的得分情况
>
> ***student,course,teacher,sc***
>
> ```mysql
> mysql>select s.sname,c.cname,t.tname,_sc.score from student s,course c,teacher t,sc _sc where _sc.sid = s.id and _sc.cid = c.id and c.tid = t.id;
> 
> ```

### join+on写法

> **表 join 表 on 一的一方的主键 = 多的一方的外键.**
>
> on的目的不是为了进行条件筛选 - 目的去除笛卡尔积
>
> **只要具有直接关系的俩张表才能够使用join进行连接**

> 1. **内连接 - 默认的inner join,但是inner可以省略不写**
>
> ```mysql
> -- 查询部门id为31的部门名称以及该部门下的员工的名字和薪水
> mysql> select d.id,d.name,e.first_name,e.salary from s_emp e join s_dept d on e.dept_id = d.id where d.id=31;
> 
> ```
>
> ```mysql
> -- 找出区域在'Asia'的所有的员工信息
> mysql> select r.name,e.first_name from s_region r join s_dept d on r.id = d.region_id join s_emp e on d.id = e.dept_id where r.name='Asia';	
> 
> ```
>
> ```mysql
> -- 李老师的授课情况信息,包括教的学生的姓名,课程的名字,以及该课程授课的老师的名字以及该学生在这门课的得分情况
> mysql> select t.tname,s.sname,c.cname,_sc.score from student s join sc _sc on s.id = _sc.sid join course c on c.id = _sc.cid join teacher t on t.id = c.tid where t.tname like '李%';
> 
> ```
>
> ```mysql
> -- 查看顾客的id,姓名以及该顾客拥有的订单信息[订单所有列]
> mysql> select c.id,c.name,o.* from s_customer c join s_ord o on c.id = o.customer_id;
> 
> ```
>
> ```mysql
> -- 查看所有区域,以及这个区域上的部门信息
> mysql> select r.name,d.* from s_region r join s_dept d on d.region_id = r.id; 
> 
> ```



## 左连接和右连接

> 1. 内连接 - inner join
>
> **查看顾客的id,姓名以及该顾客拥有的订单id** - 拥有订单的客户才能够被查询出来 - inner join
>
> ```mysql
> mysql>select c.id,c.name,o.id oid from s_customer c inner join s_ord o on c.id = o.customer_id;
> -- 总共查询得到16条数据
> 
> -- inner join - 结果中少掉了207和215俩个客户 - 因为查看订单表s_ord,发现这俩个人就没有订单信息
> -- inner join - 内连接的写法,A表 join B表 - 如果A表中的记录在B表中不存在.那么关联查询之后.这个在B表中不存在的A表中的
> -- 这个记录也同样不会被关联查询出来.
> mysql> select c.id,c.name,o.id oid from s_customer c inner join s_ord o on c.id = o.customer_id;
> +-----+----------------------------+-----+
> | id  | name                       | oid |
> +-----+----------------------------+-----+
> | 201 | Unisports                  |  97 |
> | 202 | OJ Atheletics              |  98 |
> | 203 | Delhi Sports               |  99 |
> | 204 | Womansport                 | 100 |
> | 205 | Kam's Sporting Goods       | 101 |
> | 206 | Sportique                  | 102 |
> | 208 | Muench Sports              | 103 |
> | 208 | Muench Sports              | 104 |
> | 209 | Beisbol Si!                | 105 |
> | 210 | Futbol Sonora              | 106 |
> | 211 | Kuhn's Sports              | 107 |
> | 212 | Hamada Sport               | 108 |
> | 213 | Big John's Sports Emporium | 109 |
> | 214 | Ojibway Retail             | 110 |
> | 204 | Womansport                 | 111 |
> | 210 | Futbol Sonora              | 112 |
> +-----+----------------------------+-----+
> 16 rows in set (0.00 sec)
> 
> ```
>
> `分析`
>
> ```mysql
> mysql> select c.id,c.name from s_customer c;
> +-----+----------------------------+
> | id  | name                       |
> +-----+----------------------------+
> | 201 | Unisports                  |
> | 202 | OJ Atheletics              |
> | 203 | Delhi Sports               |
> | 204 | Womansport                 |
> | 205 | Kam's Sporting Goods       |
> | 206 | Sportique                  |
> | 207 | Sweet Rock Sports          |
> | 208 | Muench Sports              |
> | 209 | Beisbol Si!                |
> | 210 | Futbol Sonora              |
> | 211 | Kuhn's Sports              |
> | 212 | Hamada Sport               |
> | 213 | Big John's Sports Emporium |
> | 214 | Ojibway Retail             |
> | 215 | Sporta Russia              |
> +-----+----------------------------+
> 15 rows in set (0.00 sec)
> 
> mysql> select o.id,o.customer_id from s_ord o;
> +-----+-------------+
> | id  | customer_id |
> +-----+-------------+
> |  97 |         201 |
> |  98 |         202 |
> |  99 |         203 |
> | 100 |         204 |
> | 101 |         205 |
> | 102 |         206 |
> | 103 |         208 |
> | 104 |         208 |
> | 105 |         209 |
> | 106 |         210 |
> | 107 |         211 |
> | 108 |         212 |
> | 109 |         213 |
> | 110 |         214 |
> | 111 |         204 |
> | 112 |         210 |
> +-----+-------------+
> 16 rows in set (0.00 sec)
> 
> ```
>
> 2. 左连接 - left join
>
> **A表  join B表 - 以left join左边的表为基准表.右边的表非基准表.如果基准表中的记录在非基准表不存在.但是也能够查询出来.**
>
> 如果这个客户没有订单信息,那么也要将这个客户的信息查询出来
>
> ```mysql
> mysql> select c.id,c.name,o.id oid from s_customer c left join s_ord o on c.id = o.customer_id;
> 
> +-----+----------------------------+------+
> | id  | name                       | oid  |
> +-----+----------------------------+------+
> | 201 | Unisports                  |   97 |
> | 202 | OJ Atheletics              |   98 |
> | 203 | Delhi Sports               |   99 |
> | 204 | Womansport                 |  100 |
> | 205 | Kam's Sporting Goods       |  101 |
> | 206 | Sportique                  |  102 |
> | 208 | Muench Sports              |  103 |
> | 208 | Muench Sports              |  104 |
> | 209 | Beisbol Si!                |  105 |
> | 210 | Futbol Sonora              |  106 |
> | 211 | Kuhn's Sports              |  107 |
> | 212 | Hamada Sport               |  108 |
> | 213 | Big John's Sports Emporium |  109 |
> | 214 | Ojibway Retail             |  110 |
> | 204 | Womansport                 |  111 |
> | 210 | Futbol Sonora              |  112 |
> | 207 | Sweet Rock Sports          | NULL |
> | 215 | Sporta Russia              | NULL |
> +-----+----------------------------+------+
> 18 rows in set (0.00 sec)
> 
> ```
>
> 3. 右连接 - right join
>
> **A表  join B表 - 以right join右边的表为基准表.左边的表非基准表.如果基准表中的记录在非基准表不存在.但是也能够查询出来.**
>
> ```mysql
> select c.id,c.name,o.id oid from s_ord o right join s_customer c on c.id = o.customer_id;
> 
> ```

> 4. 自关联 - 自己关联自己
>
> -- 员工s_emp表中的manager_id - 上司的id -> 绑定的就是s_emp表中的id
>
> ```mysql
> mysql> select id,first_name,manager_id from s_emp;
> +----+------------+------------+
> | id | first_name | manager_id |
> +----+------------+------------+
> |  1 | Car_men    |       NULL |
> |  2 | LaDoris    |          1 |
> |  3 | Midori     |          1 |
> |  4 | Ma_rk      |          1 |
> |  5 | Audry      |          1 |
> |  6 | Molly      |          2 |
> |  7 | Roberta    |          2 |
> |  8 | Ben        |          2 |
> |  9 | Antoinette |          2 |
> | 10 | Marta      |          2 |
> | 11 | Colin      |          3 |
> | 12 | Henry      |          3 |
> | 13 | Yasmin     |          3 |
> | 14 | Mai        |          3 |
> | 15 | Andre      |          3 |
> | 16 | Elena      |          6 |
> | 17 | George     |          6 |
> | 18 | Akira      |          7 |
> | 19 | Vikram     |          7 |
> | 20 | Chad       |          8 |
> | 21 | Alexander  |          8 |
> | 22 | Eddie      |          9 |
> | 23 | Radha      |          9 |
> | 24 | Bela       |         10 |
> | 25 | Sylvie     |         10 |
> +----+------------+------------+
> 25 rows in set (0.00 sec)
> 
> ```
>
> 练习
>
> ```mysql
> -- 查询员工的名字,以及这个员工的上司的名字 - 一个上司拥有多个下属的.
> -- 效果:这个员工没有上司,也要能够查询出来.
> mysql> select e1.first_name 员工名字,e2.first_name 上司 from s_emp e1 left join s_emp e2 on e2.id = e1.manager_id
> 
> ```



## 分组查询

> SELECT 语句
>
> WHERE 语句
>
> GROUP BY 语句
>
> [HAVING 组函数判断]
>
> ORDER BY语句

### 多行函数

> 1. **count函数 - 统计个数**
>
> ```mysql
> -- 统计s_emp的员工的个数
> mysql> select count(*) from s_emp;
> 
> -- 除了可以写*,那么还可以推荐写id
> mysql> select count(e.id) from s_emp e;
> 
> -- 重复的数据也算1个
> mysql> select count(title) from s_emp;
> 
> -- 统计职称,重复的数据,只要统计一次
> mysql> select count(distinct title) from s_emp;
> 
> -- 如果遇到null值,忽略null值的统计
> mysql> select count(commission_pct) from s_emp;
> 
> ```
>
> 2. **avg,sum,min,max - 忽略null值**
>
> ```mysql
> -- 统计学生的得分情况
> mysql>select avg(score),sum(score),min(score),max(score) from sc;
> 
> ```

### group by

> 1. 统计各个部门编号,以及这个部门的员工的数量
>
> ```mysql
> mysql> select dept_id,count(*) from s_emp group by dept_id;
> 
> ```
>
> 2. 统计部门人数大于等于3的部门的编号以及人数信息
>
> `条件是分组之后得到的,所以不能使用where语句,where语句是出现在group by分组之前的.一定是用having语句来进行判断`
>
> `having后面一定只能跟组函数的判断`
>
> ```mysql
> mysql> select dept_id,count(*) from s_emp group by dept_id having count(*)>=3;
> 
> ```
>
> 3. 统计部门[41,42,43]人数大于等于4的部门的编号以及人数信息
>
> ```mysql
> mysql> select dept_id,count(*) from s_emp where dept_id in(41,42,43) group by dept_id having count(*)>=4;
> 
> ```
>
> 4. 统计部门[41,42,43]人数大于等于3的部门的编号以及人数信息,并且根据部门编号降序排列
>
> ```mysql
> mysql> select dept_id,count(*) from s_emp where dept_id in(41,42,43) group by dept_id having count(*)>=3 order by 1 desc;
> 
> ```

#### 分组练习

> 0. 经常分组统计 - 分组+统计.也是可以只进行分组的动作.
>
> ```mysql
> -- 统计每个职称的人数 - 分组+统计了
> mysql> select title,count(id) from s_emp group by title;
>    
> -- 只是分组 - 除了可以使用distinct来去重.那么我们也可以直接使用分组进行去重.
> mysql> select title from s_emp group by title;
> 
> ```

> 1. 统计部门的名称,编号以及该部门上的员工的数量
>
> ```mysql
> mysql> select d.id,d.name,count(e.id) from s_emp e join s_dept d on e.dept_id = d.id
>        group by d.id,d.name;
>        
> -- 注意点,写group by 容易发生错误的地方.
> 
> -- mysql5.7以后对group by的写法要求变得高了.推荐.
> -- 如果select后面除了存在组函数以外,还存在其他列.那么group by后面的列一定要和select后面的列高度保持一致.
> -- 1. select 列1,列2,组函数 from 表 group by 列1,列2
> 
> -- 但是如果select后面只有组函数
> select count(e.id) from s_emp e join s_dept d on e.dept_id = d.id group by d.id;
> 
> ```
>
> 2. 统计每个客户的订单数量[超过1的统计出来],没有订单的客户也要能够统计出来
>
> ```mysql
> mysql> select c.id,c.name,count(o.id) from s_customer c left join s_ord o on c.id = o.customer_id group by c.id,c.name having count(o.id)>1;
> 
> ```
>
> 3. 统计每个区域的部门的数量
>
> ```mysql
> mysql> select r.id,r.name,count(d.id) from s_region r join s_dept d on r.id = d.region_id group by r.id,r.name;
> 
> ```
>
> 4. 统计每个区域上的员工的数量
>
> ```mysql
> mysql>select r.name,r.id,count(e.id) from s_region r join s_dept d on r.id = d.region_id join s_emp e
> on e.dept_id = d.id group by r.name,r.id;
> 
> ```
>
> 5. 统计每个学生的课程的总分,平均分
>
> ```mysql
> mysql> select v.sid,sum(v.score),avg(v.score) from sc v group by v.sid;
> 
> -- 统计每个学生的id,姓名以及他的课程的总分,平均分
> mysql> select s.id,s.sname,sum(v.score),avg(v.score) from student s left join sc v on s.id = v.sid
> group by s.id,s.sname;
> 
> ```
>
> 6. 统计每个老师授课的课程的数量
>
> ```mysql
> mysql> select t.id,t.tname,count(c.id) from teacher t join course c on t.id = c.tid group by t.id,t.tname;
> 
> ```
>
> 7. 统计每个课程的最低分,最高分,平均分
>
> ```mysql
> mysql> select c.id,c.cname,min(v.score),max(v.score),avg(v.score) from course c 
> join sc v on c.id = v.cid group by c.id,c.cname;
> 
> ```

> 8. 统计李老师的学生数量
>
> ```mysql
> mysql> select t.id,t.tname,count(v.sid) from teacher t 
> join course c on t.id = c.tid
> join sc v on v.cid = c.id where t.tname like '李%'
> group by t.id,t.tname;
> 
> ```



## 子查询 - 最难的

> **子查询是效率不如关联查询**.但是子查询更加灵活.能用关联查询解决的查询,肯定也是可以通过子查询去解决.
>
> 但是子查询可以解决关联查询解决的查询.使用原则优先使用关联查询.
>
> 注意点:子查询必须要使用()括号
>
> 三个维度
>
> 1. **子查询作为外部查询的列.**
> 2. 子查询作为外部查询的where语句中
> 3. 子查询可以看做是一张"虚拟表/临时表",可以对这个"虚拟表"再次进行查询.

###子查询作为外部查询的where语句中

> ```mysql
> -- 查询和'Ben'在同一个部门的员工first_name,dept_id;
> 
> -- 分解 - 找出'Ben'的部门id
> mysql> select dept_id from s_emp where first_name='Ben';//43
> 
> -- 找出43部门的所有的员工,但是不包含Ben
> mysql> select first_name,dept_id from s_emp where first_name!='Ben' and dept_id=43;
> 
> -- 整合到一块儿
> mysql> select first_name,dept_id from s_emp where first_name!='Ben' and dept_id=(select dept_id from s_emp where first_name='Ben');
> 
> ```

### 子查询作为外部查询的列

> ```mysql
> -- 查询客户的id,客户的姓名,以及该客户的订单个数
> mysql> select c.id,c.name,(select count(o.customer_id) from s_ord o where c.id=o.customer_id) 订单数量
> from s_customer c;
> 
> ```
>
> ```mysql
> -- 查询部门的id,部门的名称,以及这个部门上的员工的个数
> mysql> select d.id,d.name,(select count(e.id) from s_emp e where e.dept_id = d.id) 员工数 from s_dept d;
> 
> ```

###子查询可以看做是一张"虚拟表/临时表",可以对这个"虚拟表"再次进行查询.

> 使用场景最多的子查询
>
> ```mysql
> -- 用子查询来实现 - 查询和'Ben'在同一个部门的员工first_name,dept_id
> 
> -- (select e.dept_id from s_emp e1 where e1.first_name='Ben')结果必须返回单个值
> mysql> select e.first_name,(select e.dept_id from s_emp e1 where e1.first_name='Ben') from s_emp e;
> 和下面的这个效果是一样的
> mysql> select e.first_name,e.dept_id from s_emp e;
> 
> -- 修改
> -- 思路 e.first_name,子查询e.dept_id - 判断成立 - 有值,否则为null
> -- e.dept_id是否和'Ben'是一个部门的.
> select e.first_name,
> (select e.dept_id from s_emp e1 
> where e1.first_name='Ben' and e.dept_id=e1.dept_id) did
> from s_emp e;
> 
> -- 把上面的查询的结果作为"虚拟表" - 别名 - core_
> select * from (
> 	select e.first_name,
> (select e.dept_id from s_emp e1 
> where e1.first_name='Ben' and e.dept_id=e1.dept_id) did
> 	 from s_emp e
> ) core_ where core_.did is not null and core_.first_name<>'Ben';
> 
> ```



## exists和not exists用法

> 1. exists - 是否存在.只要存在,那么就返回true - 顺利被查询出来
> 2. not exists - 不存在  如果是false,反而会被顺利查询出来
> 3. 案例
>
> ```mysql
> -- exists(子查询 - 如果有结果,返回true,否则返回false)
> mysql>select first_name from s_emp where exists(select 1 from s_emp where 1=1);
> 
> mysql>select first_name from s_emp where not exists(select 1 from s_emp where 1=2);
> 
> ```

### 练习 - salary是不重复的

> 1. **查询和'Ben'在同一个部门的员工first_name,dept_id**
>
> ```mysql
> mysql> select e.first_name,e.dept_id from s_emp e where exists(
> select 1 from s_emp e1 where e1.first_name='Ben' and e1.dept_id=e.dept_id
> ) and e.first_name<>'Ben';
> 
> ```

> 2. 找出各个部门工资排名前二的员工
>
> "e-我"这个部门中,如果还有人工资比我高.那么这个数量不能超过1个.
>
> ```mysql
> mysql> select e.dept_id,e.first_name,e.salary from s_emp e where exists(
>   select 1 from s_emp e1 where e.dept_id = e1.dept_id and e1.salary>e.salary
>   having count(*)<=1
> ) order by 1;
> 
> ```
>
> not exists来实现的
>
> ```mysql
> 不存在'我e'这个部门中,有人的工资比我高.并且这个数量超过1个.
> mysql> select e.dept_id,e.first_name,e.salary from s_emp e where not exists(
>   select 1 from s_emp e1 where e.dept_id = e1.dept_id and e1.salary>e.salary
>   having count(*)>1
> ) order by 1;
> 
> ```
>
> 3. 找出各个部门中工资最高的 
>
> ```mysql
> mysql> select e.dept_id,e.first_name,e.salary from s_emp e where not exists(
>   select 1 from s_emp e1 where e.dept_id = e1.dept_id and e1.salary>e.salary
>   having count(*)>0
> ) order by 1;
> 
> ```

# mysql 单行函数 - 了解即可

> 第一个作业 - 字符串函数+数字函数+日期函数[能有多少] - mysql_function.md

## 字符串函数

> 1. instr(str,substr) - 返回字符串substr在字符串str第一次出现的位置(str不包含substr时返回0)
>
> mysql下标索引从1开始的
>
> ```mysql
> mysql>select instr('ppopo','o');//3,找不到则返回0
> 
> ```
>
> 2. lpad(str,len,padstr) - 用字符串padstr填补str左端直到字串长度为len并返回 
> 3. rpad(str,len,padstr) - 用字符串padstr填补str右端直到字串长度为len并返回
>
> ```mysql
> mysql>select rpad('abc',6,'do');//abcdod
> 
> mysql>select rpad('吴亦凡',10,'*');//
> 
> -- oracle数据库rpad函数 - 遇到汉字,一个汉字是占3个长度吴亦凡*
> 
> ```
>
> 4. left(str,len) - 返回字符串str的左端len个字符 
> 5. right(str,len) - 返回字符串str的右端len个字符 
> 6. substring(str,pos,len) - 返回字符串str的位置pos起len个字符  
> 7. substring(str,pos) - 返回字符串str的位置pos起后面的子串 
> 8. ltrim(str) - 返回删除了左空格的字符串str  
> 9. rtrim(str)  - 返回删除了右空格的字符串str  
> 10. space(n) - 返回由n个空格字符组成的一个字符串  
> 11. ***replace(str,from_str,to_str)  - 用字符串to_str替换字符串str中的子串from_str并返回*** 
> 12. reverse(str) - 颠倒字符串str的字符顺序并返回
> 13. insert(str,pos,len,newstr) - 把字符串str由位置pos起len个字符长的子串替换为字符串
>
> ```mysql
>  mysql>select insert('Carmen',2,5,'*****');
> 
> ```
>
> 14. lower(str) - 返回小写的字符串str  
> 15. upper(str) - 返回大写的字符串str
> 16. **char_length(str) -  不管汉字还是数字或者是字母都算是一个字符。**
> 17. **length(str);//汉字占3个,其他占1个.**

### 列中的应用

```mysql
-- 将first_name列的数据全部反转
mysql> select first_name,reverse(first_name) from s_emp;

-- 这些函数是允许嵌套使用的
mysql> select first_name,upper(reverse(first_name)) from s_emp;

-- 练习 - 模拟银行账户的输出方式-考虑该列是中文的情况.
-- 查询效果
-- Car_men   C******
-- 吴亦凡     吴**
-- Mark      M**

-- concat函数 - 字符串的拼接
mysql> select first_name,concat(substring(first_name,1,1),rpad('*',char_length(first_name)-1,'*')) 显示账户 from s_emp;

-- replace函数
-- replace(str,oldStr,newStr);
mysql> select first_name,replace(first_name,substring(first_name,2),rpad('*',char_length(first_name)-1,'*')) 账户 from s_emp;

-- 直接使用rpad函数
-- rpad(str,拼接之后总的长度,appendStr);
mysql>select first_name,rpad(substring(first_name,1,1),char_length(first_name),'*') 账户 from s_emp;

-- 使用insert函数
-- insert(str,pos,len,newstr) - 把字符串str由位置pos起len个字符长的子串替换为字符串

-- insert函数的基本使用
mysql> select insert('abcde',2,3,'*');//a*e

mysql> select first_name,insert(first_name,2,char_length(first_name)-1,rpad('*',char_length(first_name)-1,'*')) 账户 from s_emp;

```

> ```mysql
> 剖析一下 - s_customer表phone列最后一个'-'后面的phone的数字.
> 
> mysql> select phone,reverse(phone) from s_customer;
> +----------------+----------------+
> | phone          | reverse(phone) |
> +----------------+----------------+
> | 55-2066101     | 1016602-55     |
> | 81-20101       | 10102-18       |
> | 91-10351       | 15301-19       |
> | 1-206-104-0103 | 3010-401-602-1 |
> | 852-3692888    | 8882963-258    |
> | 33-2257201     | 1027522-33     |
> | 234-6036201    | 1026306-432    |
> | 49-527454      | 454725-94      |
> | 809-352689     | 986253-908     |
> | 52-404562      | 265404-25      |
> | 42-111292      | 292111-24      |
> | 20-1209211     | 1129021-02     |
> | 1-415-555-6281 | 1826-555-514-1 |
> | 1-716-555-7171 | 1717-555-617-1 |
> | 7-3892456      | 6542983-7      |
> +----------------+----------------+
> 
> instr(str,substr);//从左到右查找substr在str中第一次出现的位置
> 
> mysql> select instr(reverse(phone),'-') from s_customer;
> 
> -- 取出'-'左边的元素
> left(str,len) - 返回字符串str的左端len个字符
> 
> mysql> select phone,left(reverse(phone),instr(reverse(phone),'-')-1) from s_customer;
> 
> +----------------+--------------------------------------------------+
> | phone          | left(reverse(phone),instr(reverse(phone),'-')-1) |
> +----------------+--------------------------------------------------+
> | 55-2066101     | 1016602                                          |
> | 81-20101       | 10102                                            |
> | 91-10351       | 15301                                            |
> | 1-206-104-0103 | 3010                                             |
> | 852-3692888    | 8882963                                          |
> | 33-2257201     | 1027522                                          |
> | 234-6036201    | 1026306                                          |
> | 49-527454      | 454725                                           |
> | 809-352689     | 986253                                           |
> | 52-404562      | 265404                                           |
> | 42-111292      | 292111                                           |
> | 20-1209211     | 1129021                                          |
> | 1-415-555-6281 | 1826                                             |
> | 1-716-555-7171 | 1717                                             |
> | 7-3892456      | 6542983                                          |
> +----------------+--------------------------------------------------+
> 
> -- 继续反转
> mysql> select phone,reverse(left(reverse(phone),instr(reverse(phone),'-')-1)) from s_customer;
> 
> ```

## 数字函数

> 1. abs(n) - 求绝对值
> 2. mod(n,m) - 取模运算,返回n被m除的余数(同%操作符)
> 3. **floor(n) - 返回不大于n的最大整数值** - 向下取整  
> 4. **ceiling(n) - 返回不小于n的最小整数值**  - 向上取整 
> 5. round(n[,d]) - 返回n的四舍五入值,保留d位小数(d的默认值为0)
>
> ```mysql
> mysql>select round(3.5);
> 
> mysql>select round(3.456,2);//3.46
> 
> ```
>
> 6. pow(x,y) - 返回值x的y次幂  
> 7. sqrt(n) - 返回非负数n的平方根
> 8. pi() - 返回圆周率  
> 9. rand() - 返回在范围**[0到1.0)**内的随机浮点值
> 10. **truncate(n,d) - 保留数字n的d位小数并返回  - 直接截取**

### 练习

> 1. 求1~3之间的随机整数
>
> ```mysql
> mysql> select floor(rand()*3+1);
> mysql> select truncate(rand()*3+1,0);
> 
> ```

> 2. 计算年薪 - 保留小数点2位
>
> ```mysql
> select first_name,truncate(salary*12*(1+coalesce(commission_pct,0)/100),2) 年薪 from s_emp;
> 
> ```

## 日期函数

> 查询当前系统的日期 - **select now();**
>
> 1. dayofweek(date) - 返回日期date是星期几(1=星期天,2=星期一,……7=星期六,odbc标准)  
> 2. weekday(date) - 返回日期date是星期几(0=星期一,1=星期二,……6= 星期天)
> 3. **year(date) - 返回date的年份(范围在1000到9999)**    
> 4. **month(date)  - 返回date中的月份数值**   
> 5. **dayofmonth(date)** - 返回date是一月中的第几日(在1到31范围内)   
> 6. **hour(time) - 返回time的小时数(范围是0到23)**  
> 7. **minute(time) - 返回time的分钟数(范围是0到59)** 
> 8. **second(time) - 返回time的秒数(范围是0到59)** 
> 9. period_add(p,n) - 增加n个月到时期p并返回(p的格式yymm或yyyymm) 
>
> ```mysql
> mysql> select period_add('202108',3);
> 
> -- 需求把s_emp表中的start_date往后延1个月
> mysql> select start_date,period_add(date_format(start_date,'%Y%m'),1) from s_emp;
> 
> ```
>
> 注意:p可以是字符串,一定要满足一定的格式,或者可以直接使用date类型.
>
> 10. period_diff(p1,p2) - 返回在时期p1和p2之间月数(p1和p2的格式yymm或yyyymm)  p1-p2
> 11. curdate() - 以'yyyy-mm-dd'或yyyymmdd格式返回当前日期值(根据返回值所处上下文是字符串或数字) 
> 12. curtime() - 以'hh:mm:ss'或hhmmss格式返回当前时间值(根据返回值所处上下文是字符串或数字)
> 13. now() - 以'yyyy-mm-dd hh:mm:ss'或yyyymmddhhmmss格式返回当前日期时间(根据返回值所处上下文是字符串或数字)     
> 14. last_day(date) - date日期所在月的最后一天是什么时候
> 15. datediff(d1,d2) - 两个日期d1,d2之间相差的天数
> 16. timestampdiff(type,d1,d2) - type - year,month,day...
>
> 
>
> mysql中,虽然日期date_format函数来转成字符串类型的,mysql内部为了简化我们的写法,有的时候有的函数.
>
> 只要你传入的字符串满足一定的格式,那么它就会自动被转成日期函数.

### 补充

```mysql
1. date_add(date,interval n type)
2. date_sub(date,interval n type)
type:
year
month
day
hour
minute
second

```



## 日期格式化

> **date_format(date,format)**   - 把日期转换成指定模板的字符串.
>
> 根据format字符串格式化date值  
> 　(在format字符串中可用标志符:  
> 　%m 月名字(january……december)    
> 　%w 星期名字(sunday……saturday)    
> 　%d 有英语前缀的月份的日期(1st, 2nd, 3rd, 等等。）    
> 　**%Y 年, 数字, 4 位    
> 　%y 年, 数字, 2 位**    
> 　%a 缩写的星期名字(sun……sat)    
> 　**%d 月份中的天数, 数字(00……31)    
> 　%e 月份中的天数, 数字(0……31)**    
> 　**%m 月, 数字(01……12)    
> 　%c 月, 数字(1……12)**    
> 　%b 缩写的月份名字(jan……dec)    
> 　%j 一年中的天数(001……366)    
> 　**%H 24时制小时(00……23)**    
> 　%k 小时(0……23)    
> 　%h 12时小时(01……12)    
> 　**%i 小时(01……12)**    
> 　%l 小时(1……12)    
> 　**%i 分钟, 数字(00……59)**    
> 　%r 时间,12 小时(hh:mm:ss [ap]m)    
> 　%t 时间,24 小时(hh:mm:ss)    
> 　**%s 秒(00……59)**       
> 　%p am或pm    
> 　%w 一个星期中的天数(0=sunday ……6=saturday ）   

## demo

```mysql
mysql> select start_date,date_format(start_date,'%Y-%m-%d %H:%i:%s') from s_emp;

```

### 日期练习

> 1. 找出员工的工作月数 - 和当前的日比较的
>
> ```mysql
> mysql>  select first_name,start_date,timestampdiff(month,start_date,now()) 月份 from s_emp;
> 
> ```
>
> 2. 查询员工的工作天数
>
> ```mysql
> mysql> select first_name,start_date,timestampdiff(day,start_date,now()) 天数 from s_emp;
> 
> mysql> select first_name,start_date,abs(datediff(start_date,now())) 天数 from s_emp;
> 
> ```
>
> 3. 计算一年前,当前,一年后的日期
>
>    > 1. date_add(date,interval n type)
>    > 2. date_sub(date,interval n type)
>
> ```mysql
> mysql> select date_sub(now(),interval 1 year) 一年前,now() 当前,date_add(now(),interval 1 year) 一年后;
> 
> mysql> select date_add(now(),interval -12 month) 一年前,now() 当前,date_add(now(),interval 12 month) 一年后;
> 
> ```
>
> 4. 当前日期前6个月的最后一天
>
> ```mysql
> mysql> select last_day(date_sub(now(),interval 6 month));
> 
> ```
>
> 5. 把员工的入职日期格式化为年/月/日
>
> ```mysql
> mysql> select start_date,date_format(start_date,'%Y-%m-%d') from s_emp;
> 
> -- 1990年05月03日
> mysql> select start_date,date_format(start_date,'%Y年%m月%d日') from s_emp;
> 
> ```
>
> 6. 找出5月份入职的员工
>
> ```mysql
> mysql> select first_name,start_date from s_emp where month(start_date)=5;
> 
> ```



# 类型的转换

## 日期转字符串

> 数据查询出来之后显示用的.
>
> **date_format(date,模板);**

## 字符串转日期

> 插入数据的时候,需要将日期列的字符串转换成日期类型,才能够插入到date类型列.
>
> 1. **mysql中只要你的字符串满足默认支持的格式.那么就会默认转成date类型.**
>
> ```mysql
> -- date列
> insert into test_date values(3,'xx','2019-09-08');//ok '2019-09-08'
> insert into test_date values(4,'xx','2019/10/08');//ok '2019/10/08'
> insert into test_date values(5,'xx','20191105');//ok '20191105'
> insert into test_date values(5,'xx','2018-11-05 13:08:09');//ok
> 
> 假设如果指定的是一个非法的字符串模板
> mysql> insert into test_date values(5,'xx','05/11/2023');
> ERROR 1292 (22007): Incorrect date value: '05/11/2023' for column 'd1' at row 1
> 
> ```
>
> 2. 解决一个非法模板的字符串 - mysql不能把这个形式的字符串自动转换成date/datetime类型
>
> **str_to_date(str,pattern)**
>
> ```java
> str的格式也必须要和pattern是匹配 - 实现str-date类型的转换
>         
> mysql> insert into test_date values(6,'xx',str_to_date('05/11/2023','%d/%m/%Y'));
> 
> ```

# date和datetime

> date - 年月日
>
> datetime - 年月日时分秒

> mysql中列如果是一个日期,常用的俩个类型分别是date和datetime
>
> ```mysql
> drop table test_date;
> 
> create table test_date(
> 	id int(7),
> 	name varchar(20),
> 	d1 date
> );
> -- 插入数据
> insert into test_date values(1,'admin',now());
> 
> -- 插入指定的日期到表中
> mysql> insert into test_date values(5,'xx','20191105');
> 
> -- 列是date,但是插入数据包含时分秒的
> mysql> insert into test_date values(5,'xx','2018-11-05 13:08:09');
> 
> -- 查询出来的d1列的内容默认显示的日期的格式%Y-%m-%d
> mysql> select * from test_date;
> +------+-------+------------+
> | id   | name  | d1         |
> +------+-------+------------+
> |    1 | admin | 2021-08-17 |
> +------+-------+------------+
> 
> drop table test_datetime;
> 
> create table test_datetime(
> 	id int(7),
> 	name varchar(20),
> 	d1 datetime
> );
> insert into test_datetime values(1,'tom',now());
> 
> -- 效果,如果列设置成了datetime,查询出来的模板是-%Y-%m-%d %H:%i:%s
> mysql> select * from test_datetime;
> +------+------+---------------------+
> | id   | name | d1                  |
> +------+------+---------------------+
> |    1 | tom  | 2021-08-17 10:12:18 |
> +------+------+---------------------+
> 
> -- 插入一个指定的日期 - 仅仅包含年月日
> mysql> insert into test_datetime values(5,'xx','2019-09-08');
> 
> -- 时分秒是自动归0
> 5 | xx   | 2019-09-08 00:00:00 
> 
> mysql>insert into test_datetime values(5,'xx','2019/09/08 13:12:18');
> 
> 
> 5 | xx   | 2019-09-08 13:12:18
> ```

