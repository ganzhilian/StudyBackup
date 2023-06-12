---
title: part02_mysql_ddl_dml
date: 2021-08-31 00:09:40
categories: mysql
tags: mysql
---

# DDL

> DDL[Data Definition Language] - 数据定义语言
>
> 涉及到的sql命令如下:
>
> 1. **create命令 - 创建**
>
> **创建表(table)**,视图(view),用户(user),函数(function),存储过程(procedure),数据库[database],约束(constraints)
>
> 以上构建的都是属于数据库DB的对象/单位 - DB的基本单位是table
>
> 2. **drop命令 - 删除**
>
> **删除表(table),视图(view),用户(user),函数(function),存储过程(procedure),数据库[database]**,约束[constraints]
>
> 但是不能够删除表中具体行记录,是由delete命令
>
> 3. alter命令 - 修改 - 了解即可
>
> 修改列名,列的类型,修改约束.列的类型 - 使用情况很少
>
> 4. rename to - 修改表名
> 5. comment - 创建列的时候加上注释
> 6. truncate - 清空整张表

# ORM思想

> **Obejct-Relational-Mapping - 对象关系映射**
>
> 需要用到OO思想[第一步找出对象,分析对象的属性,分析对象的功能.考虑对象之间的关系]来进行实体类的设计 - 数据库的设计.
>
> **掌握目标 - 看到实体类-表结构.看到表结构-实体类.**
>
> 映射规则:
>
> 1. 实体名 - 表名
> 2. 对象标识(id) - 主键(id) -primary key - [特点,非空且唯一]
> 3. 普通属性 - 普通列[推荐俩者的名称保持一致.需要注意的是如果列是多个名称[匈牙利] - 小驼峰]
> 4. 对象之间的关系 - 外键(foreign key) - 表与表之间是通过**外键**来进行关联[进行关联查询的基础]
>
> **通常情况下某张表的外键是指向另外一张表的主键的.**

## 对象之间的关系

> 分析一个"小米商城",利用OO思想,找出业务中的关键对象
>
> - 登录,注册 - "用户User对象" - (id,phone,password,login_time,login_ip)
> - 个人中心 - 其中有一块是用来负责展示用户的详情UserInfo
> - 产品
> - 产品详情
> - 购物车 - id,商品id,商品名称,商品单价,num,小计算,总价,备注
> - 订单 - 一个用户拥有多个订单,一个订单只能属于一个用户
> - 产品分类表
>
> 1:N
>
> N:N

### 1:1

> User和UserInfo: 一个用户匹配一个用户详情,一个用户详情仅属于一个用户
>
> 强调的是平等性 - 表设计中,外键可以出现在任何一方表中.
>
> 原则 - 能用单向关联解决的业务就不要使用双向关联.

> 1. java实体类
>
> ```java
> 现阶段的思想 - 实体类中没有外键的概念.
> -- 关系具有方向性
> -- 单向关联 - 关系的维护仅仅在其中一方存在.
> -- 根据第一表进行设计
> public class User{
>   private Integer id;
>            
>   private String phone;
>            
>   private String password;
>            
>   //一个用户拥有一个用户详情对象
>   private UserInfo userInfo;
>            
>   //现阶段 - 千万不要写成 - 对象里面是没有外键的概念的
>   //private Integer user_info_id;
> }
>          
> public class UserInfo{
>   private Integer id;
>            
>   private String header;
>            
>   private Gender sex;
> }
>    
> 
> -- 单向关联 - 第二张表
> public class User{
>   private Integer id;
>   
>   private String phone;
>               
>   private String password;
>  
> }
> 
> public class UserInfo{
>   private Integer id;
>   
>   private String header;
>               
>   private Gender sex;
>               
>   private User user;
> }
> 
> -- ORM思想的精髓 - 如何进行映射以及为什么需要这种思想???
>   
> 未来的操作 - java代码去连接db - 查询user表中所有的数据 -> List<User>
> 取出每一行数据 - 创建一个user对象[数据在内存中的载体.对象特殊类型的数组]
> 
> //db中的每一行数据 - java代码来进行获取
> List<User> users = new ArrayList<>();
> 循环体中{
>   //jdbc代码
>   Integer id = rs.getInt(1);
>   String phone = rs.getString(2);
>   String passwrod = rs.getString(3);
>   
>   //使用对象来封装每一行数据
>   User user = new User(id,phone,password);
>   users.add(user);
> }
> 
> //未来学习了前端 - 后台list集合中的数据显示到网页上.
> 
> 
> -- 双向关联 - 彼此之间相互维护
>  public class User{
>   private Integer id;
>   
>   private String phone;
>               
>   private String password;
>                 
>   private UserInfo userInfo;
>  
> }
> 
> public class UserInfo{
>   private Integer id;
>   
>   private String header;
>               
>   private Gender sex;
>               
>   private User user;
> }
> 
> 
> ```
>
> 2. 表设计
>
> ![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/onetoone01.png) 

> ​		![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/onetoone02.png) 

### 1:N

