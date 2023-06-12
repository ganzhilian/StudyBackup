---
title: part05_视图函数存储过程
date: 2021-08-31 00:15:14
categories: mysql
tags: mysql
---

# 函数function

> 了解即可 - 开发中肯定用不到.

> 单行函数 - 字符串函数,数字函数,日期函数
>
> 多行函数(组函数)
>
> 实际上数据库是可以进行编程的.

> 如果使用的是mysql8.x,需要设置一下才能允许自定义函数
>
> ```mysql
> 1. 在my.ini文件下添加：log-bin-trust-function-creators=1 
> 需要重启db服务器
> 
> 2. 在mysql客户端[终端] - set global log_bin_trust_function_creators=TRUE;
> ```

## 语法

`函数语法 - 创建`

`注意点:`

`1. 函数体中肯定有return语句`

`2. 只要遇到varchar类型,必须要指定参数的长度`

```mysql
-- mysql中默认的结束符号使用的是;
-- 修改了默认的结束符号位$$
-- 函数体中肯定会出现分号
delimiter $$

-- 创建函数
create function 函数名([变量 数据类型(长度)]) returns 返回数据类型
begin
	-- 函数体
	return 结果;
end $$

delimiter ;
```

`删除函数`

```mysql
-- 删除函数
drop function 函数名;
```

## 练习1 - 自定义函数

> 传入俩个整数,返回俩个整数的相加的结果
>
> ```mysql
> drop function adds;
> 
> delimiter //
> create function adds(a int,b int) returns int
> begin
> 	return a + b;
> end //
> delimiter ;
> ```
>
> 调用函数
>
> ```mysql
> select adds(10,30);
> ```

## 练习2 - 自定义函数

> 日期格式化  xxxx年xx月xx日
>
> ```mysql
> drop function my_date;
> 
> delimiter //
> create function my_date(dt date) returns varchar(20)
> begin
> 	return date_format(dt,'%Y年%m月%d日');
> end //
> delimiter ;
> 
> -- 调用
> select my_date(now());
> 
> select my_date('2010-09-09');
> ```

## 练习3 - while..do...end while

> **感受循环语句(while .. do. .. end while)以及局部变量(declare)以及变量如何重新设置(set)**
>
> 1~x之间的总和
>
> ```mysql
> drop function x_add;
> delimiter //
> 
> create function x_add(x int) returns int
> begin
> 	-- 定义俩个局部变量
> 	declare i int default 1;
> 	declare sums int default 0;
> 	-- 循环
> 	while i<=x do
> 		-- sums加
> 		set sums = sums+i;
> 		-- i自增
> 		set i = i + 1;
> 	end while;
> 	return sums;
> end //
> delimiter ;
> 
> select x_add(100);
> ```

## 练习4 - if...end if

> 分支语句
>
> 1~x之间的偶数奇数进行相加
>
> ```mysql
> drop function ji_add;
> delimiter //
> create function ji_add(x int) returns int
> begin
> 	declare i int default 1;
> 	declare sums int default 0;
> 	while i<=x do
> 		if i%2!=0 then
> 		set sums = sums+i;
> 		end if;
> 		set i = i + 1;
> 	end while;
> 	return sums;
> end //
> delimiter ;
> 
> select ji_add(100);
> ```

## 练习4 - 全局变量

> 1~x之间的数字之后,但是不包括5的倍数
>
> ```mysql
> drop function jis_add;
> delimiter //
> create function jis_add(x int) returns int
> begin
> 	-- 定义一个局部变量
> 	declare i int default 1;
> 	-- 定义一个全局变量 - mysql能够识别到的符号@ - @变量名 - 全局变量
> 	set @sums = 0;
> 	-- 类似于java中的continue语句 + 代码块命名
> 	-- success是一个循环语句标签名称 - 随便命名的
> 	success:while i<=x do
> 		if i%5=0 then
> 			set i = i + 1;
> 			-- 下面的代码不执行
> 			-- 继续迭代success的循环 - continue - 继续下一轮循环,忽略本轮的循环
> 			iterate success;
> 		end if;
> 		set @sums = @sums + i;
> 		set i = i + 1;
> 	end while;
> 	return @sums;
> end //
> delimiter ;
> 
> select jis_add(100);
> -- 全局变量 - 函数体外部是可以访问得到的
> select @sums;
> 
> ```



