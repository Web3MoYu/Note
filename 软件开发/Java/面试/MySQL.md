一、知识体系
------

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9ee93739afcfa97f279e3a9e39929873.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/5812a4eed92a732ec3fad3d2231d3577.png)

二、Mysql-优化
----------

### 1\. 优化-如何定位慢查询

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a04f104dd11e421ccd8c18a650b68d3a.png)

1.聚合查询
2.多表查询
3.表数据量过大查询
4.深度分页查询

表象：页面加载过慢、接口压测响应时间过长（超过1s)

#### ② 解决方案

方案一：开源工具 
调试工具：Arthas 
运维工具：Prometheus 、**Skywalking** 
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3b2ea265c2d344e9eafe4afbb9ad3277.png)
方案二：Mysql自带**慢日志** 
慢查询日志记录了所有执行时间超过指定参数(long\_query\_time，单位:秒，默认10秒）的所有SQL语句的日志如果要开启慢查询日志，需要在MySQL的配置文件(/etc/my.cnf)中配置如下信息： 
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3a1a5db2281ecba85e2f0aadd85f9566.png) 
配置完毕之后，通过以下指令重新启动MySQL服务器进行测试，查看慢日志文件中记录的信息/var/lib/mysql/localhost-slow.log 
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ea4f01ecd42b2c87b784e5db3976d08d.png)

#### ③ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9ff35c27478077dccca722e058a1021b.png)

#### ④ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/671774cec9f23c90688554bdaeab33b9.png)

### 2\. 优化-sql执行很慢，如何解决

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/2bad78700007df14207323f4e0af3926.png)

1.聚合查询：新增临时表
2.多表查询：优化sql语句结构
3.表数据量过大查询：添加索引
4.深度分页查询

#### ② 解决方案

一个SQL语句执行很慢，如何分析？

可以采用EXPLAIN 或者DESC命令获取MySQL如何执行SELECT语句的信息 
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/12c4017ea93ce798172c484c22b771d2.png)
展示SQL执行的情况，部分字段说明如下： 
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c170bd1946eca5702bc1f319b8d73353.png)

1.possible_key 当前sql可能会使用到的索引
2.key 当前sql实际命中的索引
3.key_len 索引占用的大小
4.Extra 额外的优化建议 
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0175b9ef9f9e2263d41699ebd188550a.png)
5.type这条sql的连接的类型，性能由好到差为NULL、system、const、 eq_ref、ref、range、index、all 
system：查询系统中的表 
const：根据主键查询（常用） 
eq_ref：sql的查询条件是主键索引查询或唯一索引查询（返回一条数据）
ref：sql的查询条件是索引查询（返回多条数据）
range：索引范围查询
index：全索引查询，遍历整个索引数，效率不高（需要优化）
all：全盘扫描（需要优化）

#### ③ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c71ccfb36686a865724799719b30e02e.png)
可以采用MySQL自带的分析工具EXPLAIN

1.通过key和key_len检查是否命中了索引(索引本身存在是否有失效的情况)
2.通过type字段查看sql是否有进一步的优化空间，是否存在全索引扫描或全盘扫描
3.通过extra建议判断，是否出现了回表的情况，如果出现了，可以尝试添加索引或修改返回字段来修复

#### ④ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/30236d87b23c60ec2355f301f3c16836.png)

### 3\. 优化-索引概念

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ebb636cea26b5b925f95ece9ef9cb0cf.png)
索引(index) 是帮助MySQL高效获取数据的数据结构(有序)。在数据之外，数据库系统还维护着满足特定查找算法的数据结构（B+树)，这些数据结构以某种方式引用 (指向）数据，这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。 
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/5cd8b93fde9e14e6b309a3dea992c8ee.png)
维护树的数据结构，提高查找效率，减少IO的操作

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0808814013a256b21baa2cf03f60e824.png)
B+树、二叉树、红黑树、B树

#### ② 数据结构对比

MySQL默认使用的索引底层数据结构是B+树。再聊B+树之前，我们先聊聊二叉树和B树
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/17290e3d34b0aedc284c1f0639532045.png)
**B-Tree**，B树是一种多叉路衡查找树，相对于二叉树，B树每个节点可以有多个分支，即多叉。以一颗最大度数(max-degree)为5(5阶)的b-tree为例，那这个B树每个节点最多存储4个key
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/1059b914544ce55805b1a4d221fc848c.png)
**B+Tree**是在BTree基础上的一种优化，使其更适合实现外存储索引结构，InnoDB存储引擎就是用B+Tree实现其索引结构
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ebcdb7142dde4a3cb85521f1cc684c1f.png)