> 一个用户对象和购物车对象
>
> 一个用户可以拥有多个购物车对象,但是一个购物车对象只能属于一个用户.
>
> **外键是存储在多的一方**
>
> `实体类`
>
> ```java
> -- 单向关联01
> public class User{
> //普通属性
> 
> //一个用户拥有多个购物车
> private Set<Cart> carts;
> }
> 
> public class Cart{
> //...
> 
> }
> 
> -- 单向关联02
> public class User{
> //普通属性
> 
> }
> 
> public class Cart{
> //...
> 
> //一个购物车只能属于一个用户
> private User user;
> 
> }
> 
> 
> -- 双向关联
> public class User{
> //普通属性
> 
> //一个用户拥有多个购物车
> private Set<Cart> carts;
> }
> 
> public class Cart{
> //...
> 
> private User user;
> }
> ```
>
> `表结构`
>
> User表
>
> | id   | phone | password |      |
> | ---- | ----- | -------- | ---- |
> | 1    | 110   | 123      |      |
> | 2    | 114   | 123      |      |
> | 3    | 115   | 123      |      |
>
> 购物车Cart表
>
> | 2    | 2          | mi02         | 1       |      |      |
> | ---- | ---------- | ------------ | ------- | ---- | ---- |
> | 1    | 1          | mi01         | 1       |      |      |
> | 3    | 3          | mi03         | 2       |      |      |
> | 4    | 4          | mi04         | 2       |      |      |
> | id   | product_id | product_name | user_id |      |      |
> | 5    | 5          | mi05         | 2       |      |      |

### N:N

> 需要拆分成俩个一对多

> 一个学生可以选择多门课程,
>
> 一门课程可以被多个学生去选择.
>
> 比如:客户表,商品表,购买表
>
> 一个客户拥有多个商品
>
> 一个商品可以被多个用户购买.
>
> customer表
>
> | id   | cname |      |
> | ---- | ----- | ---- |
> | 1    | admin |      |
> | 2    | tom   |      |
>
> product表
>
> | id   | pname  | price  |
> | ---- | ------ | ------ |
> | 1    | mac    | 100000 |
> | 2    | xiaomi | 8000   |
> | 3    | huawei | 8000   |
> | 4    | 联想   | 7000   |

> 需要建立第三方表,来确定customer表和product表的关系 . **N:N->拆分成俩个1:N**
>
> purcase表 - 购买表 
>
> 采用的是联合主键[**组合主键]** 
>
> **主键-primary key - 非空且唯一.作用:用来确定唯一元组[行]**
>
> 所谓的"俩个1:n" - **customer表和purcase表**以及**product表和purcase表**
>
> 1. 一个客户拥有多条购买记录[1:N]
> 2. 一个产品拥有多条被购买记录[1:N]
>
> 主键列未必是单列[常见的id列] , N:N场景可以采取联合主键,主键列由俩列共同组成**(customer_id,product_id)**
>
> **外键列通常情况是引入另外一张表的主键列.在该表中.外键列有2列**
>
> **分别是customer_id 引用了customer表中的id列**
>
> **product_id引用了product表中的id列**
>
> | customer_id | product_id |      |
> | ----------- | ---------- | ---- |
> | 1           | 1          |      |
> | 1           | 2          |      |
> | 2           | 1          |      |
> | 2           | 3          |      |
> | 2           | 4          |      |

> 另一种情况关于主键的选择.第三方表[用来维护关系的表],开发的时候,还是推荐给这张自己一个**单独的主键列**
>
> | id   | customer_id | product_id |
> | ---- | ----------- | ---------- |
> | 1    | 1           | 1          |
> | 2    | 1           | 2          |
> | 3    | 2           | 1          |
> | 4    | 2           | 3          |
> | 5    | 2           | 4          |

### 自关联

> 当前表中的外键列自关联到了自己表中的主键列

> s_emp
>
> | id   | first_name | manager_id[上司id] |
> | ---- | ---------- | ------------------ |
> | 1    | Carmen     |                    |
> | 2    | Mark       | 1                  |
> | 3    | tom        | 1                  |
> | 4    | admin      | 2                  |
> | 5    | jack       | 2                  |
>
> 

## 分析评论业务

> 以"抖音"
>
> User - Viedo
>
> 关注评论功能.一个用户可以评论多个视频.一个视频可以被多个用户进行评论  - N:N

> User表
>
> | id   | phone | password | username |
> | ---- | ----- | -------- | -------- |
> | 1    | 110   | 123      | 吴签     |
> | 2    | 114   | 123      | 霍针     |
> | 3    | 115   | 123      | 孙岩     |

> Viedo表
>
> 一个用户可以发布多个视频,但是一个视频只能由一个用户去发布
>
> | id   | 视频标题 | 视频点赞量 | user_id |      |      |
> | ---- | -------- | ---------- | ------- | ---- | ---- |
> | 1    | 吴签     | 100万+     | 2       |      |      |
> | 2    | 霍针     | 100万+     | 1       |      |      |
> | 3    | 杨晓轩   | 10+        | 3       |      |      |
> | 4    | 孙岩     | 10+        | 3       |      |      |
> | 5    | 闫昊     | 10+        | 3       |      |      |

