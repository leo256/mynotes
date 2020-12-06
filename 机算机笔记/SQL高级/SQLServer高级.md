# SQLServer高级

## 范式

在数据库设计中，范式主要用来解决数据库的冗余，在设计关系数据库时，遵从不同的规范要求，设计出合理的关系型数据库，这些不同的规范要求被称为不同的范式，各种范式呈递次规范，越高的范式数据库冗余越小。

### 1NF

所谓第一范式（1NF）是指在关系模型中，对于添加的一个规范要求，所有的字段都应该是原子性的，即数据库表的每一列都是不可分割的原子数据项，而不能是集合，数组，记录等非原子数据项。

例子：

客户信息表

| 编号 | 姓名 | 年龄 | 联系方式                   |
| ---- | ---- | ---- | -------------------------- |
| 1    | 张三 | 21   | 13567899087，0756-29087652 |

上面的设计中就违反了1NF，联系方式可以分为固定电话和手机号，因此修改为：

| 编号 | 姓名 | 年龄 | 手机        | 固话          |
| ---- | ---- | ---- | ----------- | ------------- |
| 1    | 张三 | 21   | 13567899087 | 0756-29087652 |

### 2NF

满足2NF的前提是必须满足1NF，因为第二范式是建立在第一范式的基础上。2NF要求表中的所有列，都必须依赖于主键，而不能有任何一列与主键没有关系，也就是说一个表只描述一个实体。

例子：

订单表

| 订单编号 | 下单时间            | 商品编号 | 商品名称       | ...  |
| -------- | ------------------- | -------- | -------------- | ---- |
| 1000     | 2020-09-08 13:25:21 | 001      | macbook pro 13 | ...  |

上面的订单表违反了2NF，主键是订单编号，在设计表时即包含订单信息，还包含了商品信息，因此将表拆解为订单表和商品表，如下：

订单表

| 订单标号 | 下单时间            |
| -------- | ------------------- |
| 1000     | 2020-09-08 13:25:21 |

商品表

| 商品编号 | 商品名称       |
| -------- | -------------- |
| 001      | macbook pro 13 |

### 3NF

3NF必须先满足第二范式2NF，要求表中的每一列只与主键直接相关而不是间接相关（表中的每一列只能依赖于主键）。

例子：

订单表

| 订单编号 | 下单时间            | 客户编号 | 客户姓名 |
| -------- | ------------------- | -------- | -------- |
| 1000     | 2020-09-08 13:25:21 | 1        | 张三     |

上面表中的客户编号与订单编号是直接的依赖关系，但客户姓名其实是与订单编号产生间接依赖关系，并不是直接依赖，仅仅是间接依赖，因此只需要在订单表中关联客户表中的编号即可，如下：

订单表

| 订单编号 | 下单时间            | 客户编号 |
| -------- | ------------------- | -------- |
| 1000     | 2020-09-08 13:25:21 | 1        |

客户信息表

| 客户编号 | 姓名 | 年龄 | 手机        | 固话          |
| -------- | ---- | ---- | ----------- | ------------- |
| 1        | 张三 | 21   | 13567899087 | 0756-29087652 |

## SQL语言

结构化查询语言(Structured Query Language)简称SQL，是1986年10 月由美国国家标准局（ANSI）通过的数据库语言美国标准，接着，国际标准化组织（ISO）颁布了SQL正式国际标准。1989年4月，ISO提出了具有完整性特征的SQL89标准，1992年11月又公布了SQL92标准。

SQL可分为数据定义语言（DDL）、数据操纵语言（DML）、数据查询语言（DQL）、数据控制语言（DCL）、事务控制语言（TCL）五种类型。

### 数据定义语言（DDL）

DDL包括CREATE创建语句、DROP删除语句、ALTER修改语句。主要用于定义或修改数据库和表结构、数据类型等操作。

#### 创建数据库

示例1：

~~~ sql
--创建数据库
create database students
go