B树与B+树对比:

1.磁盘读写代价B+树更低（非叶子节点不存储数据）;
2.查询效率B+树更加稳定（数据都存储在叶子节点）;
3.B+树便于扫库和区间查询（叶子节点之间使用的双向指针）

#### ③ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6d6e3a2ed40bbda86003b24b737db2ce.png)

#### ④ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/792a79e7fee8da5818c30ca954fa4634.png)

### 4\. 优化-聚簇索引与非聚簇索引

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6b1924a0dd068c1e7794332bb27b55e9.png)

1.什么是聚集索引
2.什么是二级索引（非聚集索引)
3.什么是回表?

#### ② 聚簇索引

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/dec9bbaa95e6f996ab94621746d31aeb.png)
聚集索引选取规则:
1.如果存在主键，主键索引就是聚集索引。
2.如果不存在主键，将使用第一个唯一(UNIQUE)索引作为聚集索引。
3.如果表没有主键，或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为隐藏的聚集索引。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e2f18b62af45d983430e495713a5cb04.png)
回表查询：先通过二级索引找到对应的主键值，在用主键值在聚集索引中去找整行的数据

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/92a6d37ed7bb515ea6badee4dcc12b50.png)

#### ③ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/84979f7bb61054f7ebc87868a7edc246.png)

#### ④ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e71acdd2186ba2752803b79f39859181.png)

### 5\. 优化-覆盖索引

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/34ecaadbf77b143c87fc4909eee59d47.png)
**覆盖索引**是指查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/293ba0cf8abe97230ddf7a8de66baca0.png)
判断下面的SQL哪些是覆盖索引，为什么？

select * from tb_user where id = 1
是，因为根据id查询的，id默认是主键索引，就是聚簇索引，聚簇索引中对应的是整行的记录

select id，name from tb_user where name = 'Arm'
是，因为通过name这个二级索引也可以找到id，是一次性可以查询出来id和name的

select id,name，gender from tb_user where name = 'Arm'
不是，需要通过回表查询才能获取到gender


#### ② 覆盖索引

覆盖索引是指查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/950a65ff7278ed0de7163f30b8dbaf12.png)

#### ③ Mysql超大分页处理

在数据量比较大时，如果进行limit分页查询，在查询时，越往后，分页查询效率越低。
我们一起来看看执行limit分页查询耗时对比:
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/afc8f50b58e5608f347e651b276eb3d3.png)因为，当在进行分页查询时，如果执行limit 9000000,10，此时需要MySQL排序前9000010记录，仅仅返回9000000 - 9000010 的记录，其他记录丢弃，查询排序的代价非常大。

**优化思路：**
一般分页查询时，通过创建覆盖索引能够比较好地提高性能，可以通过覆盖索引加子查询形式进行优化
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/94f813c4b55690810b0a4ee959f72475.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ceb83feb6a27b73aef61455cbe72266d.png)

#### ④ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a462d4a4ea502484e07c4bf2d91dc241.png)

#### ⑤ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b34aa79a310c162479e3214ec420cb3e.png)

### 6\. 优化-索引创建的原则

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/643c2dac6a36017322c2d440fbf82e84.png)
先陈述自己在实际的工作中是怎么用的
主键索引
唯一索引
根据业务创建的索引(复合索引)

#### ② 索引创建的原则

1.针对于**数据量较大**，且**查询比较频繁**的**表**建立索引。单表超过10万数据 (增加用户体验)

2.针对于常作为**查询条件**(where)、**排序**（order by)、**分组**(group by) 操作的**字段**建立索引。

3.尽量选择**区分度高的列**作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高（比如一个地区字段，大部分都是北京市，就不适合创建索引）。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/65e406f19699bc85303695f2caae34a1.png)

4.如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立**前缀索引**（比如简介内容想要创建索引，就采用前缀索引）。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9d46b29f630f04c7ed8ca13549b094b3.png)

5.尽量使用联合索引，减少单列索引，查询时，**联合索引**很多时候可以**覆盖索引**，节省存储空间，避免回表，提高查询效率。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ce5e8295723578992b3a9c04b617ba82.png)

6.要控制索引的数量，索引并不是多多益善，**索引越多**，**维护索引**结构的**代价也就越大**，会**影响增删改的效率**。

7.如果索引列**不能存储NULL值**，请在**创建表**时使用**NOT NULL约束**它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询。


#### ③ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4fec094a18e0309e2eda9cde6828b488.png)

#### ④ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/dd91b74833001bd5d75b731bb2bdf796.png)