> 评论表 - 重点 体现出谁评论了哪个视频即可.comment
>
> 外键列是可以为空的
>
> id=5的人并不是直接对视频进行评论的 - 属于第二层评论,他是对id=1的这个人进行评论的
>
> 若comment_id为null,说明这个人是直接对视频进行评论的,属于业务的第一级评论
>
> | id   | 视频id | user_id | 评论内容              | comment_pid |      |
> | ---- | ------ | ------- | --------------------- | ----------- | ---- |
> | 1    | 1      | 3       | 很羡慕                |             |      |
> | 2    | 1      | 4       | 渣男                  |             |      |
> | 3    | 1      | 5       | 渣男                  |             |      |
> | 4    | 2      | 1       | 彼此彼此              |             |      |
> | 5    | 2      | 5       | 你还是人么?你这样认为 | 1           |      |

![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/dataBase/j03s_mysql/note/imgs/reply.png) 

除了评论还有回复....

第一层 - 属于评论表的数据

第二层 - 对第一级的评论继续评论

第三层 - 对第二层及其以后的评论/回复 - 叫做回复.

> 回复表
>
> 一条评论可以引起多个回复 - reply
>
> | id   | comment_id | content        | replay_id | user_id |      |
> | ---- | ---------- | -------------- | --------- | ------- | ---- |
> | 1    | 5          | 难道你不羡慕吗 |           | 1       |      |
> | 2    | 5          | 我羡慕         | 1         | 2       |      |
> |      |            |                |           |         |      |
> |      |            |                |           |         |      |
> |      |            |                |           |         |      |
>
> 结构:
>
> 一条视频发出来id=5的视频
>
> user_id=3: 很羡慕
>
> ​	user_id=5:你还是人么?你这样认为
>
> ​		user_id=1回复了user_id=5:难道你不羡慕吗
>
> ​		user_id=2回复了user_id=1:我羡慕

# 表设计-三大范式

> 表设计一定**尽可能去遵守三大方式**.作用:指导db的设计更加合理化,避免数据的冗余.
>
> 笔试题中简答题.
>
> 1. **1NF(第一范式):保证列的原子性.列不可再分割.**
>
> ```java
> s_emp(id,name);
> 但是发现外国的name比较特殊.发现name列是可再分割的.不符合1NF.需要对name进行切割
> s_emp(id,first_name,last_name);
> 
> address只需要包含省市区
> school(id,name,address);
> 1  苏州大学  		江苏省苏州市工业园区
> 2  常州大学  		江苏省常州市武进区
> 3  苏州科技学院  江苏省苏州市高新区
> 
> school(id,name,province,city,area);
> ```
>
> 2. **2NF(第二范式):基于1NF,不存在非关键列部分依赖于关键列.非关键列要全部依赖于关键列**
>
> `关键列 - 主键列;非关键列 - 非主键列`
>
> ```mysql
> -- student,course表 - 所有的信息合并到一张表了.
> --1. student信息以及course信息都会存在数据的冗余性
> -- 如何确定主键列[单列,组合形式] - 能够确定唯一的一条行记录 - sid,cid
> 
> -- db设计中,并不是表的数量或者列的数量越少越好的 - 满足三大范式
> -- 关键列(sid,cid),非关键列(sname,cname)
> -- ①(sid,cid) -> 唯一确定 sname
> 		 实际上如果关键列中的部分列sid就可以确定了sname的值
> 		 sname只需要由关键列(sid,cid)中的部分列[也就是sid列]就可以确定了.
> 		 存在了非关键列sname部分依赖于关键列,违背了2NF
> -- ②(sid,cid) -> 唯一确定 cname
> student_course(sid,sname,cid,cname)
> sid  sname  cid  cname
> 1     tom    1    java
> 1     tom    2    python
> 2     admin  1    java
> 2     admin  3    jack
> 
> 为了满足2NF
> student(sid,sname);//学生表 sid->sname
> course(cid,cname);//课程表  cid->cname
> sc(sid,cid,score);//选课表  (sid,cid)-> score
> ```
>
> 3. 3NF(第三范式):基于2NF.不存在非关键列传递依赖于关键列.所有的非关键列要直接依赖于关键列
>
> ```mysql
> student(id,sname,uno,uname,address)
> 1  tom   x1001  苏州大学 江苏省
> 2  jack  x1002  社会大学 东北省
> 3  james x1001  苏州大学 江苏省
>    
> 关键列只有id列
> ①关键列id可以直接确定一个唯一的uno的
> ②uno又可以直接确定唯一的一个uname
> ③给定一个学生的id,也是可以确定一个唯一的uname
>    
> id -> uno -> uname  => id -> uname
> uname是间接被id确定下来的-uname是间接依赖[通过一个uno传递依赖]于关建列id.
> 违背了3NF,存在了非关键列传递依赖于关键列.
>    
> 所有的非关键列要直接依赖于关键列 - 所有的非关键列应该是直接被关键列来确定的.
>    
> 修改 - 拆分
> student(id,sname,uid);//uid就是大学的id
> id -> uid(引用university表中的id列)
>    
> university(id,uno,uname,adress)
> uid->uname
> ```



# 建表

```mysql
CREATE TABLE 表名(
  列名 数据类型[(长度)],
  列2  数据类型[(长度)],
  ...
  ...
  列  数据类型[(n)]
)ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- mysql5.7及其以上可以省略了ENGINE=InnoDB DEFAULT CHARSET=utf8;
-- 前提是my.ini文件配置好了
-- 指定我们的数据的存储引擎InnoDB,表编码是utf8
```

