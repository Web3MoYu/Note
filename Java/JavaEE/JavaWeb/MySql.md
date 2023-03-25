# 数据库基础

***

## 数据库定义语言（DDL）

### 数据库操作

我们可以通过`create database`来创建一个数据库：

```sql
create database 数据库名

-- 创建study数据库
create database study;
```

为了能够支持中文，我们在创建时可以设定编码格式：

```sql
CREATE DATABASE IF NOT EXISTS 数据库名 DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
```

如果我们创建错误了，我们可以将此数据库删除，通过使用`drop database`来删除一个数据库：

```sql
drop database 数据库名
```

### 创建表

数据库创建完成后，我们一般通过`create table`语句来创建一张表：

```sql
create table 表名(列名 数据类型[列级约束条件],
             列名 数据类型[列级约束条件],
             ...
             [,表级约束条件])
```

- 例子创建一个学生和教师表

  ```sql
  -- 创建一个student表，其中sid为int类型的主键，name为varchar不能为空，sex为枚举类不能为空，默认男
  create table student(sid int primary key,
                      name varchar(10) not null,
                       sex enum('男','女') not null, default '男');
  -- 修改表名
  alter table student rename to students; # 将student修改成students
  ```

  

### SQL数据类型

以下的数据类型用于字符串存储：

* char(n)可以存储任意字符串，但是是固定长度为n，如果插入的长度小于定义长度时，则用空格填充。
* varchar(n)也可以存储任意数量字符串，长度不固定，但不能超过n，不会用空格填充。

以下数据类型用于存储数字：

* smallint用于存储小的整数，范围在 (-32768，32767)
* int用于存储一般的整数，范围在 (-2147483648，2147483647)
* bigint用于存储大型整数，范围在 (-9,223,372,036,854,775,808，9,223,372,036,854,775,807)
* float用于存储单精度小数
* double用于存储双精度的小数

以下数据类型用于存储时间：

* date存储日期
* time存储时间
* year存储年份
* datetime用于混合存储日期+时间

### 列级约束条件

列级约束有六种：主键Primary key、外键foreign key 、唯一 unique、检查 check （MySQL不支持）、默认default 、非空/空值 not null/ null

### 表级约束条件

表级约束有四种：主键、外键、唯一、检查

现在我们通过SQL语句来创建我们之前提到的三张表。

```sql
[CONSTRAINT <外键名>] FOREIGN KEY 字段名 [，字段名2，…] REFERENCES <主表名> 主键列1 [，主键列2，…]
```

### 修改表

如果我们想修改表结构，我们可以通过`alter table`来进行修改：

```sql
ALTER TABLE 表名[ADD 新列名 数据类型[列级约束条件]] #添加新的列
							 [DROP COLUMN 列名[restrict|cascade]] #删除列名
							 [ALTER COLUMN 列名 新数据类型] 
							 
-- 例子
alter table teacher add sex enum('男','女') not null;
-- 修改列
alter table teacher modify sex enum('男','女'); #将sex是否为空设置成默认
```

我们可以通过ADD来添加一个新的列，通过DROP来删除一个列，不过我们可以添加restrict或cascade，默认是restrict，表示如果此列作为其他表的约束或视图引用到此列时，将无法删除，而cascade会强制连带引用此列的约束、视图一起删除。还可以通过ALTER来修改此列的属性。

### 删除表

我们可以通过`drop table`来删除一个表：

```sql
DROP TABLE 表名[restrict|cascade]
```

其中restrict和cascade上面的效果一致。

### 修改表名字

```sql
rename table student to studentt
```



### 删除字段

```sql
alter table student
    drop column name;
```

### 修改字段名

```sql
alter table student
    change sex x enum ('男', '女') default '男' not null;
```



***

## 数据库操纵语言（DML）

前面我们已经学习了如何使用SQL语句来创建、修改、删除数据库以及表，而如何向数据库中插入、删除、更新数据，将是本版块讨论的重点。

### 插入数据

通过使用`insert into`语句来向数据库中插入一条数据（一条记录）：

```sql
INSERT INTO 表名 VALUES(值1, 值2, 值3)

-- 向student插入数据
insert into student values('2021081001','小明','男');
```

如果插入的数据与列一一对应，那么可以省略列名，但是如果希望向指定列上插入数据，就需要给出列名：

```sql
INSERT INTO 表名(列名1, 列名2) VALUES(值1, 值2)

-- 向student中指定插入 剩下的使用默认值
insert into student(sid, name) values('2021081002','小强');
```