# 存储过程procedure

> 安装好mysql环境之后,实际上就是在本地中安装好了mysql-server - mysql的服务器端以及mysql-client - mysql的客户端
>
> mysql-server - 右击计算机 - 管理 - 服务 - 服务和应用程序 - 右边👉 - Mysql服务
>
> mysql-client - mysql自带的一个客户端.市面上还有很多第三方的客户端[可视化效果不错] - navicat[收费的],idea

> ***一条sql语句的执行的流程.***
>
> **mysql> select * from s_emp;**
>
> ***①当输入sql完毕之后,敲下enter键之后,客户端检测一下sql的语法是否规范.如果不规范,直接报错.***
>
> ***②如果sql语法是规范的,客户端会负责将这条合法的sql语法发送到mysql-server  - mysql服务器端***
>
> ***③mysql-server接受到来自于客户端的sql之后.mysql-server会对这条sql进行编译和解析***
>
> ***④mysql-server会将解析的结果返回给mysql-client端[sql语句的执行结果]***
>
> ***总结: sql语句 - 语法检测 - 发送到mysql-server - 编译,解析,返回给mysql-client***,将这次sql执行的结果保存

## 作用

> 为了完成一些特定的功能,提前讲sql预编译好,存储在mysql-server端系统的执行计划表中.第一次去调用存储过程的时候
>
> 会对sql进行预编译并且进行保存.第二次再调用的时候,省去了sql语句的编译的过程.
>
> **存储过程可以做到标准的组件编程[封装sql语句]**

## 语法

```mysql
-- 删除存储过程
drop procedure 存储过程名;

-- 创建存储过程
delimiter //
create procedure 存储过程名([in|out] [参数列表])
begin
	-- 过程体
end //
delimiter ;

```

## 体验

```mysql
-- 把s_emp表中的员工的平均薪资的sql预编译好放在mysql-server端
-- select avg(salary) from s_emp;
-- 该条sql经常会被频繁调用/或者该条sql写法比较复杂.
-- 考虑把这条sql封装到 - 存储过程

drop procedure sal_pro;

delimiter //
create procedure sal_pro()
begin
	-- 封装sql语句
	select avg(salary) from s_emp;
end //

delimiter ;

-- 预编译sql语句 - 创建存储过程的时候
-- mysql-server会对这个存储过程中的sql语句进行编译,解析,将sql语句的编译的结果进行保存.
-- 后续的每次调用的过程中.应该不会再对这条sql语句进行编译了.

-- 使用call关键字来调用,不需要编译
-- 第一次调用,
call sal_pro();

-- 第二次调用
-- mysql-server不需要对这个存储过程中的sql语句再去进行编译了[省略了],直接返回之前保存的结果.
call sal_pro();

```

## 输入和输出

> 1. in
> 2. out
> 3. inout

### in输入

> 创建存储过程的时候,参数列表.确认这个参数到底是用来接受参数的,还是用来返回存储过程的结果的.
>
> ```mysql
> drop procedure in_pro;
> delimiter //
> create procedure in_pro(in a int)
> begin
> 	-- System.out.println(a);
> 	select a;
> 	-- 对a重新赋值
> 	set a = 10;
> 	select a;
> end //
> delimiter ;
> 
> -- 调用
> -- 直接传入一个字面量
> call in_pro(20);
> 
> -- 测试一下使用全局变量进行传参
> set @i = 20;
> call in_pro(@i);
> 
> ```

### out输出

> 如果参数是使用out来修饰的,它是不能够用来保存接受到的数据的
>
> ```mysql
> drop procedure out_pro;
> delimiter //
> create procedure out_pro(out a int)
> begin
> 	-- a是用out修饰的,所以为null
> 	select a;
> 	-- a是out - 存储过程返回出去的数据
> 	set a = 10;
> end //
> delimiter ;
> 
> -- 报错,如果参数是out修饰的,那么是不能够直接传入一个字面量的
> -- call out_pro(100);
> 
> -- 如果是out修饰的,调用存储过程,只能传入一个全局变量
> set @i=100;
> call out_pro(@i);
> +------+
> | a    |
> +------+
> | NULL |
> +------+
> 里面的a是没有绑定到你的100 - 原因就是因为a是使用out修饰的.不能接受参数
> 
> select @i;
> +------+
> | @i   |
> +------+
> |   10 |
> +------+
> 
> ```