## 数据类型

> 列的数据类型就是用来限制你将来插入数据到该列的限制.该列是什么类型,就需要插入什么类型的数据

> 1. 数字相关的
>
> a. 整数 - int和bigint
>
> b. 小数 - float,double[使用最多],decimal
>
> 笔试题中:TINYINT(1个字节)或者smallint(2个字节)
>
> 2. 字符串相关的 - mysql中字符串是用单引号表示的
>
> a. char(n)    -  固定长度 - 0-255 bytes[ascill]
>
> b. varchar(n) - 可变长度 - 0-65535 bytes[unicode的范围]
>
> c. text - 长文本数据
>
> 笔试题中:mysql中char和varchar的区别
>
> ```MYSQL
> 数字5代表的是该列可以存储的字符串的个数.不是代表字节数
> char(5) - 定长字符的意思是即时该列仅仅保存一个字母'a',那么在内存中,char的实际消耗的长度就是5个 - "浪费"
> 					对char类型的列的读写操作效率高于varchar
> 					
> varchar(5) - 可变长度[最大可保存的字符串的长度],实际存储了一个字母'a'.实际也就只消耗了一个1长度 - "节约了内存" - 按需分配.
> 
> 因此,开发中尽可能字符串使用varchar,而不使用char.
> ```
>
> 3. 日期
>
> a. date -> 即使保存的是年月日时分秒,查询出来的也仅仅只会显示到**年月日**
>
> b. datetime->年月日时分秒.即使保存的数据仅仅是年月日.但是查询出来的时候,可以显示年月日时分秒

## 建表demo

```mysql
drop table tbl_product;
create table tbl_product(
	id int(3),
  name varchar(20),
  create_time date,
  price double(10,2)
);

-- 分析括号中的参数
-- int(3)-并不会影响该列可插入数据的一个范围.范围仍然是int型的范围
-- 为了看出来int(3),修改一下建表语句
-- int(13)想要看出来效果,这个列一定要加上zerofill,不足位数的用0填充
create table tbl_product(
	id int(13) zerofill,
  name varchar(20),
  create_time date,
  price double(7,2)
);
mysql> insert into tbl_product values(1234,'tom',now(),123.45);
mysql> select * from tbl_product;
+---------------+------+-------------+--------+
| id            | name | create_time | price  |
+---------------+------+-------------+--------+
| 0000000001234 | tom  | 2021-08-19  | 123.45 |
+---------------+------+-------------+--------+
总结:int(n)此处的n不会影响插入到该列的数据的大小.那么n是配合zerofill来进行的.查询的时候,不足n位的数字全部用0填充.

-- 修改表结构-测试varchar(n)中的n - 个数
create table tbl_product(
	id int(7),
  name varchar(2),
  create_time date,
  price double(10,2)
);
-- 如果varchar(n)设置成了2,但是插入了'tom',报错
-- ERROR 1406 (22001): Data too long for column 'name' at row 1
mysql> insert into tbl_product values(1,'tom',now(),123.45);
-- 总结:varchar(n) - 中的n是代表的是字符的个数[一个汉字就是代表一个]

-- double(m,n)
-- m代表的是总长度,n代表的是精度 - double(7,2)
create table tbl_product(
	id int(7),
  name varchar(20),
  create_time date,
  price double(7,1)
);
mysql> insert into tbl_product values(2,'admin','2021-10-01',123456.56789);
-- double(7,2) -> 7就是总的长度,2代表的精度[四舍五入] - 123456.56789 
-- 插入失败了 - Out of range value for column 'price' at row 1
-- 插入失败的原因是123456.57 -> 这个结果已经超过了总的长度7了.

-- 修改double(7,1) -> 123456.56789 -> 123456.6 -> 仍然没有超过7这个总的长度的. - 可以被插入进去的
```

```mysql
mysql> show tables;
mysql> desc tables;
-- 查看建表语句
mysql> show create table tbl_product

 CREATE TABLE `tbl_product` (
   `id` int(7) DEFAULT NULL,
   `name` varchar(20) DEFAULT NULL,
   `create_time` date DEFAULT NULL,
   `price` double(3,2) DEFAULT NULL
 ) ENGINE=InnoDB DEFAULT CHARSET=utf8 

```

## 删除表

> 1. 在不考虑有外键因素的情况下,可以直接删除表的
>
> ```mysql
> DROP TABLE 表名;
> 
> ```

# 约束constraint

> 作用:在插入到表中列的数据进行进一步的限制

## 约束类型

> 1. 主键约束[primary key] - 非空且唯一.用来确定唯一的一行记录 - pk
> 2. 非空约束[not null] - 非空 - 该列不能为null.必须插入值或者给定一个默认值 - nn
> 3. 唯一约束[unique] - 唯一性 - nq
> 4. 外键约束[foreign key] - 表与表之间的关系是通过外键来进行维护的.外键列一般是对应某张表的主键列.可以允许为null  - fk

### 列级添加