我们也可以一次性向数据库中插入多条数据：

```sql
INSERT INTO 表名(列名1, 列名2) VALUES(值1, 值2), (值1, 值2), (值1, 值2)

-- 向student表中插入两组数据
insert into student values('2021081003','小红','女'), ('2021081004','小丽','女');
```

我们来试试看向我们刚刚创建的表中添加三条数据。

### 修改数据

我们可以通过`update`语句来更新表中的数据：

```sql
UPDATE 表名 SET 列名=值,... WHERE 条件

-- 将student表中的小明设置成小刚 如果不加where约束条件，那么student中的所有名字都会成为小刚
update student set name = '小刚' where sid = '2021081001';
```

注意，SQL语句中的等于判断是`=`

**警告：**如果忘记添加`WHERE`字句来限定条件，将使得整个表中此列的所有数据都被修改！

### 删除数据

我们可以通过使用`delete`来删除表中的数据：

```sql
DELETE FROM 表名
```

通过这种方式，将删除表中全部数据，我们也可以使用`where`来添加条件，只删除指定的数据：

```sql
DELETE FROM 表名 WHERE 条件

-- 将student表中的sid=111的数据删除
delete from student where sid = '111';
```

***

## 数据库查询语言（DQL）

数据库的查询是我们整个数据库学习中的重点内容，面对数据库中庞大的数据，该如何去寻找我们想要的数据，就是我们主要讨论的问题。

### 单表查询

单表查询是最简单的一种查询，我们只需要在一张表中去查找数据即可，通过使用`select`语句来进行单表查询：

```sql
-- 指定查询某一列数据
SELECT 列名[,列名] FROM 表名
select name, sex from student; # 查询name，sex从student表中
-- 会以别名显示此列
SELECT 列名 别名 FROM 表名
-- 查询所有的列数据
SELECT * FROM 表名
-- 只查询不重复的值
SELECT DISTINCT 列名 FROM 表名 
select distinct sex from student; # 查询不重复的sex从student中
```

我们也可以添加`where`字句来限定查询目标：

```sql
SELECT * FROM 表名 WHERE 条件
```

### 常用查询条件

* 一般的比较运算符，包括=、>、<、>=、<=、!=等。
* 是否在集合中：in、not in
* 字符模糊匹配：like，not like
* 多重条件连接查询：and、or、not

```sql
select * from student where name not in ('小明','小刚'); #查询name不是小明，小刚的人的信息
select * from student where sid like'%081%'; #查找sid包含081的，以081开头则是081%,结尾是%081
select * from student where sid not like'%081%'; # 上面相反
select * from student where sid like'%081%' and sex = '男'; #查找sid包括081并且sex=男的人
```



我们来尝试使用一下上面这几种条件。

### 排序查询

我们可以通过`order by`来将查询结果进行排序：

```sql
SELECT * FROM 表名 WHERE 条件 ORDER BY 列名 ASC|DESC
select * from student order by sid desc; # 降序,默认为升序
```

使用ASC表示升序排序，使用DESC表示降序排序，默认为升序。

我们也可以可以同时添加多个排序：

```sql
SELECT * FROM 表名 WHERE 条件 ORDER BY 列名1 ASC|DESC, 列名2 ASC|DESC

select * from student order by sex asc, sid desc; # 先按照sex升序，再按照sid降序
```

这样会先按照列名1进行排序，每组列名1相同的数据再按照列名2排序。

### 聚集函数

聚集函数一般用作统计，包括：

* `count([distinct]*)`统计所有的行数（distinct表示去重再统计，下同）
* `count([distinct]列名)`统计某列的值总和
* `sum([distinct]列名)`求一列的和（注意必须是数字类型的）
* `avg([distinct]列名)`求一列的平均值（注意必须是数字类型）
* `max([distinct]列名)`求一列的最大值
* `min([distinct]列名)`求一列的最小值

一般聚集函数是这样使用的：

```sql
SELECT count(distinct 列名) FROM 表名 WHERE 条件 
select count(*) from student; # 统计student表中有几行
select count(distinct sex) from student; # 统计sex不同的人数 
select max(math) from student; # 统计math成绩的最大值
```

### 分组和分页查询

通过使用`group by`来对查询结果进行分组，它需要结合聚合函数一起使用：

```sql
SELECT sum(*) FROM 表名 WHERE 条件 GROUP BY 列名
mysql>  select count(*) from student group by sex; # 按找sex进行分组统计
+----------+
| count(*) |
+----------+
|        3 |
|        2 |
+----------+
```