## 练习 - 封装单个结果集

> 1. 根据员工的id来返回员工的名称,薪资 - sql语句 - 封装
>
> ```mysql
> drop procedure emp_pro;
> delimiter //
> create procedure emp_pro(in eid int(7),out fname varchar(25),out sal float(11,2))
> begin
> 	select first_name into fname from s_emp where id=eid;
> 	select salary into sal from s_emp where id = eid;
> end //
> delimiter ;
>       
> -- 如果参数是out修饰的,必须只能传入全局变量[作用就是来保存存储过程一些执行结果的值]
> call emp_pro(1,@fname,@sal);
>       
> select @fname;
> select @sal;
> 
> ```

> 2. 第二种写法 - 针对的是单个结果集的写法
>
> ```mysql
> drop procedure emp_pro;
> delimiter //
> create procedure emp_pro(in eid int(7),out fname varchar(25),out sal float(11,2))
> begin
> 	select first_name,salary into fname,sal from s_emp where id=eid;
> end //
> delimiter ;
>       
> -- 如果参数是out修饰的,必须只能传入全局变量[作用就是来保存存储过程一些执行结果的值]
> call emp_pro(1,@fname,@sal);
>       
> select @fname;
> select @sal;
> 
> ```

## 练习 - 封装的是多行结果集

> 找出大于某个id的first_name和salary - 结果可能是多行的
>
> 1. 现在已经被弃用的游标 - 性能很低下.
> 2. **需要定义第三方表来存储我们的多行结果集**
>
> ```mysql
>    1. 根据具体的查询的业务来构建第三方表 
>    业务中最终查询出来的数据正好都在s_emp表中.
>          
>    -- 仅仅保留结构,但是不保留结果
>    create table s_emp_pro select first_name,salary from s_emp where 1=2;
>          
>    2. 比如分组统计,查询出来的数据不在某个表中存在 - 一定要根据具体的查询出来的数据 - 手动构建一张表
> 
> ```
>
> ```mysql
> drop procedure emp_pro;
> delimiter //
> create procedure emp_pro(in eid int(7))
> begin
> 	-- insert into 表名 select 语句;
> 	insert into s_emp_pro(first_name,salary) select first_name,salary from s_emp where id >= eid;
> end //
> delimiter ;
>       
> -- 如果参数是out修饰的,必须只能传入全局变量[作用就是来保存存储过程一些执行结果的值]
> call emp_pro(20);
> 
> ```



## 练习 - 带事务

```mysql
-- 转账功能,同时成功,同时失败.
drop procedure transfer_pro;
delimiter //
create procedure transfer_pro(in sid int(7),in tid int(7),in money double(7,2),in st int(7))
begin
	-- 定义一个局部变量 - 展示信息
	declare msg varchar(20) default '0';
	-- 手动开启一个事务
	start transaction;
	update account set balance = balance-money where id = sid;
	-- 模拟一个异常
	if st=1 then
		set msg = 'sorry,转账失败了!';
		rollback;
	else
		update account set balance = balance+money where id = tid;
		-- 手动提交一个事务
		set msg = 'good,转账成功了!';
		commit;
	end if;
	select msg;
end //
delimiter ;

-- st=1,进入到异常块的.
call transfer_pro(1,2,1000.0,1);

-- st=2,正常执行转账的业务功能
call transfer_pro(1,2,1000.0,2);

```



# 语句使用

## 条件分支语句

> 1. if .. then .. elseif .. then ..elseif.. then ... else.. end if;
>
> ```mysql
> drop procedure if_pro;
> delimiter //
> create procedure if_pro(in a int)
> begin
> 	declare msg varchar(20) default '';
> 	if a>=90 then
> 		set msg = '优秀';
> 	elseif a>=80 then
> 		set msg = '良好';
> 	else
> 		set msg = '及格';
> 	end if;
> 	select msg;
> end //
> delimiter ;
> 
> call if_pro(85);
> 
> ```
>
> 2. case .. when .. then..else... end case
>
> ```mysql
> drop procedure case_pro;
> delimiter //
> create procedure case_pro(in a int)
> begin
> 	declare msg varchar(20) default '';
> 	case a
> 		when 1 then
> 			set msg='1';
> 		when 2 then
> 			set msg = '2';
> 		else
> 			set msg = '3';
> 	end case;
> 	select msg;
> end //
> delimiter ;
>       
> call case_pro(2);
> 
> ```