--使用数据库
use students
go
~~~

示例2：

~~~ sql
--创建数据库并自定义主数据库文件和日志文件
create database students
go
on(
	name=students,
	filename='c:\students_db.mdf',
	size=10,
	maxsize=500
)
log on(
	name=student_log,
	filename='c:\students_log.ldf',
	size=10,
	maxsize=500
)
go

--使用数据库
use students
go
~~~

#### 删除数据库

~~~ sql
--删除数据库
drop database students
go
~~~

#### 创建表

~~~ sql
-- 创建班级表
create table class_info(
	c_id int primary key identity,  --ID主键，自增长
	c_name varchar(20) not null    --班级名称, 非空约束
)
go

-- 创建学生表
create table stu_info(
	stu_id int primary key identity,  --ID主键，自增长
	stu_name varchar(20) not null unique,    --姓名，非空约束, 唯一约束
	age int not null check(age>18 and age<80),   --年龄，非空约束、检查约束
	sex nchar(1) not null default('男'), --性别,非空约束、默认约束 
	c_id int references class_info(c_id) --班级外键，外键约束
)
go
~~~

#### 删除表

~~~ sql
-- 删除表
drop table stu_info
go
~~~

#### 修改表

~~~sql
--修改表（添加字段、修改字段类型、添加删除约束）
--先创建简单的表
create table stu_info(
	stu_id int identity,
	stu_name varchar(20) not null,
	age int not null,
	c_id int
)
go

-- 添加字段
alter table stu_info add sex varchar(1) not null
go

-- 字段类型
alter table stu_info alter column sex nchar(1) not null
go

-- 添加主键约束
alter table stu_info add constraint pk_stuid primary key(stu_id)
go

-- 添加唯一约束
alter table stu_info add constraint uq_stuname unique(stu_name)
go

-- 添加默认约束
alter table stu_info add constraint df_sex default('男') for sex
go

-- 添加检查约束
alter table stu_info add constraint ck_age check(age>18 and age<100)
go

-- 添加外键约束
alter table stu_info add constraint fk_cid foreign key(c_id) references class_info(c_id)
go

-- 删除约束
alter table stu_info drop constraint fk_cid
go
~~~

### 数据操纵语言（DML）

#### 插入数据

插入一条数据

~~~ sql
insert into stu_info(stu_name, age, sex, c_id) values('user1', 21, '男', 1)
go
~~~

插入多条记录

~~~sql
insert into stu_info(stu_name, age, sex, c_id) values('user2', 21, '男', 1)
insert into stu_info(stu_name, age, sex, c_id) values('user3', 20, '女', 1)
insert into stu_info(stu_name, age, sex, c_id) values('user4', 19, '男', 1)
insert into stu_info(stu_name, age, sex, c_id) values('user5', 22, '女', 1)
go
~~~

或者

~~~sql
insert into stu_info(stu_name, age, sex, c_id) values
('user2', 21, '男', 1), 
('user3', 20, '女', 1),
('user4', 19, '男', 1),
('user5', 22, '女', 1)
go
~~~

#### 修改数据

将所有学生关联的班级ID修改为编号为2的班级ID

~~~sql
update stu_info set c_id = 2
~~~

修改学生ID为1的记录信息

~~~sql
update stu_info set stu_name='user0', age=20 where stu_id = 1
go
~~~

#### 删除数据

根据主键删除某一条记录

~~~sql
delete from stu_info where stu_id = 1
go
~~~

删除整张表的数据

~~~ sql
delete from stu_info
go
~~~

### 数据查询语言（DQL）

#### 单表查询

查询所有列

~~~sql
select * from stu_info
~~~

查询指定的列

~~~sql
select stu_name, age, sex from stu_info
~~~

#### 条件查询

单条件查询

~~~sql
select stu_name, age, sex from stu_info where stu_id = 1
~~~

多条件查询