> 优点:比较简单,缺点:不能给约束取名字
>
> 语法:
>
> ```mysql
> CREATE TABLE 表名(
> 列名 数据类型[(长度)] [约束类型1],
> 列2  数据类型[(长度)] [约束类型2],
> ...
> ...
> 列  数据类型[(n)]
> )ENGINE=InnoDB DEFAULT CHARSET=utf8;
> 
> ```
>
> ```mysql
> drop table tbl_product;
> create table tbl_product(
> 	id int(7) primary key comment '产品的id',
> -- 给某列增加俩个约束,用空格隔开
> name varchar(20) not null unique comment '产品的名称',
> -- 如果该列没有插入数据,则使用default给定的默认值
> create_date date not null default now(),
> -- 非空
> price double(7,2) not null
> );
> 
> -- 死记住
> -- 如果上面的create_date使用的是date,给定默认值的时候,是不能直接使用now()函数的.如果想要给定默认值是now(),那么这个列必须是datetime
> -- 修改date类型列的默认值
> drop table tbl_product;
> create table tbl_product(
> 	id int(7) primary key comment '产品的id',
> -- 给某列增加俩个约束,用空格隔开
> name varchar(20) not null unique comment '产品的名称',
> -- 如果该列没有插入数据,则使用default给定的默认值
> create_date date not null default '2019-01-01',
> -- 非空
> price double(7,2) not null
> );
> 
> -- 演示primary key
> insert into tbl_product(id,name,price) values(1,'tom',123.567);
> 
> mysql> select * from tbl_product;
> +----+------+-------------+--------+
> | id | name | create_date | price  |
> +----+------+-------------+--------+
> |  1 | tom  | 2019-01-01  | 123.57 |
> +----+------+-------------+--------+
> 
> -- 继续插入一个id也是1的值
> insert into tbl_product(id,name,price) values(1,'admin',123.567);
> ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
> -- 违背了primary key主键约束的规则 - 重复了.
> 
> -- not null
> -- 没有给price插入值,但是该列存在一个not null非空约束
> insert into tbl_product(id,name) values (2,'tom');
> -- ERROR 1364 (HY000): Field 'price' doesn't have a default value
> 
> -- unique 唯一性,表中已经存在了一行记录是tom
> insert into tbl_product(id,name,price) values(2,'tom',12.3);
> -- name字段/列值重复了.
> ERROR 1062 (23000): Duplicate entry 'tom' for key 'name'
> 
> ```



### 表级添加

> 优势可以给约束命名,记住列级添加是不支持给列取名称的
>
> not null非空约束以及default仅仅是支持列级添加的方式
>
> ```mysql
> CREATE TABLE 表名(
> 列名 数据类型[(长度)],
> 列2  数据类型[(长度)],
> ...
> ...
> 列  数据类型[(n)],
> [constraint 约束名称] 约束类型(列)
> )ENGINE=InnoDB DEFAULT CHARSET=utf8;
> 
> ```
>
> ```mysql
> drop table tbl_product;
> create table tbl_product(
> 	id int(7) comment '产品的id',
> -- 给某列增加俩个约束,用空格隔开
> name varchar(20) not null comment '产品的名称',
> -- 如果该列没有插入数据,则使用default给定的默认值
> create_date date not null default '2019-01-01',
> -- 非空
> price double(7,2) not null,
> 
> -- 给列添加约束
> -- 约束命名 - 表名_列名_约束类型缩写
> constraint tbl_product_id_pk primary key(id),
> constraint tbl_product_name_uq unique(name)
> );
> 
> insert into tbl_product values(1,'tom',now(),1223.3);
> -- 让name违背了unique约束
> insert into tbl_product values(2,'tom',now(),14.56);
> 
> -- for key '约束名称',如果采用的是列添加.系统会默认给定一个约束名称[就是列的名称]
> -- 默认的约束名='name'和给定的约束名称'tbl_product_name_uq'
> -- 自定义的约束名称 - 精确定位错误的位置
> ERROR 1062 (23000): Duplicate entry 'tom' for key 'tbl_product_name_uq'
> 
> ```



## 外键约束

> foreign key - 简称fk.用来确定表之间的关系的.外键通常是是在多的一方.
>
> 1. 1:N或者1:1
>
> ```mysql
> drop table tbl_user;
> create table tbl_user(
>   id int(7) primary key,
>   username varchar(20) not null unique
> );
> insert into tbl_user values(1,'tom');
> insert into tbl_user values(2,'admin');
> 
> drop table tbl_video;
> create table tbl_video(
> 	id int(7) primary key,
>   title varchar(20),
>   likes int(7),
>   create_time datetime default now(),
>   -- 外键列命名方式 表名_另外一张表的引用列名[主键列]
>   -- 外键列的数据类型和长度和你引用的地方保持一致
>   tbl_user_id int(7),
>   -- 表级添加
>   constraint tbl_video_table_user_id_fk foreign key(tbl_user_id) references tbl_user(id)
> );
> -- 一旦创建了约束,插入数据的时候需要注意的是.
> -- 外键列是允许为空的.
> insert into tbl_video values(1,'学好myql',100,now(),1);
> insert into tbl_video values(2,'学好java',100,now(),1);
> insert into tbl_video values(3,'学习jdbc',100,now(),2);
> 
> -- 注意.既然已经定义了外键的约束.那么插入数据的时候,引用的外键列的值在另外一张表中一定要有行记录的
> -- tbl_user表中是不存在id=3的行记录的
> insert into tbl_video values(3,'mysql太简单了',100,now(),3);
> 
> -- 报错
> ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails (`j03s`.`tbl_video`, CONSTRAINT `tbl_video_table_user_id_fk` FOREIGN KEY (`tbl_user_id`) REFERENCES `tbl_user` (`id`))
> 
> ```