## 循环语句

> 1. while .. do .. end while
>
> ```mysql
> drop procedure while_pro;
> delimiter //
> create procedure while_pro(in x int,out result int)
> begin
> 	declare i int default 1;
> 	declare sums int default 0;
> 	while i<=x do
> 		set sums = sums + i;
> 		set i = i+1;
> 	end while;
> 	-- result修饰成out =return result
> 	set result = sums;
> end //
> delimiter ;
>       
> -- out修饰的,只能传入全局变量
> call while_pro(100,@result);
>       
> select @result;
> 
> ```

> 2. loop ... end loop
>
> 类似于while(true)
>
> ```mysql
> -- 求1~x结束
> drop procedure loop_pro;
> delimiter //
> create procedure loop_pro(in x int,out result int)
> begin
> 	declare i int default 1;
> 	set result=0;
> 	success:loop
> 		if i>x then
> 			-- 打破循环
> 			-- iterate success; -- conintue
> 			leave success; 
> 		end if;
> 		set result = result + i;
> 		set i = i + 1;
> 	end loop;
> end //
> delimiter ;
> 
> call loop_pro(100,@result);
> select @result;
> 
> ```
>
> 3. repeat .. until .. end repeat
>
> 类似于java-> do..while
>
> ```mysql
> drop procedure repeat_pro;
> delimiter //
> create procedure repeat_pro(in x int)
> begin
> 	repeat
> 		set x = x + 1;
> 		select x;
> 	until x>0
> 	end repeat;
> end //
> delimiter ;
>       
> call repeat_pro(-1);
> 
> ```

# 总结函数和存储过程的区别

> 1. 定义函数的时候,必须指定returns 返回类型.定义存储过程的时候不需要使用returns 返回类型
> 2. 函数体中必须要有return语句+返回结果,但是存储过程可以没有return语句
> 3. 函数的调用用select关键字,存储过程调用使用call关键字
> 4. 存储过程更加侧重于封装以及预编译sql语句,提高sql语句的复用性.
> 5. 存储过程中是使用in来接受参数,out来返回结果.

# 触发器

> 在mysql中,当我们执行一些操作的时候[比如dml操作 - 触发器能够触发的事件],一旦事件被触发,就会执行一段程序
>
> 触发器的本质上就是一个特殊的存储过程

## 分类

> 1. after触发器 - 在触发条件之后执行
> 2. before触发器 - 在触发条件之前执行

## 语法

```mysql
-- 删除触发器
drop trigger 触发器名称;
delimiter //
create trigger 触发器名称
触发时机(after,before) 触发事件(insert,delete,update) on 触发器事件所在的表名
for each row
-- 触发器事件程序
begin
	
end //
delimiter ;

```

## 体验