我们还可以添加`having`来限制分组条件：

```sql
SELECT sum(*) FROM 表名 WHERE 条件 GROUP BY 列名 HAVING 约束条件
select min(math), sex from student group by sex having sex = '男'; # 查找男性最低分 
```

我们可以通过`limit`来限制查询的数量，只取前n个结果：

```sql
SELECT * FROM 表名 LIMIT 数量
select name, math from student group by name, math order by math asc limit 1; # 分组查询name，math并按照math降序取第一个 相当于查询最后一名的成绩
```

我们也可以进行分页：

```sql
SELECT * FROM 表名 LIMIT 起始位置,数量
mysql> select * from student limit 0,3;
+------------+------+-----+------+
| sid        | name | sex | math |
+------------+------+-----+------+
| 2001081001 | 小刚 | 男  | 90.5 |
| 2021081002 | 小强 | 男  |   56 |
| 2021081003 | 小红 | 女  | 87.5 |
+------------+------+-----+------+
3 rows in set (0.02 sec)

mysql> select * from student limit 3,3;
+------------+------+-----+------+
| sid        | name | sex | math |
+------------+------+-----+------+
| 2021081004 | 小丽 | 女  |   65 |
| 2021081005 | 小明 | 男  |   76 |
| 2021081006 | 小李 | 男  |   81 |
+------------+------+-----+------+
3 rows in set (0.02 sec)
```

### 多表查询

多表查询是同时查询的两个或两个以上的表，多表查询会提通过连接转换为单表查询。

```sql
SELECT * FROM 表1, 表2
mysql> select * from student, teacher;
+------------+------+-----+------+------+------+-----+
| sid        | name | sex | math | tid  | name | sex |
+------------+------+-----+------+------+------+-----+
| 2001081001 | 小刚 | 男  | 90.5 | 1004 | 小然 | 女  |
| 2001081001 | 小刚 | 男  | 90.5 | 1003 | 王五 | 男  |
| 2001081001 | 小刚 | 男  | 90.5 | 1002 | 李四 | 男  |
| 2001081001 | 小刚 | 男  | 90.5 | 1001 | 张三 | 男  |
| 2021081002 | 小强 | 男  |   56 | 1004 | 小然 | 女  |
| 2021081002 | 小强 | 男  |   56 | 1003 | 王五 | 男  |
| 2021081002 | 小强 | 男  |   56 | 1002 | 李四 | 男  |
| 2021081002 | 小强 | 男  |   56 | 1001 | 张三 | 男  |
| 2021081003 | 小红 | 女  | 87.5 | 1004 | 小然 | 女  |
| 2021081003 | 小红 | 女  | 87.5 | 1003 | 王五 | 男  |
| 2021081003 | 小红 | 女  | 87.5 | 1002 | 李四 | 男  |
| 2021081003 | 小红 | 女  | 87.5 | 1001 | 张三 | 男  |
| 2021081004 | 小丽 | 女  |   65 | 1004 | 小然 | 女  |
| 2021081004 | 小丽 | 女  |   65 | 1003 | 王五 | 男  |
| 2021081004 | 小丽 | 女  |   65 | 1002 | 李四 | 男  |
| 2021081004 | 小丽 | 女  |   65 | 1001 | 张三 | 男  |
| 2021081005 | 小明 | 男  |   76 | 1004 | 小然 | 女  |
| 2021081005 | 小明 | 男  |   76 | 1003 | 王五 | 男  |
| 2021081005 | 小明 | 男  |   76 | 1002 | 李四 | 男  |
| 2021081005 | 小明 | 男  |   76 | 1001 | 张三 | 男  |
| 2021081006 | 小李 | 男  |   81 | 1004 | 小然 | 女  |
| 2021081006 | 小李 | 男  |   81 | 1003 | 王五 | 男  |
| 2021081006 | 小李 | 男  |   81 | 1002 | 李四 | 男  |
| 2021081006 | 小李 | 男  |   81 | 1001 | 张三 | 男  |
+------------+------+-----+------+------+------+-----+
```

直接这样查询会得到两张表的笛卡尔积，也就是每一项数据和另一张表的每一项数据都结合一次，会产生庞大的数据。

```sql
SELECT * FROM 表1, 表2 WHERE 条件
```

这样，只会从笛卡尔积的结果中得到满足条件的数据。

**注意：**如果两个表中都带有此属性吗，需要添加表名前缀来指明是哪一个表的数据。

### 自身连接查询