> 2. N:N
>
> ```mysql
> -- 评论表
> -- 一个用户可以对多个视频进行评论
> -- 一个视频可以被多个用户进行评论
> -- 拆分成俩个1:N
> 
> -- 组合主键 + 列级添加
> drop table tbl_comment;
> create table tbl_comment(
>   -- 组合主键 - 分别给俩个列都去定义了主键列 - 抛出错误ERROR 1068 (42000): Multiple primary key defined
>   -- 认为这张表中存在俩个主键,这是不允许的.
> 	user_id int(7) references tbl_user(id),
>   video_id int(7) references tbl_video(id),
>   content varchar(50),
>   -- 组合主键的正确定义方式
>   constraint tbl_comemnt_pk primary key(user_id,video_id),
>   -- 外键的列级添加方式
>   comment_id int(7) references tbl_comemnt(id)
> );
> 
> -- 评论表拥有独立的主键id + 外键约束(表级添加方式)
> drop table tbl_comment;
> create table tbl_comment(
> 	id int(7) primary key,
>   user_id int(7),
>   video_id int(7),
>   content varchar(50),
>   comment_id int(7),
>   constraint tbl_comment_user_id_fk foreign key(user_id) references tbl_user(id),
>   constraint tbl_comment_video_id_fk foreign key(video_id) references tbl_video(id),
>   constraint tbl_comment_id_fk foreign key(comment_id) references tbl_comment(id)
> );
> insert into tbl_comment(id,user_id,video_id,content) values(1,1,1,'好的,我会学会mysql的');
> insert into tbl_comment values(2,2,1,'好的,我会学会mysql的',1);
> 
> ```



## 建表补充

> 由一张表来构建另外一张表
>
> 1. 保留原表的表结构+数据
>
> ```mysql
> create table 表名 select语句;
> 
> ```
>
> ```mysql
> drop table video_copy;
> -- 保留原表中的所有的列的数据
> create table video_copy select * from tbl_video;
> 
> ```
>
> ```mysql
> drop table video_copy;
> create table video_copy select id,title from tbl_video;
> 
> ```
>
> 2. 保留原表的表结构 + 但是不需要原表的数据
>
> ```mysql
> drop table video_copy;
> create table video_copy select id,title from tbl_video where 1=2;
> 
> ```

# DML操作

> insert 插入数据
>
> delete 删除数据
>
> update 更新数据

## 插入语句

> 1. 语法一:给表中所有的列添加数据
>
> **插入的列值要和建表语句中列顺序以及列的类型以及列的约束高度保持一致.**
>
> ```mysql
> INSERT INTO 表名 VALUES(列值1,列值2,列值3,..,列值N)
> 
> ```

> 2. 语法二:给表中的指定列插入数据
>
> ```mysql
> INSERT INTO 表名(列名1,列名2) VALUES(列值1,列值2);
> 
> ```

## insert+select

> **insert into 表名 select 语句;**
>
> 把查询结果的数据插入到该表中
>
> ```mysql
> -- 统计视频的被评论的用户量,并且保存到另外一张表中 video_id,comment_num
> drop table comment_aly;
> create table comment_aly(
> 	 video_id int(7),
> comment_num int(7)
> );
> insert into comment_aly select video_id,count(user_id) from tbl_comment group by video_id;
> 
> ```

# auto_increment

> mysql中的主键的生成策略 - 自增长策略 - auto_increment[默认的是从1开始,步长为1]
>
> oracle中的主键的生成策略 - 自增长 - sequence
>
> 应用场景:以后插入的时候[除非是模拟数据],id肯定是后台自动生成 - 唯一性.
>
> ```mysql
> drop table comment_aly;
> create table comment_aly(
> id int(7) primary key auto_increment,
> 	 video_id int(7),
> comment_num int(7)
> );
> insert into comment_aly(video_id,comment_num) select video_id,count(user_id) from tbl_comment group by video_id;
> 
> -- mysql中提供了一个函数last_insert()
> -- 查看最后一个id的插入的数据
> mysql> select last_insert_id();
> 
> -- 还希望模拟数据的时候接着使用这个自增长
> -- 修改自增长的初始值.
> mysql> alter table comment_aly auto_increment=6;
> 
> -- 比如数据就是自己插入进去的
> insert into comment_aly(video_id,comment_num) values(1,3);
> 
> ```

## 补充

> hive+hbase[分布式数据库] - 大数据
>
> 数据分析业务
>
> 1. 数据的获取
>
> 1-1. 爬数据 - 爬虫 - 大公司都有反爬技术[前端的js算法是公开的.万一被破解了.需要重新制定算法]
>
> 爬虫工程师 = 低级的[网站没有反爬技术]和高级的[反爬]
>
> ```java
> 验证发展历程 - 为了阻止程序模拟用户进行登录的过程.
>    1. 早期是没有验证码 - 模拟用户登录的程序不断去尝试登录的过程.
>       - 网站可能卡死
>   
>    2. 简单的文字或者数字框/弹出框 1+1=?  1+2=?
>   
>    3. 图片验证码 - 图像处理技术-识别出图像中的文字的
>    图片中文字尽可能做得非常非常非常模糊,颠倒 - 程序识别不出来.
>   
>    4. 手机验证/滑动块 - 区分程序在操作还是人操作
> 
> ```
>
> 1-2. 日志文件 - 比如后台会记录用户的行为轨迹 - 透露出用户的爱好.