> 1. 删除account表中的任意一条数据的时候[触发事件],并且把这条数据放入到备份表中acc_copy
>
> ```mysql
> -- 创建备份表
> create table acc_copy select * from account where 1=2;
> 
> ```
>
> ```mysql
> drop trigger acc_tri;
> delimiter //
> create trigger acc_tri
> after delete on account
> for each row
> begin
> 	-- old.列名 - 刚刚删除的那条数据
> 	insert into acc_copy values(old.id,old.name,old.balance);
> end //
> delimiter ;
> 
> -- 触发器是遇到触发的事件才会执行的.不是我们手动去调用的.
> delete from account where id=1;
> 
> 
> mysql> select * from account;
>    +----+------+----------+
>    | id | name | balance  |
>    +----+------+----------+
>    |  2 | tom  | 10000.00 |
>    |  3 | jack |  9000.00 |
>    |  4 | ss   |  9000.00 |
>    |  5 | ss2  |  9000.00 |
>    +----+------+----------+
>    4 rows in set (0.00 sec)
> 
>    mysql> select * from acc_copy;
>    +----+-------+---------+
>    | id | name  | balance |
>    +----+-------+---------+
>    |  1 | admin | 8000.00 |
>    +----+-------+---------+
>    1 row in set (0.00 sec)
> 
> ```
>
> 2. 如果表和表之间存在约束关系,比如1:N,无论是在drop或者delete的时候.必须要先删除多的一方,然后再删除一的一方
>
> 必须先把一的一方的所有子记录全部删除完毕,才能删除一的一方
>
> ```mysql
> drop table tri_cus;
> drop table tri_ord;
> 
> create table tri_cus(
> 	id int(7) primary key,
>   name varchar(20)
> );
> insert into tri_cus values(1,'admin');
> insert into tri_cus values(2,'tom');
> 
> create table tri_ord(
> 	id int(7) primary key,
>   ord_no varchar(20),
>   cus_id int(7),
>   foreign key(cus_id) references tri_cus(id)
> );
> insert into tri_ord values(1,'1001',1);
> insert into tri_ord values(2,'1002',1);
> insert into tri_ord values(3,'1003',2);
> 
> -- 外键约束存在的原因
> mysql> delete from tri_cus where id=1;
> ERROR 1451 (23000): Cannot delete or update a parent row: a foreign key constraint fails (`j03s`.`tri_ord`, CONSTRAINT `tri_ord_ibfk_1` FOREIGN KEY (`cus_id`) REFERENCES `tri_cus` (`id`))
> 
> 
> -- 删除客户之前,应该先删除订单
> drop trigger cus_tri;
> delimiter //
> create trigger cus_tri
> before delete on tri_cus
> for each row
> begin
> 	-- 先去删除这个客户的子记录
> 	delete from tri_ord where cus_id=old.id;
> end //
> delimiter ;
> 
> mysql> delete from tri_cus where id=1;
> 
> mysql> select * from tri_cus;
> +----+------+
> | id | name |
> +----+------+
> |  2 | tom  |
> +----+------+
> 1 row in set (0.00 sec)
> 
> mysql> select * from tri_ord;
> +----+--------+--------+
> | id | ord_no | cus_id |
> +----+--------+--------+
> |  3 | 1003   |      2 |
> +----+--------+--------+
> 1 row in set (0.00 sec)
> 
> ```

> 3. oracle数据库中还会多一个自检约束[自定义检测],check约束
>
> 但是Mysql中是不存在check约束.
>
> ```mysql
> -- oracle - 使用check约束
> create table ckt(
> 	id int(7) primary key,
>   age int(1) check(age>0 and age<100)
> )
> -- 插入数据的时候,age插入的值只能在check约束自定义的规则之内,否则插入失败.
> 
> -- 面试题 - mysql中如何实现oracle数据库中的check自检约束的功能.
> -- 在执行insert的时候,对某些数据进行进一步的校验 - 触发器
> create table ckt(
> 	id int(7) primary key,
>   age int(1)
> );
> 
> drop trigger ckt_tri;
> delimiter //
> create trigger ckt_tri
> before insert on ckt
> for each row
> begin
> 	-- 对新插入的数据进行一个校验
> 	-- old.列还是使用new.old列 - 看该列在表中有没有.
> 	if new.age<18 then
> 		signal sqlstate 'HY000' set message_text='age不合法';
> 	end if;
> end //
> delimiter ;
> 
> insert into ckt values(1,20);
> 
> insert into ckt values(2,17);
> mysql> insert into ckt values(2,17);
> ERROR 1644 (HY000): age不合法
> 
> ```



# 数据库优化

- 分表/分库

- 主从复制

- ***sql优化操作***

  - select查询列不要出现 *

    ```mysql
    尽量还是select 具体的列名称,
    select * 效率低于select 具体的列 - "争议"
    
    为了数据的安全.
    
    ```

  - 不鼓励使用order by语句

    ```mysql
    mysql中的order by性能是及其低下,业务中如果遇到排序.
    建议是先把数据取出来放入到java内存中 - 集合中.
    
    再通过java代码的集合排序的各种方式来进行对数据的排序.
    
    ```

  - 查询唯一的非索引列的值的时候,配合limit 1语句,避免全表扫描.

  - 索引优化

## sql优化 - 至少背10条

1. MySQL如何执行区分大小写的字符串比较？

   ```mysql
   select * from s_emp where binary first_name = 'Carmen';
   
   ```

2. 应尽量避免在 where 子句中使用!=或<>操作符，否则将引擎进行全表扫描。