自身连接，就是将表本身和表进行笛卡尔积计算，得到结果，但是由于表名相同，因此要先起一个别名：

```sql
SELECT * FROM 表名 别名1, 表名 别名2
mysql> select * from student t1, student t2;
```

其实自身连接查询和前面的是一样的，只是连接对象变成自己和自己了。

### 外连接查询

外连接就是专门用于联合查询情景的，比如现在有一个存储所有用户的表，还有一张用户详细信息的表，我希望将这两张表结合到一起来查看完整的数据，我们就可以通过使用外连接来进行查询，外连接有三种方式：

* 通过使用`inner join`进行内连接，只会返回两个表满足条件的交集部分：

* ```sql
  select * from student inner join teach on student.sid = teach.sid; # 查询sid等的
  
  mysql> select * from student inner join teach on student.sid = teach.sid;
  +------------+------+-----+------+------+------------+
  | sid        | name | sex | math | tid  | sid        |
  +------------+------+-----+------+------+------------+
  | 2021081002 | 小强 | 男  |   56 | 1001 | 2021081002 |
  | 2021081005 | 小明 | 男  |   76 | 1002 | 2021081005 |
  | 2001081001 | 小刚 | 男  | 90.5 | 1001 | 2001081001 |
  | 2021081003 | 小红 | 女  | 87.5 | 1001 | 2021081003 |
  | 2021081003 | 小红 | 女  | 87.5 | 1003 | 2021081003 |
  +------------+------+-----+------+------+------------+
  ```



![在这里插入图片描述](https://img-blog.csdnimg.cn/2019053022120536.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg1ODIwMQ==,size_16,color_FFFFFF,t_70)

* 通过使用`left join`进行左连接，不仅会返回两个表满足条件的交集部分，也会返回左边表中的全部数据，而在右表中缺失的数据会使用`null`来代替（右连接`right join`同理，只是反过来而已，这里就不再介绍了）：

* ```sql
  mysql> select * from student left join teach on student.sid = teach.sid;
  +------------+------+-----+------+------+------------+
  | sid        | name | sex | math | tid  | sid        |
  +------------+------+-----+------+------+------------+
  | 2001081001 | 小刚 | 男  | 90.5 | 1001 | 2001081001 |
  | 2021081002 | 小强 | 男  |   56 | 1001 | 2021081002 |
  | 2021081003 | 小红 | 女  | 87.5 | 1001 | 2021081003 |
  | 2021081003 | 小红 | 女  | 87.5 | 1003 | 2021081003 |
  | 2021081004 | 小丽 | 女  |   65 | NULL | NULL       |
  | 2021081005 | 小明 | 男  |   76 | 1002 | 2021081005 |
  | 2021081006 | 小李 | 男  |   81 | NULL | NULL       |
  +------------+------+-----+------+------+------------+
  
  -- 两次连接
  mysql> select * from student left join teach on student.sid = teach.sid
      -> left join teacher on teacher.tid = teach.tid;
  +------------+------+-----+------+------+------------+------+------+------+
  | sid        | name | sex | math | tid  | sid        | tid  | name | sex  |
  +------------+------+-----+------+------+------------+------+------+------+
  | 2001081001 | 小刚 | 男  | 90.5 | 1001 | 2001081001 | 1001 | 张三 | 男   |
  | 2021081002 | 小强 | 男  |   56 | 1001 | 2021081002 | 1001 | 张三 | 男   |
  | 2021081003 | 小红 | 女  | 87.5 | 1001 | 2021081003 | 1001 | 张三 | 男   |
  | 2021081003 | 小红 | 女  | 87.5 | 1003 | 2021081003 | 1003 | 王五 | 男   |
  | 2021081004 | 小丽 | 女  |   65 | NULL | NULL       | NULL | NULL | NULL |
  | 2021081005 | 小明 | 男  |   76 | 1002 | 2021081005 | 1002 | 李四 | 男   |
  | 2021081006 | 小李 | 男  |   81 | NULL | NULL       | NULL | NULL | NULL |
  +------------+------+-----+------+------+------------+------+------+------+
  
  -- 查询张三所教的学生
  mysql> select * from student left join teach on student.sid = teach.sid
      -> left join teacher on teach.tid = teach.tid where teacher.name = '张三';
  +------------+------+-----+------+------+------------+------+------+-----+
  | sid        | name | sex | math | tid  | sid        | tid  | name | sex |
  +------------+------+-----+------+------+------------+------+------+-----+
  | 2021081003 | 小红 | 女  | 87.5 | 1003 | 2021081003 | 1001 | 张三 | 男  |
  | 2021081003 | 小红 | 女  | 87.5 | 1001 | 2021081003 | 1001 | 张三 | 男  |
  | 2001081001 | 小刚 | 男  | 90.5 | 1001 | 2001081001 | 1001 | 张三 | 男  |
  | 2021081005 | 小明 | 男  |   76 | 1002 | 2021081005 | 1001 | 张三 | 男  |
  | 2021081002 | 小强 | 男  |   56 | 1001 | 2021081002 | 1001 | 张三 | 男  |
  +------------+------+-----+------+------+------------+------+------+-----+
  
  -- 查询张三所教的学生
  mysql> select * from student where sid in(select sid from teach where tid in (select tid from teacher where name = '张三'));
  +------------+------+-----+------+
  | sid        | name | sex | math |
  +------------+------+-----+------+
  | 2021081002 | 小强 | 男  |   56 |
  | 2001081001 | 小刚 | 男  | 90.5 |
  | 2021081003 | 小红 | 女  | 87.5 |
  +------------+------+-----+------+
  
  -- 查询张三所教的学生
  mysql> select s.sid, name,sex from student s left join teach t on t.sid = s.sid where tid = (select tid from teacher where name = '张三');
  +------------+------+-----+
  | sid        | name | sex |
  +------------+------+-----+
  | 2021081002 | 小强 | 男  |
  | 2001081001 | 小刚 | 男  |
  | 2021081003 | 小红 | 女  |
  +------------+------+-----+
  ```

* 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190530221543230.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg1ODIwMQ==,size_16,color_FFFFFF,t_70)