> 2. 数据的清洗
>
> 日志文件/爬过来的数据 [字段比较多的.需要在有**业务经验**的基础之上]需要对若干个字段进行进一步的筛选工作.
>
> 把关心的和业务相关的字段清洗出来.
>
> 3. 数据的分析
>
> 找出某个分类的共性的特征.比如代码好的-睡眠时间长,敲代码时间比较长等...
>
> 4. 数据的挖掘
>
> 金融行业,房地产行业
>
> 根据数据分析的结果来预判下一次的结果 - 建模
>
> 理想状态中 -> y = ax + b;
>
> 现实状态中,不可能得到一个100%的模型的 - 最优模型-最优的解-模型的准确率
>
> x值,y值 -> y=ax+b
>
> 
>
> x     y     ->    z
>
> 8h   3h   - > good
>
> 8.5h 3.5h -> good
>
> 1h 30min -> error
>
> ...
>
> ...
>
> ...
>
> ...
>
> x和y与z之间的关系
>
> 未来接触到了新生,8.8h,10h -> 可能是good[概率高于error]

# delete语句

> 1. 清空表 - delete语句不带where
>
> ```mysql
> delete from 表名;
> 
> delete from video_copy;
> 
> ```
>
> 2. 带条件的删除 - 删除1条,不删除,删除多条
>
> ```mysql
> delete from 表名 where语句;
> 
> -- 删除video_copy表中id=2的行记录
> delete from video_copy where id=2;
> 
> ```

# update语句

> 1. 更新的整张表的数据 - 不带where语句
>
> ```mysql
> 语法:
> -- 更新所有的行的单列
> update 表名 set 列名=新的列值;
> 
> -- 更新所有的行的多列.
> update 表名 set 列1=新的列值1,列2=新的列值2,列n=新的列值n;
> 
> -- 比如更新video_copy表中的likes
> update video_copy set likes=likes*1.1;
> 
> -- 比如更新video_copy表中的likes和title
> update video_copy set likes=200,title='java无敌';
> 
> ```
>
> 2. 更新指定条件的行记录 - where语句
>
> ```mysql
> 更新id=2的title
> update video_copy set title='python不错' where id=2;
> 
> ```



# 总结约束注意点

> **列添加了约束,需要注意的地方有哪些**
>
> 1. 先删除多的一方表,再删除的一的一方的表
> 2. 删除行记录的时候,需要先把这个行记录在其他表中被引用的行记录要全部删除之后,才能删除自己.
> 3. 创建表的时候,应该先创建一的一方,然后创建多的一方

## 删除表

```mysql
-- 删除表不是随意删除的 - 前提是创建了外键约束
--  先删除多的一方表,再删除的一的一方的表
drop table tbl_comment;
drop table tbl_user;
drop table tbl_video;

-- 用户表
create table tbl_user(
  id int(7) primary key,
  username varchar(20) not null unique
);
insert into tbl_user values(1,'tom');
insert into tbl_user values(2,'admin');

-- 视频表
create table tbl_video(
	id int(7) primary key,
  title varchar(20),
  likes int(7),
  create_time datetime default now(),
  -- 外键列命名方式 表名_另外一张表的引用列名[主键列]
  -- 外键列的数据类型和长度和你引用的地方保持一致
  tbl_user_id int(7),
  -- 表级添加
  constraint tbl_video_table_user_id_fk foreign key(tbl_user_id) references tbl_user(id)
);
-- 一旦创建了约束,插入数据的时候需要注意的是.
-- 外键列是允许为空的.
insert into tbl_video values(1,'学好myql',100,now(),1);
insert into tbl_video values(2,'学好java',100,now(),1);
insert into tbl_video values(3,'学习jdbc',100,now(),2);

-- 评论表
create table tbl_comment(
	id int(7) primary key,
  user_id int(7),
  video_id int(7),
  content varchar(50),
  comment_id int(7),
  constraint tbl_comment_user_id_fk foreign key(user_id) references tbl_user(id),
  constraint tbl_comment_video_id_fk foreign key(video_id) references tbl_video(id),
  constraint tbl_comment_id_fk foreign key(comment_id) references tbl_comment(id)
);
insert into tbl_comment(id,user_id,video_id,content) values(1,1,1,'好的,我会学会mysql的');
insert into tbl_comment values(2,2,1,'好的,我会学会mysql的',1);

```

`分析删除表的顺序`

```mysql
-- 如果删除的是tbl_user或者tbl_video都会报错
-- 报错的原因就是由外键约束存在导致的

mysql> drop table tbl_user;
ERROR 1217 (23000): Cannot delete or update a parent row: a foreign key constraint fails
mysql> drop table tbl_video;
ERROR 1217 (23000): Cannot delete or update a parent row: a foreign key constraint fails

-- 应该要先删除子记录[外键所在的表中的记录],然后再删除父记录 - 先删除多的一方表,再删除的一的一方的表
-- 多的一方的外键引用的是一的一方的主键.

```