~~~sql
select stu_name, age, sex from stu_info where stu_id = 1 and c_id = 1
~~~

#### 查询排序

升序

~~~sql
select stu_name, age, sex from stu_info order by stu_id asc
~~~

降序

~~~sql
select stu_name, age, sex from stu_info order by stu_id desc
~~~

#### 模糊查询

使用“%”模糊匹配多个字符

~~~sql
select stu_name, age, sex from stu_info where stu_name like '张%'
~~~

~~~sql
select stu_name, age, sex from stu_info where stu_name like '%三%'
~~~

使用“_”匹配单个字符

~~~sql
select stu_name, age, sex from stu_info where stu_name like '张_'
~~~

~~~sql
select stu_name, age, sex from stu_info where stu_name like '张__'
~~~

#### 统计查询

计算总行数

~~~sql
select count(*) as students from stu_info
~~~

计算总和

~~~sql
select sum(age) as sum_age from stu_info
~~~

计算最大值

~~~sql
select max(age) as max_age from stu_info
~~~

计算最小值

~~~sql
select min(age) as min_age from stu_info
~~~

计算平均值

~~~sql
select avg(age) as avg_age from stu_info
~~~

#### 分组统计

统计男生和女生的总人数

~~~sql
select sex, count(*) as num from stu_info group by sex 
~~~

注意：在查询中未参与统计的所有字段必须出现在group by语句中，如果分组之后需要添加条件，需使用having关键字。

#### 连接查询

内连接（inner join）

~~~sql
select s.stu_id, s.stu_name, s.age, c.c_name from stu_info s inner join class_info c on s.c_id = c.c_id
~~~

左外连接（left join）

~~~sql
select s.stu_id, s.stu_name, s.age, c.c_name from stu_info s left join class_info c on s.c_id = c.c_id
~~~

右外连接（right join）

~~~sql
select s.stu_id, s.stu_name, s.age, c.c_name from class_info c right join stu_info s on s.c_id = c.c_id
~~~

交叉连接（cross join）

注意：交叉连接如果不加条件默认会产生笛卡尔积

~~~sql
select s.stu_id, s.stu_name, s.age, c.c_name 
from class_info c cross join stu_info s where s.c_id = c.c_id
~~~

或者

~~~sql
select s.stu_id, s.stu_name, s.age, c.c_name
from stu_info s, class_info c where s.c_id = c.c_id
~~~

自连接

自身表中的外键列引用主键列，例如：

~~~sql
create table menu_info(
	id int primary key,
	name varchar(20),
	p_id int 
)

insert into menu_info(id, name, p_id) values
(1,'一级菜单', null),
(2,'二级菜单1', 1),
(3,'二级菜单2', 1),
(4,'三级菜单1', 2),
(5,'三级菜单2', 2),
(6,'三级菜单3', 3),
(7,'三级菜单4', 3)

-- 查询
select m1.name as parent, m2.name as child from menu_info m1 join menu_info m2 on m1.id = m2.p_id
~~~

#### 查询合并

创建两张列相同的表

~~~sql
create table table1(
	id int primary key,
	name varchar(20)
)
create table table2(
	id int primary key,
	name varchar(20)
)
go

-- 插入测试数据
insert into table1(id, name) values
(1, 'user1'),
(2, 'user2'),
(3, 'user3')
insert into table2(id, name) values
(1, 'user1'),
(2, 'user4'),
(3, 'user5')
go
~~~

union：对两个结果集进行并集操作，不包括重复记录

~~~sql
select * from table1 
union 
select * from table2
~~~