### 嵌套查询

我们可以将查询的结果作为另一个查询的条件，比如：

```sql
SELECT * FROM 表名 WHERE 列名 = (SELECT 列名 FROM 表名 WHERE 条件)
```

我们来再次尝试编写一下在最开始我们查找某教师所有学生的SQL语句。

***

## 数据库控制语言（DCL）

庞大的数据库不可能由一个人来管理，我们需要更多的用户来一起管理整个数据库。

### 创建用户

我们可以通过`create user`来创建用户：

```sql
CREATE USER 用户名 identified by 密码;

# 创建用户lsh
mysql> create user lsh;
```

也可以不带密码：

```sql
CREATE USER 用户名;
ALTER USER 'root'@'localhost' IDENTIFIED BY '密码';（更改密码）
ALTER USER ‘test’@‘localhost’ IDENTIFIED WITH MYSQL_NATIVE_PASSWORD BY ‘新密码’;
```

我们可以通过@来限制用户登录的登录IP地址，`%`表示匹配所有的IP地址，默认使用的就是任意IP地址。

### 登陆用户

首先需要添加一个环境变量，然后我们通过cmd去登陆mysql：

```sql
login -u 用户名 -p
```

输入密码后即可登陆此用户，我们输入以下命令来看看能否访问所有数据库：

```sql
show databases;
```

我们发现，虽然此用户能够成功登录，但是并不能查看完整的数据库列表，这是因为此用户还没有权限！

### 用户授权

我们可以通过使用`grant`来为一个数据库用户进行授权：

```sql
grant all|权限1,权限2...(列1,...) on 数据库.表 to 用户 [with grant option]

# 将study数据库的所有权限授予lsh用户
mysql> grant  all on study.* to lsh;

# 将study.student数据库的select，update(name)权限给lsh用户
grant select,update(name) on study.student to lsh;
```

其中all代表授予所有权限，当数据库和表为`*`，代表为所有的数据库和表都授权。如果在最后添加了`with grant option`，那么被授权的用户还能将已获得的授权继续授权给其他用户。

我们可以使用`revoke`来收回一个权限：

```sql
revoke all|权限1,权限2...(列1,...) on 数据库.表 from 用户

# 将study数据库的权限从lsh中收回
revoke all on study.* from lsh;
```

***

## 视图

视图本质就是一个查询的结果，不过我们每次都可以通过打开视图来按照我们想要的样子查看数据。既然视图本质就是一个查询的结果，那么它本身就是一个虚表，并不是真实存在的，数据实际上还是存放在原来的表中。

我们可以通过`create view`来创建视图;

```sql
CREATE VIEW 视图名称(列名) as 子查询语句 [WITH CHECK OPTION];

# 创建一个视图
mysql> create view test as select * from student;

# 对视图的数据进行修改，原数据也会改变
mysql> update test set math = 91 where sid like '%1001';
```