## 删除行记录

```mysql
-- id=2的用户是在tbl_comment中存在一条行记录的,它是被引用了.
mysql> delete from tbl_user where id=2;
ERROR 1451 (23000): Cannot delete or update a parent row: a foreign key constraint fails (`j03s`.`tbl_comment`, CONSTRAINT `tbl_comment_user_id_fk` FOREIGN KEY (`user_id`) REFERENCES `tbl_user` (`id`))

-- id=2的所有的评论的全部删除[删除子记录],然后再去删除id=2的用户
mysql> delete from tbl_comment where user_id=2;

-- 然后才能去删除id=2的用户,已经把用户id=2这个用户的所有的评论全部删除了
mysql> delete from tbl_user where id=2;

ERROR 1451 (23000): Cannot delete or update a parent row: a foreign key constraint fails (`j03s`.`tbl_video`, CONSTRAINT `tbl_video_table_user_id_fk` FOREIGN KEY (`tbl_user_id`) REFERENCES `tbl_user` (`id`))

id=2的用户又发布了一条视频,它也在tbl_video表中被引用了.
mysql> delete from tbl_video where tbl_user_id=2;

-- 等把id=2的用户的所有评论信息以及发布的视频全部删除完毕之后,才可以删除自己
mysql> delete from tbl_user where id=2;

```



## 约束的缺点

> 外键约束 - 保证数据的完整性和一致性.缺点 - 表之间的关系越强烈[手动创建了约束条件] - 表之间的耦合越高.
>
> 在下周DB设计 - **不要手动创建约束** - 弱化表之间的关系 - **"表的关系 - 心中"**
>
> ```mysql
> 驰星CRM系统 - 学生信息,项目经理信息
> 
> -- 假设一个项目经理可以教多个学生
> -- dev_id是外键列
> student(id,sname,school,dev_id);
> 1 杨晓轩 文正养老学院 2
> 2 闫昊   八一农垦    1
> 3 刘静   八一农垦    2
> 
> dev(id,dev_name,age);
> 1 success 18
> 2 james  45
> 3 小强    19
> 
> 如果建表的时候
> create table student(
> 	//...
> 
> foreign key(dv_id) references dev(id);
> );
> 
> -- 导致的后果,不能随意删除项目经理.项目经理只要有学生信息.就不能删除项目经理.
> 
> -- 现实生活中 - success上台了,james开除了...
> mysql> delete from dev where id=2;//删除失败了.因为james在student表中有子记录
> -- 要想删除james,需要删除他所有的学生.实际的业务 - 肯定不允许我去删除"宝贵的学生信息"
> 
> -- 目标 - 1. 成功删除项目经理
> --       2. 知道学生曾经是哪个老师带过的.
> 
> -- 因此,在建表的时候,其实是允许数据的冗余性的.
> drop table t_student;
> drop table dev;
> create table dev(
> 	id int(7) primary key auto_increment,
> dev_name varchar(20),
> age int(3)
> );
> insert into dev values(1,'success',18);
> insert into dev values(2,'james',45);
> 
> create table t_student(
> 	id int(7) primary key auto_increment,
> sname varchar(20),
> school varchar(20),
> -- 此处没有进行约束的添加 - "关系在心中即可"
> dev_id int(7),
> -- 数据的冗余 - 防止项目经理的记录被删除
> dev_name varchar(20)
> );
> insert into t_student values(1,'杨晓轩','文正养老学院',2,'james');
> insert into t_student values(2,'闫昊','八一农垦',1,'success');
> insert into t_student values(3,'刘静','八一农垦',1,'success');
> 
> -- 虽然没有给dev_id列创建约束,业务分析可以确定该列就是用来"维护俩表之间的关系的"
> -- 查询学生的姓名以及项目经理的姓名以及年龄
> mysql> select s.sname,d.dev_name,d.age from t_student s join dev d on s.dev_id=d.id;
> 
> -- 开除james,没有约束,可以直接删除
> mysql> delete from dev where id=2;
> 
> ```



# 物理删除和逻辑删除

> 1. 物理删除就是真正意义上的delete语句
> 2. **逻辑删除 - 建议 - 比较安全.**
>
> 因为数据是不能随意删除的.数据是需要后台进行数据分析的.**用户的端的删除大部分都是逻辑删除.**
>
> 实际上就是每张表都会增加逻辑列.
>
> ```mysql
> drop table xx_del;
> create table xx_del(
> 	id int(7) primary key,
>   name varchar(20),
>   status int(7)
> );
> insert into xx_del values(1,'tom',0);
> insert into xx_del values(2,'admin',0);
> 
> -- 首页 - 加载xx_del表中所有的信息
> -- 查询逻辑列 - 把status=0的行记录全部加载
> List<XX> list = "select * from xx_del where status=0";
> 
> -- 删除按钮 - 删除 - 实际上是逻辑删 - 并不会执行delete语句,执行的是update语句
> update xx_del set status=1 where id=1;
> 
> -- 再去根据status=0去加载,给用户端的感觉好像是这条记录被删除了.
> 
> ```















