### 7\. 优化-什么情况下索引会失效

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/1c963bc0a02a300f2797f2c296d70110.png)
索引失效的情况有很多，可以说一些自己遇到过的。
给tb_seller创建联合索引，字段顺序: name,status,address
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c13305f96bce70b2772fbc3180057207.png)
那快读判断索引是否失效了呢?执行计划explain

#### ② 什么情况下索引会失效

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/163daad38b4c8fc532e8cc1b29799d26.png)

1.违反最左前缀法则
如果索引了多列，要**遵守最左前缀法则**。指的是查询从索引的最左前列开始，并且不跳过索引中的列。匹配最左前缀法则，走索引。
有效的情况如下图所示：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/83c39b0e21e87d2b7ade933ba03f2a24.png)
失效的情况如下图所示（违反最左前缀法则）：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/89140d9ead2da5ca18fb9cb2455f3f33.png)

2.范围查询右边的列，不能使用索引。
根据前面的两个字段name , status查询是走索引的，但是最后一个条件address没有用到索引。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9d76477f293af5a837a708c08826397b.png)

3.不要在**索引列上进行运算操作**，索引将失效。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/7a96ba56b55f3fe321ed70a104cad407.png)

4.字符串不加单引号，造成索引失效。
由于，在查询是，没有对字符串加单引号，MySQL的查询优化器，会自动的进行**类型转换**，造成索引失效。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4e1d493e6384d5e37615cd25404cdc0c.png)

5.以%开头的Like模糊查询，索引失效。如果仅仅是尾部模糊匹配，索引不会失效。如果是头部模糊匹配，索引失效。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f4fb9e74886101d581caeee2eb2c8114.png)


#### ③ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/918dbb1338b4159bd9260f7f1b282cb0.png)

#### ④ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/5e06c4abd404ca82e518a35e7fa5d958.png)

### 8\. 优化-SQL优化的经验

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a3222d020c316900a56ac9d8ccd06681.png)
表的设计优化
索引优化（参考优化创建原则和索引失效）
SQL语句优化
主从复制、读写分离
分库分表

#### ② sql优化经验

1.**表**的设计优化(参考阿里开发手册《嵩山版》)
a. 比如设置**合适的数值**(tinyint int bigint)，要根据实际情况选择
b. 比如设置**合适的字符串类型**(char和varchar) char定长效率高.varchar可变长度，效率稍低
2.**SQL语句**优化
a. SELECT语句务必**指明字段名称**（避免直接使用select* )
b. SQL语句要**避免造成索引失效**的写法
c. 尽量用**union all**代替union，union会多一次过滤，效率低
d. 避免在where子句中**对字段进行表达式操作**
e. Join优化能用**inner join**就不用left join right join，如必须使用一定要以小表为驱动，内连接会对两个表进行优化，优先把小表放到外边，把大表放到里边。left join或right join，不会重新调整顺序
3.主从复制、**读写分离**
如果数据库的使用场景**读的操作比较多**的时候，为了避免写的操作所造成的性能影响可以采用读写分离的架构。
读写分离解决的是，**数据库的写入，影响了查询的效率**。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3371cee442eb08774fe8fcd99dc1039a.png)

#### ③ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f8205599a49a111d47de572f6efd8723.png)

#### ④ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a4c79d7a820ad0ccc8f028db0d130d88.png)

三、Mysql-其他面试题
-------------

### 1\. 事务的特性ACID

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/416e8548b5698acf61039d9b27226b59.png)
事务是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

#### ② ACID是什么

1.原子性(Atomicity) :事务是不可分割的最小操作单元，**要么全部成功，要么全部失败**。
2.一致性(Consistency) :事务完成时，必须使**所有的数据都保持一致状态**。
3.隔离性(Isolation) :数据库系统提供的隔离机制，保证事务**在不受外部并发操作影响的独立环境下运行**。
4.持久性(Durability) :事务一旦提交或回滚，它对数据库中的**数据的改变就是永久的**。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a0f3a556383080f6ed83eff206a9dc5e.png)

#### ③ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/fa03fecafad5a73b99a008e9b38d203a.png)

#### ④ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/7d8bc1d298fa68f57e9286a8da357a15.png)

### 2\. 并发事务

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f0088cfb52d584225c4c11a15cddd2fa.png)
● 并发事务问题：脏读、不可重复读、幻读
● 隔离级别：读未提交、读已提交、**可重复读**、串行化

#### ② 并发事务问题

1.脏读：一个事务读到另外一个事务还没有提交的数据。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/122bda13ee56febfaa221fa50109d7d7.png)

2.不可重复读：一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/78c2b524d29ba6c64450276d45257b59.png)

3.幻读：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在，好像出现了“幻影”。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/dab77d88db02f06f5157f528ef2d7f5e.png)