WITH CHECK OPTION是指当创建后，如果更新视图中的数据，是否要满足子查询中的条件表达式，不满足将无法插入，创建后，我们就可以使用`select`语句来直接查询视图上的数据了，因此，还能在视图的基础上，导出其他的视图。

1. 若视图是由两个以上基本表导出的，则此视图不允许更新。
2. 若视图的字段来自字段表达式或常数，则不允许对此视图执行INSERT和UPDATE操作，但允许执行DELETE操作。
3. 若视图的字段来自集函数，则此视图不允许更新。
4. 若视图定义中含有GROUP BY子句，则此视图不允许更新。
5. 若视图定义中含有DISTINCT短语，则此视图不允许更新。
6. 若视图定义中有嵌套查询，并且内层查询的FROM子句中涉及的表也是导出该视图的基本表，则此视图不允许更新。例如将成绩在平均成绩之上的元组定义成一个视图GOOD_SC： CREATE VIEW GOOD_SC AS SELECT Sno, Cno, Grade FROM SC WHERE Grade > (SELECT AVG(Grade) FROM SC); 　　导出视图GOOD_SC的基本表是SC，内层查询中涉及的表也是SC，所以视图GOOD_SC是不允许更新的。
7. 一个不允许更新的视图上定义的视图也不允许更新

通过`drop`来删除一个视图：

```sql
drop view test;#删除test视图
```

***

## 索引

在数据量变得非常庞大时，通过创建索引，能够大大提高我们的查询效率，就像Hash表一样，它能够快速地定位元素存放的位置，我们可以通过下面的命令创建索引：

```sql
-- 创建索引
CREATE INDEX 索引名称 ON 表名 (列名)

# 给student(name)创建一个索引i
create index i on student(name);
-- 查看表中的索引
show INDEX FROM student
```

我们也可以通过下面的命令删除一个索引：

```sql
drop index 索引名称 on 表名
```

虽然添加索引后会使得查询效率更高，但是我们不能过度使用索引，索引为我们带来高速查询效率的同时，也会在数据更新时产生额外建立索引的开销，同时也会占用磁盘资源。

***

## 触发器

触发器就像其名字一样，在某种条件下会自动触发，在`select`/`update`/`delete`时，会自动执行我们预先设定的内容，触发器通常用于检查内容的安全性，相比直接添加约束，触发器显得更加灵活。

触发器所依附的表称为基本表，当触发器表上发生`select`/`update`/`delete`等操作时，会自动生成两个临时的表（new表和old表，只能由触发器使用）

比如在`insert`操作时，新的内容会被插入到new表中；在`delete`操作时，旧的内容会被移到old表中，我们仍可在old表中拿到被删除的数据；在`update`操作时，旧的内容会被移到old表中，新的内容会出现在new表中。

```sql
CREATE TRIGGER 触发器名称 [BEFORE|AFTER] [INSERT|UPDATE|DELETE] ON 表名/视图名 FOR EACH ROW DELETE FROM student WHERE student.sno = new.sno

# 创建一个触发器再student表中数据被删除前自动触发
create trigger t before delete on student for each row delete from teach where old.sid = teach.sid;
```

 FOR EACH ROW表示针对每一行都会生效，无论哪行进行指定操作都会执行触发器！

通过下面的命令来查看触发器：

```sql
SHOW TRIGGERS
```

如果不需要，我们就可以删除此触发器：

```sql
DROP TRIGGER 触发器名称
```

***

## 事务

当我们要进行的操作非常多时，比如要依次删除很多个表的数据，我们就需要执行大量的SQL语句来完成，这些数据库操作语句就可以构成一个事务！只有Innodb引擎支持事务，我们可以这样来查看支持的引擎：

```sql
SHOW ENGINES;
```

MySQL默认采用的是Innodb引擎，我们也可以去修改为其他的引擎。

事务具有以下特性：

- **原子性：**一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
- **一致性：**在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
- **隔离性：**数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
- **持久性：**事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

我们通过以下例子来探究以下事务：

```sql
begin;   #开始事务
...
rollback;  #回滚事务
savepoint 回滚点;  #添加回滚点
rollback to 回滚点; #回滚到指定回滚点
...
commit; #提交事务
-- 一旦提交，就无法再进行回滚了！
```

***

## 选学内容

**函数**和**存储过程**并没有包含在我们的教程当中，但是这并不代表它们就不重要，通过学习它们能够让你的数据库管理能力更上一层楼，它们能够捆绑一组SQL语句运行，并且可以反复使用，大大提高工作效率。