3. 对查询进行优化，应尽量避免全表扫描，首先应考虑在where及order by涉及的列上建立索引。

4. 应尽量避免在where子句中对字段进行not null值判断，否则将导致引擎放弃使用索引而进行全表扫描

5. 尽量避免在where子句中使用or来连接条件，否则将导致引擎放弃使用索引而进行全表扫描，如：

​    select id from t where num=10 or num=20
​	可以这样查询：
​	*sel*ect id from t where num=10
​	*union* 
​    select id from t where num=20

6. 下面的查询也将导致全表扫描：(不能前置百分号)

   select id from t where name like ‘c%’;//走索引.

7. not in也要慎用，否则会导致全表扫描，如：

   ```mysql
   对于连续的数值，能用between就不要用in了：
   select id from t where num between 1 and 3
   
   ```

8. 尽量避免在where子句中对字段进行表达式操作，这将导致引擎放弃使用索引而进行全表扫描。如：
   select id from t where num/2=100

   ```mysql
   	应改为:
    select id from t where num=100*2
   
   ```

9. 应尽量避免在where子句中对字段进行函数操作，这将导致引擎放弃使用索引而进行全表扫描。如：

10. 不要在where子句中的“=”左边进行函数、算术运算或其他表达式运算，否则系统将可能无法正确使用索引。

11. 在使用索引字段作为条件时，如果该索引是复合索引，那么必须使用到该索引中的第一个字段作为条件时才能保证系统使用该索引，否则该索引将不会被使用，并且应尽可能的让字段顺序与索引顺序相一致。

    - 遵循最左原则.

12. 很多时候用exists代替in[查询性能很低]是一个好的选择：

​        select num from a where num in(select num from b)
​     	用下面的语句替换：
​     	***select num from a where exists(select 1 from b where num=a.num)***

14. 并不是所有索引对查询都有效，SQL是根据表中数据来进行查询优化的，当索引列有大量数据重复时，SQL查询可能不会去利用索引，如一表中有字段sex，male、female几乎各一半，那么即使在sex上建了索引也对查询效率起不了作用 - 索引有效 - 数据控制30%;

15. 索引并不是越多越好，索引固然可以提高相应的 select 的效率，但同时也降低了 insert及update的效率，因为insert或update时有可能会重建索引，所以怎样建索引需要慎重考虑，视具体情况而定。***一个表的索引数最好不要超过6个***，若太多则应考虑一些不常使用到的列上建的索引是否有 必要。

16. 应尽可能的避免更新 clustered [聚簇]索引数据列，因为clustered索引数据列的顺序就是表记录的物理存储顺序，一旦该列值改变将导致整个表记录的顺序的调整，会耗费相当大的资源。若应用系统需要频繁更新clustered索引数据列，那么需要考虑是否应将该索引建为clustered索引。

17. 尽量使用数字型字段，若只含数值信息的字段尽量不要设计为字符型，这会降低查询和连接的性能，并会增加存储开销。这是因为引擎在处理查询和连接时会逐个比较字符串中每一个字符，而对于数字型而言只需要比较一次就够了。

18. 尽可能的使用**varchar**/nvarchar代替**char**/nchar，因为首先变长字段存储空间小，可以节省存储空间，其次对于查询来说，在一个相对较小的字段内搜索效率显然要高些。

19. 任何地方都不要使用select * from t，用具体的字段列表代替“*”，不要返回用不到的任何字段。

20. 避免频繁创建和删除临时表，以减少系统表资源的消耗。

    ```mysql
    临时表和表变量 - 推荐表变量来代替临时表的用法.
    
    ```

21. 临时表并不是不可使用，适当地使用它们可以使某些例程更有效，例如，当需要重复引用大型表或常用表中的某个数据集时。但是，对于一次性事件，最好使用导出表。

22. 在新建临时表时，如果一次性插入数据量很大，那么可以使用select into代替create table，避免造成大量log，以提高速度；如果数据量不大，为了缓和系统表的资源，应先create table，然后insert。

23. 如果使用到了临时表，在存储过程的最后务必将所有的临时表显式删除，先truncate table，然后drop table，这样可以避免系统表的较长时间锁定。

24. 尽量避免使用游标，因为游标的效率较差，如果游标操作的数据超过1万行，那么就应该考虑改写。

25. 尽量避免大事务操作，提高系统并发能力。