#### ③ 事务隔离

对并发事务进行隔离，有以下隔离级别，如下图所示：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/24215bd601b3ec16530424a87ebf1eb7.png)
注意：事务隔离级别越高，数据越安全，但是性能越低。

#### ④ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3a946e383703469081a2b5a4ed25308a.png)

#### ⑤ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f0f0bdf2312fdb90e291ada840cb49a2.png)

### 3\. 事务-undo log和redo log的区别

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/5bb5c7883554bbbdd747634b312ccd77.png)
undo log和redo log都是mysql的日志文件，但是功能不一样。

1.**缓冲池**（buffer pool）：主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增删改查操作时，先操作缓冲池中的数据(若缓冲池没有数据，则从磁盘加载并缓存)，以一定频率刷新到磁盘，从而减少磁盘IO,加快处理速度
2.**数据页**（page）：是InnoDB 存储引擎**磁盘管理**的最小单元，每个页的大小默认为16KB。页中存储的是行数据
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c6e3ecad637bccd22eee26b238bd75e9.png)

#### ② redo log重做日志

redo log 重做日志，记录的是事务提交时数据页的物理修改，**是用来实现事务的持久性**。
该日志文件由两部分组成：重做日志缓冲（redo log buffer） 以及重做日志文件（redo log file） ，前者是在内存中，后者在磁盘中。当事务提交之后会把所有修改信息都存到该日志文件中，用于**在刷新脏页到磁盘，发生错误时，进行数据恢复使用**。（每个一段时间会进行清理）
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4d3ca939082bb02540fc600351904e54.png)

#### ③ undo log回滚日志

undo log 回滚日志，用于记录数据被修改前的信息，作用包含两个：**提供回滚**和**MVCC**(多版本并发控制)。undo log和redo log记录物理日志不一样，它是**逻辑日志**。

1.可以认为当delete一条记录时，undo log中会记录一 条对应的insert记录， 反之亦然,
2.当update一条记录时, 它记录一条对应相反的update记录。 当执行rollback时， 就可以从undo log中的逻辑记录读取到相应的内容并进行回滚。
3.undo log可以实现事务的一致性和原子性

#### ④ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/fb7c4d236223f7005051d184a7b9b31c.png)

#### ⑤ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/aaf907c3026b8d2117e0aadb5759198c.png)

### 4\. 事务-MVCC多版本并发控制（难）

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/585078b3b4a12a68590066db09c4d1b8.png)
锁：排他锁(如一 个事务获取了一个数据行的排他锁，其他事务就不能再获取该行的其他锁)
mvcc：多版本并发控制

#### ② MVCC多版本并发控制

MVCC全称Multi-Version Concurrency Control，多版本并发控制。指维护一个数据的多个版本，使得读写操作没有冲突

MVCC的具体实现，主要依赖于数据库记录中的**隐式字段**、**undo log日志**、**readView**。

例如：下面有4个事务，事务2、3、4对该记录都进行了操作，那么在事务5中的两条查询语句是查询的哪个事务版本的记录呢？
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8d7212b5c86b61739946ba7cce75428d.png)

**MVCC的实现原理：**

1.记录中的隐藏字段
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d1f0bfbeb3730300f702e6db7cf79c42.png)

2.undo log 回滚日志
a. 回滚日志，在insert、 update、 delete的时候产生的便于数据回滚的日志。
b. 当**insert**的时候，产生的undo log日志只在回滚时需要，在事务提交后，可被**立即删除**。
c. 而**update、delete**的时候， 产生的undo log日志不仅在回滚时需要，mvcc版本访问也需要，**不会立即被删除**。

3.undo log 版本链
不同事务或相同事务对同一条记录进行修改，会导致该记录的undo log生成一条记录版本链表，链表的**头部**是**最新的旧记录**，链表**尾部**是**最早的旧记录**。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/31df0637b4e5bf44a6478df27afd6b4c.png)

4.readview
ReadView (读视图)是 **快照读** SQL执行时MVCC提取数据的依据，记录并维护系统当前活跃的事务(未提交的) id。
a. **当前读**：读取的是记录的**最新版本**，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行**加锁**。对于我们日常的操作，如:
select … lock in share mode(共享锁)，select … for update、update、 insert、 delete(排他锁)都是一 种当前读。
b. **快照读**：简单的select (不加锁)就是快照读,快照读,读取的是记录数据的可见版本，**有可能是历史数据**，**不加锁**，是非阻塞读。
Read Committed：每次select, 都生成一个快照读。
Repeatable Read：开启事务后第一个select语句才是快照读的地方。