![image-20200909144703184](https://tva1.sinaimg.cn/large/007S8ZIlgy1giked16bg3j31ag07u0sz.jpg)

union all：对两个结果集进行并集操作，包括重复记录

~~~sql
select * from table1 
union all
select * from table2
~~~

![image-20200909144727433](https://tva1.sinaimg.cn/large/007S8ZIlgy1gikedezfjqj3146090mxf.jpg)

#### 子查询

**in子查询：**

查询id在1-3范围的学生信息

~~~sql
select stu_name, age, sex from stu_info where stu_id in (1,2,3)
~~~

或者

~~~sql
select stu_name, age, sex from stu_info s1 where s1.stu_id in (select stu_id from stu_info where stu_id <4)
~~~

**exists子查询：**

exists做为where 条件时，是先执行外查询，然后用外查询的结果一个一个的代入exists的子查询进行判断，如果匹配则返回true，否则返回false。

~~~sql
select stu_name, age, sex from stu_info s1 where exists (select * from stu_info where s1.stu_id <4)
~~~

注意：如果外查询和子查询数据量相当，那么用in和exists差别不大。如果外查询和子查询中一个数据较少，一个较多，则子查询较多的用exists，子查询较小的用in。

## T-SQL编程

SQL Server用于操作数据库的编程语言为Transaction-SQL，简称T-SQL。

### 变量

在T-SQL中，变量用于存放临时数据，数据类型确定了该变量存放值的格式以及允许的运算。变量标识符由字母、数字、下划线、@、#、$符号组成，其中字母可以是a-z或A-Z。但首字符不能为数字和\$，不允许是T-SQL保留字，标识符内不允许有空格和特殊字符，长度小于128。

#### 局部变量

局部变量是作用在一定范围的T-SQL中，通常是在一个批处理中被声明或定义，当这个批处理结束后，局部变量也就随之销毁。

声明一个变量：

~~~sql
declare @name varchar(20)
~~~

声明多个变量：

~~~sql
declare @name varchar(20)
declare @age int
declare @sex nchar(1)
~~~

或者

~~~sql
declare @name varchar(20), @age int, @sex nchar(1)
~~~

为变量赋值：

~~~sql
set @name = 'user1'
~~~

示例：

~~~sql
-- 声明变量
declare @name varchar(20), @age int
-- 将查询stu_name,age的值赋值给变量@name和@age
select @name = stu_name, @age = age from stu_info where stu_id = 1
-- 输出变量的值
print @name
print @age
~~~

#### 全局变量

全局变量是用来记录SQL Server服务器活动状态的一组数据，是数据库系统提供并赋值好的变量，用户不能建立全局变量，也不能给全局变量赋值或修改全局变量的值。全局变量的变量名都是以@@开头。

示例：

~~~sql
-- @@connections：记录最后一次服务器启动以来，连接到服务器的连接数（包括没有连接成功的尝试）。
print @@connections
-- @@cpu_busy：记录最后一次服务器启动以来，CPU的工作时间（单位：ms）
print @@cpu_busy
-- @@error：返回执行上一条T-SQL语句所返回的错误号
print @@error
~~~

### 流程控制语句

**begin...end语句：**

begin...end关键字将一组T-SQL语句封装成一个完整的SQL语句块，begin定义T-SQL语句块的开始，end表示语句块的结束。

示例：

~~~sql
declare @name varchar(20)
begin
	select @name = stu_name from stu_info where stu_id = 1
	print @name
end
~~~

**if...else语句：**

示例：

~~~sql
declare @age int
begin
	select @age = age from stu_info where stu_id = 1
	if @age < 20 
		begin
			print '小于20岁'
		end
	else
		begin
			print '大于20岁'
		end	
end
~~~

**while语句：**

示例：

~~~sql
declare @num int
set @num = 0
begin
	while @num < 10
	begin
		print @num
		set @num = @num + 1
	end
end
~~~

**case语句：**

示例：

~~~sql
begin
	select stu_name, 
	case sex
		when '男' then 1
		when '女' then 0
	else '未知'
	end	
	as sex
	from stu_info where stu_id = 1
end
~~~

**return语句：**

return可以语句块的任何位置使用，作用是退出语句块。return之后的所有语句将不被执行

示例：

~~~sql
declare @num int
set @num = 0
begin
	while 1=1
	begin
		if @num = 5
			return
		print @num
		set @num = @num + 1
	end
end
~~~

## 索引

数据库索引是为了增加查询速度而对表字段附加的一种标识，通过添加正确的索引可以大大减少查询的执行时间。数据库在执行一条SQL语句的时候，默认的方式是根据搜索条件进行全表扫描，遇到匹配条件的就加入搜索结果集合。如果我们对某一字段增加索引，查询时就会先去索引列表中一次定位到特定值的行数，大大减少遍历匹配的行数，所以能明显增加查询的速度。那大量使用索引是不是就可以极大的提高查询效率呢？其实并不是的，索引本身就要占用额外的存储空间，并且对于一些非唯一的字段，例如像性别这种大量重复的值的字段，添加索引说无意义的。而且对于每一次的增删改操作，字段的索引都必须重新计算更新。

### 索引类型

#### 聚集索引

聚集索引定义了数据在表中的存储顺序。聚集索引类似于电话簿，可按姓氏排列数据。由于聚集索引规定数据在表中的物理存储顺序，因此一个表只能包含一个聚集索引。SQL SERVER默认会在主键上建立聚集索引。也就是说我们通常在每个表中都建立一个自动增长的ID列。SQL SERVER会将此列默认为聚集索引。这样做的好处是可以让您的数据在数据库中按照ID进行物理排序。![聚集索引](https://segmentfault.com/img/bVFiuz?w=543&h=191)

#### 非聚集索引

不像聚集索引，非聚集索引本身不存储数据本身。相反，非聚集索引只存储指向表数据的指针，该指针只作为索引键的一部分，因此一个表中可以存在多个非聚集索引。

![非聚集索引](https://segmentfault.com/img/bVFiu4?w=664&h=534)

### 创建索引

**创建单列索引**

~~~sql
create index stu_index on stu_info(stu_name)
~~~

**创建组合索引（多列索引）**

~~~sql
create index stu_index on stu_info(stu_name, age)
~~~

**创建唯一索引**

~~~sql
create unique index stu_index on stu_info(stu_name)
~~~

### 修改索引

无论何时对基础数据执行插入、更新或删除操作， SQL Server 数据库引擎 都会自动修改索引。 随着时间的推移，这些修改可能会导致索引中的信息分散在数据库中（含有碎片）。 当索引包含的页中的逻辑排序（基于键值）与数据文件中的物理排序不匹配时，就存在碎片。 碎片非常多的索引可能会降低查询性能，导致应用程序响应缓慢，特别是扫描操作。

**重建索引**

重建索引将会删除原来的索引并重新创建索引

~~~sql
alter index stu_index on stu_info rebuild
~~~

**重新组织索引**

重新组织索引会对最外层数据页里的数据进行重新排序，并压缩索引页。所以索引可能还残留着一定程度的碎片。

~~~sql
alter index stu_index on stu_info reorganize
~~~

### 删除索引

~~~sql
drop index stu_index on stu_info
~~~

### 索引使用原则

- 主键自动建立唯一索引。
- 频繁作为查询条件的字段应该创建索引。
- 查询中与其他表关联的字段，外键关系建立索引。
- 频繁更新的字段不适合创建索引，因为每次更新不单单是更新了记录还会更新索引文件。
- where条件里用不到的字段不创建索引。
- 在高并发下倾向创建组合索引。
- 查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度。
- 查询中统计或者分组字段。

## 视图

视图是数据库的一种对象，可以是一个或多个基表的查询结果创建出来的一种虚拟表，因此视图的结构和数据是来自对数据表进行查询的结果。视图被创建后就保存在数据库中，通过视图看到的数据是存放在基表中的数据。当对视图的数据进行修改时，相应的基表数据也会发生变化。同时，如果基表的数据发生变化时，这种变化也会自动同步到视图中。

### 创建视图

~~~sql
create view elective_view 
with encryption --对视图加密（可选）
as 
select c.c_name, co.course_name, count(*)as elective from stu_info s 
join class_info c on c.c_id = s.c_id join stu_course sc 
on s.stu_id = sc.stu_id join course co on sc.course_id = co.id 
group by c.c_name, co.course_name
go
~~~

### 查询视图

~~~sql
select * from elective_view order by c_name
~~~

### 修改视图

~~~sql
alter view elective_view 
with encryption 
as 
select c.c_name, co.course_name, count(*)as elective from stu_info s 
join class_info c on c.c_id = s.c_id join stu_course sc 
on s.stu_id = sc.stu_id join course co on sc.course_id = co.id 
group by c.c_name, co.course_name having c.c_name = 'ST01'
go
~~~

### 删除视图

~~~sql
drop view elective_view
~~~

### 显示视图信息

~~~sql
sp_help elective_view
~~~

![image-20200914212834533](https://tva1.sinaimg.cn/large/007S8ZIlgy1giqi2b7sd4j30pf05i3yu.jpg)

### 通过视图更新数据

在满足一些条件限制时，可以对视图进行插入、修改、删除数据。视图必须定义在一个表上并且不包含任何统计函数或group by子句。

~~~sql
create view stu_view as select * from stu_info
~~~

**插入数据**

~~~sql
insert into stu_view(stu_name, age, sex) values('user8', 19, '男')
~~~

**修改数据**

~~~sql
update stu_view set age = 20 where stu_name = 'user8'
~~~

**删除数据**

~~~sql
delete from stu_view where stu_name = 'user8'
~~~

## 事务

事务是数据库的核心概念之一，它表示数据库一系列操作的集合。这些操作必须在一个事务当中，要么全部执行成功，要么全部不执行。

### ACID特性

**原子性（Atomicity）**

原子性是指事务包含的所有操作要么全部成功，要么全部失败回滚，因此事务的操作如果成功就必须要完全应用到数据库，如果操作失败则不能对数据库有任何影响。

**一致性（Consistency）**

一个事务执行之前和执行之后都必须处于一致性状态。例如转账，假设用户A和用户B两者的钱加起来一共是5000，那么不管A和B之间如何转账，转几次账，事务结束后两个用户的钱相加起来应该还得是5000，这就是事务的一致性。

**隔离性（Isolation）**

隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。

**持久性（Durability）**

持久性是指一个事务一旦被提交了，那么对数据库中的数据的改变就是永久性的，即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作。

### 事务分类

**自动提交事务**

自动提交事务是SQL Server默认的事务管理模式，指的是每条单独的语句都是一个事务。在每个语句完成时，都会自动提交或回滚。也就是如果一个语句执行成功则提交该语句；如果执行时遇到错误就自动回滚。

**隐式事务**

当SQL Server以隐式事务执行时，当前事务结束后会自动启用新的事务，无需手动开启一个新事物。但每个事务需要显示的调用Commit或者Rollback来提交或回滚事务。

**显示事务**

显示事务也就是事务的开启到提交或者回滚都由我们自己控制。

示例：

~~~sql
declare @c_name varchar(10)
declare @count int
set @c_name = 'ST02'
set @count = 0
begin
	begin transaction --手动开启事务
	insert into class_info(c_name) values(@c_name)
	select @count = count(*) from class_info where c_name = @c_name
	if(@count > 1)
		begin
			rollback --回滚事务
			print '事务已回滚'
		end
	else
	   begin
			commit --提交事务
			print '事务已提交'
	   end	
end
~~~

##  存储过程

存储过程是一种数据库对象，存储在数据库内。它为了实现特定任务而将一些需要多次调用的固定操作编写成子程序并集中以一个存储单元的形式存储在数据库中。

### 创建存储过程

示例一：

~~~sql
create proc proc_demo1
as
	select s.stu_id, s.stu_name, c.c_name, s.age, s.sex from stu_info s
	join class_info c on s.c_id = c.c_id
go
~~~

示例二：

~~~Sql
if exists (select * from sysobjects where name = 'proc_demo1')
	drop proc proc_demo1
go
create proc proc_demo1
as
	select s.stu_id, s.stu_name, c.c_name, s.age, s.sex from stu_info s
	join class_info c on s.c_id = c.c_id
go
~~~

### 执行存储过程

~~~sql
exec proc_demo1
~~~

### 输入参数

通过输入参数可以在存储过程执行时传入特定的条件

~~~sql
-- 创建带参数的存储过程
create proc proc_demo2
@class_name varchar(20) --定义输入参数，可以定义多个，使用逗号隔开
as
	select s.stu_id, s.stu_name, c.c_name, s.age, s.sex from stu_info s
	join class_info c on s.c_id = c.c_id where c.c_name = @class_name
go

-- 执行存储过程
exec proc_demo2 'ST01'
~~~

**使用默认参数**

~~~sql
-- 创建带默认参数的存储过程
create proc proc_demo3
@sex nchar(1) = '男' --默认参数
as
	select s.stu_id, s.stu_name, c.c_name, s.age, s.sex from stu_info s
	join class_info c on s.c_id = c.c_id where s.sex = @sex
go

-- 执行存储过程
exec proc_demo3
exec proc_demo3 '女'
~~~

### 输出参数

通过输出参数，可以存存储过程返回一个或多个值

~~~sql
-- 创建带输入和输出参数的存储过程
create proc proc_demo4
	@class_name varchar(20), --输入参数
	@count int output        --使用output指定为输出参数
as
	select @count = count(*) from stu_info s join class_info c 
	on s.c_id = c.c_id where c.c_name = @class_name
go


-- 定义一个参数用于接收返回的结果
declare @count int
-- 执行存储过程
exec proc_demo4 'ST01', @count output
-- 输出返回的结果
print @count
~~~

### 返回值

存储过程在执行后都会返回一个整型的值。如果执行成功返回0，失败则返回-1到-99之间的随机数。可以使用return语句来指定一个存储过程的返回值。

~~~sql
create proc proc_demo5
	@a int, @b int,       --输入参数
	@count int output     --使用output指定为输出参数
as
	set @count = @a + @b
	return @count
go

-- 执行
declare @count int
exec proc_demo5 3,2, @count output
print @count
~~~

## 触发器

触发器是提供给程序员和数据分析员来保证数据完整性的一种手段，它是与表事件相关的特殊的存储过程。当用户对一个表进行操作( insert，delete， update)时会被自动触发执行。

### DML触发器

DML触发器是当数据库中发生数据操纵语言事件时自动执行的操作。DML触发器分为after触发器（之后触发）和instead of 触发器 （之前触发）。

**insert触发器**

insert触发器会在inserted临时表中添加一条刚插入的记录

~~~sql
create trigger tri_insert on stu_info
after insert
as
	print '插入数据之后触发某些操作'
go
~~~

**update触发器**

update触发器会在更新数据前后，将更新前的数据保存在deleted临时表中，更新后的数据保存在inserted临时表中

~~~sql
create trigger tri_upadte on stu_info
after update
as
	print '更新数据之后触发某些操作'
go
~~~

**delete触发器**

delete触发器会在删除数据的时候，将刚才删除的数据保存在deleted临时表中

~~~sql
create trigger tri_delete on stu_info
after delete
as
	print '删除数据时触发某些操作'
go
~~~

### DDL触发器

DDL触发器是由修改数据库对象的 DDL 语句（如以 CREATE、ALTER 或 DROP）而激发。这类触发器可用于控制数据库对象的操作。

示例：

~~~sql
create trigger tri_ddl on database
for drop_table, drop_view
as
	print '不允许删除数据表'
	rollback transaction
go	
~~~