举例说明：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9e3789069eeda229f24d0310416ba9eb.png)
当前读：不受隔离级别影响，事务A的两次select都是读到的最新的数据
快照读：不同的隔离级别不一样，RC读出来不一样，RR读出来都是相同的，获得的结果就是快照读

ReadView包含的四个核心字段：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a6330a5181d43b8750a3ac71a529eb50.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8986ced14846f717cf850dd3290c75d6.png)
不同的隔离级别，生成ReadView的时机不同：
➢ READ COMMITTED ：在事务中每一 次执行快照读时生成ReadView。
➢ REPEATABLE READ：仅在事务中第一 次执行快照读时生成ReagView，后续复用该ReadView。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9fccdaeb11a66b70b77e8421da12beac.png)![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a292d30ae789eabcd39d6e574a2813df.png)

#### ③ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/1ccf8dfde4d8f7241661546ce2b052ba.png)

#### ④ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/095c78e0d2d8d1ccc63dc20cda2469e4.png)

### 5\. Mysql[主从同步](https://so.csdn.net/so/search?q=%E4%B8%BB%E4%BB%8E%E5%90%8C%E6%AD%A5&spm=1001.2101.3001.7020)原理

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/2a73a48930981a7a92416156f3a46f86.png)
主从架构：主库用于写数据，从库用于读数据
主库与从库如何进行同步的？
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0ab3f2e81378d0712bd9fad3f6f88ce0.png)

#### ② 主从同步原理

MySQL主从复制的核心就是**二进制日志**
二进制日志(bin log) 记录了所有的 DDL (数据定义语言，增删改查表结构) 语句和 DML (数据操纵语言，增删改查表中的数据) 语句，但不包括数据查询 (SELECT、 SHOW) 语句。

复制分成三步:

1.Master 主库在事务提交时，会把数据变更记录在二进制日志文件Binlog中。
2.从库读取主库的二进制日志文件Binlog，写入到从库的中继日志Relay Log。
3.slave重做中继日志中的事件，将改变反映它自己的数据。

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c2a551e9b00dee1dfe32222e1eb5a6f3.png)

#### ③ 问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/38e16aa53c8766d6131606f42210a537.png)

#### ④ 实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e682608261e2d4de541c6b1069e025a0.png)

### 6\. Mysql分库分表

#### ① 问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/99ce1b614c195894d5594a3f2567cd76.png)
主从架构，读写分离，分担了访问压力
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6f27d21d695212ddce14f2218023cda5.png)
分库分表的时机：

1.**前提**，项目业务数据逐渐增多，或业务发展比较迅速（单表的数据量达到1000W或20G以后）
2.优化已解决不了性能问题（主从读写分离、查询索引…）
3.IO瓶颈（磁盘IO、网络IO)、CP\\瓶颈（聚合查询、连接数太多）

分库分表主要解决存储的压力
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/04306a4238ee8eace682fede990e23f3.png)

#### ② 拆分策略

垂直拆分与水平拆分
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8e829cba0f1c1ede099a4b1c234f20a4.png)

垂直拆分之**垂直分库**：以表为依据，根据业务将不同表拆分到不同库中。
特点:
1.按业务对数据分级管理、维护、监控、扩展
2.在高并发下，提高磁盘IO和数据量连接数

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3c3e8b90f0db6522cb0d9607e253659c.png)

垂直拆分之**垂直分表**：以字段为依据，根据字段属性将不同字段拆分到不同表中。
**特点:**
1.冷热数据分离
2.减少IO过渡争抢，两表互不影响
**拆分规则：**
● 把不常用的字段单独放在一张表
● 把text、blob等大字段拆分出来放在附表中
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/999391cec6bcf16755fcf0a9ab7e5885.png)

水平拆分之**水平分库**：将一个库中的数据拆分到多个库中。
**特点:**
1.解决了单库大数量，高并发的性能瓶颈问题
2.提高了系统的稳定性和可用性
**路由规则**
● 根据id节点取模
● 按id也就是范围路由， 节点1(1-100万),节点2(100万-200万)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/56fbfee44a30fbb2d5320ecfd8dee7fa.png)

水平拆分之**水平分表**：将一个表的数据拆分到多个表中(可以在同一个库内)。
**特点:**
1.优化单一表数据量过大而产生的性能问题；
2.避免IO争抢并减少锁表的几率；
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4760aa9b98291c67e886d7c4c4c0c0b8.png)

**分库分表的策略有哪些？**
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b5ba86349b6547bee5cc60942eca7594.png)

#### ③ 问题总结

常用垂直分库和垂直分表
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c27139a6b6fba7948d74be492f35886b.png)