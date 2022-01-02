## MySQL高级篇

### 一、架构介绍

#### 逻辑架构介绍

和其它数据库相比，MySQL的架构可以在多种不同的场景中应用并发挥良好作用。主要体现在存储引擎的架构上，**插件式的存储引擎架构将查询处理和其它的系统任务以及数据的存储提取相分离。**

![image-20211221080037339](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211221080037339.png)

* ##### 连接层

  最上层是一些客户端和连接服务，包含本地socket通信和大多数基于客户端/服务端工具实现的类似于TCP/IP的通信。主要完成一些类似于连接处理、授权认证及相关的安全方案。在该层上引入了线程池的概念，为通过认证安全接入的客户端提供线程。同样在该层上可以实现基于SSL的安全连接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。

* ##### 服务层

  服务层主要完成大多数的核心服务功能，如SQL接口、缓存的查询、SQL的分析和优化以及部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程、函数等。在 该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化如确定查询表的顺序、是否利用索引等，最后生成相应的执行操作。如果是select语句，服务器还会查询内部缓存。如果缓存空间足够大，这样在解决大量读操作的环境中能够很好的提升系统的性能。

* ##### 引擎层

  在该层，存储引擎真正负责MySQL中数据的存储和提取，服务器通过API与存储引擎进行通信。不同的存储引擎具有的功能不同，可以根据自己的实际需要进行选取，目前比较流行的是MyISAM和InnoDB。

* 存储层

  该层主要是将数据存储在运行于裸设备的文件系统之上，并完成与存储引擎的交互。

#### 存储引擎

查看系统支持的存储引擎

```mysql
show engines;
```

查看当前使用的存储引擎

```mysql
show variables like '%storage_engine%'; 
```

##### MyISAM和InnoDB的比较

| 对比项   | MyISAM                                                     | InnoDB                                                       |
| -------- | ---------------------------------------------------------- | ------------------------------------------------------------ |
| 主外键   | ×                                                          | √                                                            |
| 事务     | ×                                                          | √                                                            |
| 行表锁   | 表锁，即使操作一条记录也会锁住整个表，不适合高并发的操作。 | 行锁，操作时只锁某一行，不对其它行有影响，**适合高并发操作** |
| 缓存     | 只缓存索引，不缓存真是数据                                 | 不仅缓存索引还要缓存真是数据，对内存要求较高，而且内存大小对性能有决定性影响。 |
| 表空间   | 小                                                         | 大                                                           |
| 关注点   | 性能                                                       | 事务                                                         |
| 默认安装 | √                                                          | √                                                            |



### 二、索引优化分析

#### SQL性能下降的原因（执行时间长，等待时间长）

* 查询语句有问题
* 索引失效
* 关联查询太多JOIN（设计缺陷或不得已的需求）
* 服务器调优及各个参数的不合理设置（缓冲、线程数等）

#### 常见的join查询

##### SQL的执行顺序

* 手写

  ```mysql
  SELECT
  DISTINCT <select_list>
  FROM <left_table>
  ON <join_condition>
  <join_type> JOIN <right_table>
  WHERE <where_condition>
  GROUP BY <group_by_list>
  HAVING <having_condition>
  ORDER BY <order_by_condition>
  LIMIT <limite_number>
  ```

* 机读

  ```mysql
  FROM <left_table>
  ON <join_condition>
  <join_type> JOIN <right_table>
  WHERE <where_condition>
  GROUP BY <group_by_list>
  HAVING <having_condition>
  SELECT
  DISTINCT <select_list>
  ORDER BY <order_by_condition>
  LIMIT <limite_number>
  ```

* 总结

![image-20211221084150014](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211221084150014.png)

##### JOIN图

七种JION关系：表A和表B可以分别代表两个集合A和B。

1.A内连接B：A∩B

>select <select list>
>
>from TableA A
>
>inner joinTableB B
>
>on A.key = B.key

2.A左连接B：A

>select <select list>
>
>from TableA A
>
>left join TableB B
>
>on A.key = B.key

3.A右连接B：B

>select <select list>
>
>from TableA A
>
>right join TableB B
>
>on A.key = B.key

4.A左连接B，且仅A：A-(A ∩ B)

>select <select list>
>
>from TableA A
>
>left joinTableB B
>
>on A.key = B.key
>
>where B.key is null;

5.A右连接B，且仅B：B-(A ∩ B)

>select <select list>
>
>from TableA A
>
>right join TableB B
>
>on A.key = B.key
>
>where A.key is null

6.A全连接B：A∪B。注意MySQL不支持full outer join，但可以用

>select <select list>
>
>from TableA A
>
>full outer join TableB B
>
>on A.key = B.key

7.A全连接B，且非A∩B：(A ∪ B) - (A ∩ B)

>select <select list>
>
>from TableA A
>
>full outer join TableB B
>
>on A.key = B.key
>
>where A.key is null or B.key is null;

##### 建表SQL

```mysql
-- 部门表建表语句
create table if not exists tbl_dept(
	id int(11) not null auto_increment,
	deptName varchar(30) default null,
	locAdd varchar(40) default null,
	PRIMARY key (id)
)engine = INNODB auto_increment=1 default charset = utf8;

-- 员工表建表语句
create table if not exists  tbl_emp(
	id int(11) not null auto_increment,
	name varchar(20) default null,
	deptId INT(11) default null,
	primary key (id),
	key `fk_dept_id` (deptId)
)engine = INNODB auto_increment=1 default charset = utf8;

-- 插入部门数据
insert into tbl_dept(deptName, locAdd) values('RD',11);
insert into tbl_dept(deptName, locAdd) values('HR',12);
insert into tbl_dept(deptName, locAdd) values('MK',13);
insert into tbl_dept(deptName, locAdd) values('MIS',14);
insert into tbl_dept(deptName, locAdd) values('FD',15);


-- 插入员工数据
insert into tbl_emp(name, deptId) values('z3',1);
insert into tbl_emp(name, deptId) values('z4',1);
insert into tbl_emp(name, deptId) values('z5',1);

insert into tbl_emp(name, deptId) values('w5',2);
insert into tbl_emp(name, deptId) values('w6',2);

insert into tbl_emp(name, deptId) values('s7',3);

insert into tbl_emp(name, deptId) values('s8',4);

insert into tbl_emp(name, deptId) values('s9',51);
```

##### 7种JOIN

```mysql
-- 令TableA = tbl_emp, TableB = tbl_dept；写出7种JOIN关系
-- 1.A内连接B：A∩B
select*
from tbl_emp a
inner join tbl_dept b
on a.deptId = b.id;

-- 2.A左连接B：A
select *
from tbl_emp a
left  join tbl_dept b
on a.deptId = b.id;

-- 3.A右连接B：B
select *
from tbl_emp a
right join tbl_dept b
on a.deptId = b.id;

-- 4.A左连接B，且仅A：A-(A ∩ B)
select *
from tbl_emp a
left join tbl_dept b
on a.deptId = b.id
where b.id is null;

-- 5.A右连接B，且仅B：B-(A ∩ B)
select *
from tbl_emp a
right join tbl_dept b
on a.deptId = b.id
where a.deptId is null;

-- 6.A全连接B：A∪B
select *
from tbl_emp a
left   join tbl_dept b
on a.deptId = b.id
union 
select a.*, b.*
from tbl_emp a
right join tbl_dept b
on a.deptId = b.id;

-- 7.A全连接B，且非A∩B：(A ∪ B) - (A ∩ B)
select *
from tbl_emp a
left join tbl_dept b
on a.deptId = b.id
where b.id is null
union 
select a.*,b.*
from tbl_emp a
right join tbl_dept b
on a.deptId = b.id
where a.deptId is null;
```

#### 索引介绍

##### 是什么

>索引(Index)是帮助MySQL高效获取数据的数据结构。
>
>可以理解为“排好序的快速查找数据结构”。所以它会影响where和order by 。
>
>数据本身之外，数据库还维护着一个满足特定查找算法的数据结构，这些数据结构以某种方式指向数据，这样就可以在这些数据结构的基础上实现高级查找算法，这种数据结构就是索引。
>
>一般来说索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式存储在磁盘上。

##### 优势

>1.类似大学图书馆建书目索引，可以**提高数据检索的效率**，降低数据库的IO成本。
>
>2.通过索引列对数据进行排序，**降低数据排序的成本**，降低了CPU的消耗。

##### 劣势

>1.实际上索引也是一张表，保存了主键与索引字段，并指向实体表的记录，所以索引列也要占用磁盘空间。
>
>2.虽然索引大大提高了查询速度，但是会降低更新表的速度。以为MySQL会在更新表的时候同时更新它的索引。
>
>3.索引只是提高效率的一个因素，如果表的数据量巨大，就需要花时间建立最优秀的索引或优化查询。

##### MySQL索引分类

* 单值索引

>一个索引只包含单个列，一个表可以有多个单列索引。

* 唯一索引

>索引列的值必须唯一，但允许有空值。

* 复合索引

>即一个索引包含多个列

基本语法

>创建：
>
>CREATE [UNIQUE] INDEX indexName ON mytable(columnname(length));
>
>ALTER mytable ADD [UNIQUE] INDEX [indexName] ON (columnname(length));
>
>加了unique就是唯一索引；columnname(length)单个就是单值索引，多个就是复合索引。
>
>删除：
>
>DROP INDEX [indexName] ON mytable;
>
>查看：
>
>SHOW INDEX FROM tableName;
>
>修改：给表添加索引，4种方法
>
>// 添加一个主键，意味着索引值必须唯一且非NULL
>
>ALTER TABLE tableName ADD PRIMARY KEY (column_list); 
>
>// 索引值唯一（除了NULL外，NULL可能出现多次）
>
>ALTER TABLE tableName ADD UNIQUE index_name(column_list);
>
>// 添加普通索引，索引值可出现多次
>
>ALTER TABLE tableName ADD INDEX index_name(column_list);
>
>// 指定索引为FULLTEXT，用于全文索引
>
>ALTER TABLE tableName ADD FULLTEXT index_name(column_list); 
>
>

建议：一张表的索引数最好不超过5个。

##### MySQL索引结构：4种

* **BTree索引**★

>TODO

* Hash索引
* full-text全文索引
* R-Tree索引

##### 哪些情况下需要创建索引

>1.主键自动建立唯一索引
>
>2.频繁作为查询条件的字段应该创建索引
>
>3.查询中与其他表关联的字段，外键关系建立索引。比如员工表中的部门编号
>
>4.单值/组合索引的选择问题：在高并发下倾向创建组合索引
>
>5.查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度
>
>6.查询中统计或者分组字段 

##### 哪些情况下不要创建索引

>1.表记录太少（300万）
>
>2.经常更新的表
>
>3.某列包含许多重复的内容
>
>4.where条件里用不到的字段不建索引

#### 性能分析

##### MySQL Query Optimizer



##### MySQL常见瓶颈

>1.CPU：CPU在饱和的时候一般发生在数据装入内存或从磁盘上读取数据的时候
>
>2.IO：磁盘I/O瓶颈发生在装入数据远远大于内容容量的时候
>
>3.硬件本身的性能瓶颈：通过top、free、iostat和vmstat来查看系统的性能状态。
>
>

##### Explain

* ###### 是什么（查看执行计划）

使用explain关键字可以模拟优化器执行SQL查询语句，从而知道MySQL是如何处理你的SQL语句的。分析你的查询语句或表结构的性能瓶颈。

* ###### 能干嘛

  * 表的读取顺序
  * 数据读取操作的操作类型
  * 哪些索引可以使用
  * 哪些索引被实际使用
  * 表之间的引用
  * 每张表有多少行被优化器查询

* ###### 怎么玩

语法：

​	EXPLAIN + SQL语句

执行计划包含的信息：

​	id、select_type、table、type、possible_keys、key、key_ken、ref、rows、Extra

* ###### 各字段解释

```MYSQL
# 建表语句
create table t1 (
	id bigint(18) PRIMARY key,
	other_column varchar(20) default null
);

create table t2 (
	id bigint(18) PRIMARY key,
	other_column varchar(20) default null
);
create table t3 (
	id bigint(18) PRIMARY key,
	other_column varchar(20) default null
);
```

**id**★

>select查询的序列号，包含一组数字，表示查询中执行select子句或操作表的顺序
>
>三种情况：
>
>* id相同：执行顺序由上至下
>
>```mysql
>explain
>select t2.*
>from t1, t2, t3
>where t1.id = t2.id and t1.id = t3.id and t1.other_column = '';
>```
>
>![image-20211223083510399](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211223083510399.png)
>
>
>
>* id不同：如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行
>
>```mysql
>explain
>select t2.*
>from t2
>where id = ( select id
>							from t1
>							where id = (select t3.id
>														from t3
>														where t3.other_column = ''));
>```
>
>![image-20211223083934910](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211223083934910.png)
>
>
>
>* id相同不同，同时存在：id越大越先执行；id相同，顺序执行。
>
>```mysql
>explain
>select t2.* from (
>	select t3.id
>	from t3
>	where t3.other_column = '') s1, t2
>where s1.id = t2.id;
># 执行顺序：t3 -> s1 -> t2
># DERIVED：衍生
>```
>
>![image-20211223221836629](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211223221836629.png)
>
>

**select_type**

>查询的类型，主要用来区别普通查询、联合查询、子查询等的复杂查询。
>
>常见值有：
>
>SIMPLE：简单的select查询，查询中不包含子查询或UNION
>
>```mysql
>explain select * from t1;
>```
>
>![image-20211223223030142](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211223223030142.png)
>
>PRIMARY：查询中若包含任何复杂的子查询，最外层查询则被标记为PRIMARY
>
>```MYSQL
>explain
>select t2.*
>from t2
>where id = ( select id
>							from t1
>							where id = (select t3.id
>														from t3
>														where t3.other_column = ''));
>```
>
>![image-20211223083934910](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20211223083934910.png)
>
>SUBQUERY：在SELECT或WHERE列表中包含了子查询
>
>```mysql
>explain
>select t2.*
>from t2
>where id = ( select id
>							from t1
>							where id = (select t3.id
>														from t3
>														where t3.other_column = ''));
>```
>
>![image-20211225210515161](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225210515161.png)
>
>DERIVED：在FROM列表中包含的子查询被标记为DERIVED(衍生)，MySQL会递归执行这些子查询，把结果放在临时表里。
>
>```mysql
>explain
>select t2.* from (
>	select t3.id
>	from t3
>	where t3.other_column = '') s1, t2
>where s1.id = t2.id;
>```
>
>![image-20211225210456526](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225210456526.png)
>
>UNION：若第二个SELECT出现在UNION之后，则被标记为UNION；若UNION包含在FROM子句的子查询中，外层SELECT被标记为DERIVED
>
>```MYSQL
>explain 
>select * from t1
>UNION 
>select * from t2;
>```
>
>![image-20211223223917311](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211223223917311.png)
>
>UNION RESULT：从UNION表获取结果的SELECT
>
>```MYSQL
>explain 
>select * from t1
>UNION 
>select * from t2;
>```
>
>![image-20211223223919096](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211223223919096.png)
>
>

**table**

>显示这一行的数据是哪张表的。

**type**★

>访问类型排列。显示查询使用了何种类型，从最好到最差依次是
>
>NULL>system > const > eq_ref > ref > range > index > ALL > NULL
>
>一般来说，得保证查询至少达到range级别，最好能达到ref。
>
>
>
>system：表只有一行记录（等于系统表），这是const类型的特例，平时不会出现，可忽略。
>
>const：表示通过索引一次就找到了。用于比较primary key或unique索引。因为只匹配一行数据，所以很快。如将主键置于where列表中，MySQL就能将该查询转换为一个常量。
>
>```mysql
>explain
>select * from (select * from t1 where id = 1) d1;
># id=1只有一条记录，所以type是const，d1是单表且一行记录，所以type为system。
>```
>
>![image-20211223230142033](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211223230142033.png)
>
>eq_ref：唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配，常见于主键或唯一索引扫描。
>
>```mysql
>explain 
>select * from t1,t2 where t1.id = t2.id;
># 假设t1是部门表，t2是员工表；对t1进行全表扫描，所以type为all，当匹配到t2的一条记录，即CEO（总裁办只有一个员工），所以type为eq_ref
>```
>
>![image-20211223231332774](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211223231332774.png)
>
>ref：非唯一性索引扫描，返回匹配某个单独值得所有行。本质上也是一种索引访问，它返回所有匹配某个单独值得行，然而，它可能会找到多个符合条件的行，所以他应该属于查找和扫描的混合体。
>
>```mysql
># demo
>create table emp(
>	id bigint(18) not null PRIMARY key,
>	name varchar(20) default null,
>	phone varchar(20) default null
>);
>create index idx_name_phone on emp(name, phone);
>insert into emp(id,name,phone)values
>(1,'张山','120'),
>(2,'王伟','130'),
>(3,'张山','140'),
>(4,'李靖','150');
>explain 
>select * from emp where name = '张山';
># 用name和phone做联合索引，当name='张山'时，找到两条记录。
>
>```
>
>![image-20211224000144509](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211224000144509.png)
>
>range：只检索给定范围的行，使用一个索引来选择行。key列显示使用了哪个索引。一般就是在你的where语句中出现了between，<，>，in等查询。这种范围扫描索引比全表扫描要好，因为它只需要开始于索引的某一点，结束于另一点，不用扫描全部索引。
>
>```mysql
>explain 
>select * from t1 where id BETWEEN 1 AND 10;
>```
>
>![image-20211224000855787](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211224000855787.png)
>
>index：FULL Index Scan，index与ALL区别为index类型只遍历索引树。通常比ALL快，因为索引文件通常比数据文件小。全索引表扫描
>
>```mysql
>explain 
>select * from emp where phone BETWEEN '120' AND '130';
>
>```
>
>![image-20211224001151782](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211224001151782.png)
>
>ALL：Full Table Scan，全表扫描，将遍历全表以找到匹配的行。百万级别的表，需要优化。
>
>```mysql
>explain 
>select * from t1 where other_column BETWEEN 'a' AND 'z';
>```
>
>![image-20211224001526326](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211224001526326.png)
>
>

**possible_keys**

>理论上用到的索引，有一个或多个。查询涉及到的字段上若存在索引，则该索引将被列出，**但不一定被查询实际使用**。
>
>

**key**★

>实际使用的索引。如果为NULL，则没有使用索引。
>
>查询中若使用了覆盖索引，则该索引仅出现在key列表中。
>
>```mysql
>explain 
>select * from emp;
># 
>```
>
>![image-20211225105314528](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225105314528.png)
>
>

**key_ken**

>表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度。在不损失精度的情况下，长度越短越好。
>
>它显示的值为索引字段的最大可能长度，并非实际使用长度。即key_len是根据表定义计算而得，不是通过表内检索出的。
>
>```mysql
>explain 
>select id,name from emp where name = '张三';
># ken_len为63
>```
>
>![image-20211225110333042](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225110333042.png)
>
>```mysql
>explain 
>select id,name from emp where name = '张三' and phone = '120';
># ken_len为126
>```
>
>![image-20211225110401093](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225110401093.png)

**ref**

>显示索引的哪一列被使用了，如果可能的话，是一个常量。哪些列或常量被用于查找索引列上的值。
>
>```mysql
>explain 
>select * from t2,t1 where t1.id = t2.id and t2.other_column = '';
># t2的ref上是advance_mysql.t1.id，代表advance_mysql库的t1.id被使用。
>```
>
>![image-20211225111439434](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225111439434.png)

**rows**★

>根据表统计信息及索引选用情况，大致估算出找到所需的记录所需要读取的行数。
>
>表有无索引，对这个字段的值影响比较大。

**Extra**★

>包含不适合在其它列显示但十分重要的信息。
>
>Using filesort★
>
>说明MySQL会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取。MySQL中无法利用索引完成的排序操作称为“文件排序”。
>
>```mysql
>alter table emp drop index idx_name_phone
>create index idx_id_name_phone on emp(id,name,phone);
>
>explain 
>select id,name from emp where name = '张三' order by phone;
># 排序没有用到索引，而是using filesort；如果改为roder by id, name,phone就可以用到索引
>```
>
>![image-20211225113451839](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225113451839.png)
>
>
>
>Using temporary★
>
>使用了临时表保存中间结果，MySQL在对查询结果排序时使用临时表。常见于排序order by和分组查询group by。
>
>tips：要么按group by的顺序建索引，要么别建索引。
>
>```mysql
>explain 
>select id,name from emp where name = '张三' group by  phone;
>```
>
>![image-20211225114242257](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225114242257.png)
>
>
>
>Using index★
>
>表示相应的select操作中使用了覆盖索引，避免访问表的数据行，高效。
>
>如果同时出现using where，表明索引被用来执行索引键值的查找；
>
>```mysql
>explain 
>select id,name from emp where name = '张三';
>```
>
>![image-20211225115211971](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225115211971.png)
>
>如果没有出现using where，表明索引用来读取数据而非执行查找动作。
>
>```mysql
>explain 
>select id,name from emp;
>```
>
>![image-20211225115131867](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225115131867.png)
>
>覆盖索引：select的数据列只用从索引中就能取得，不必读取数据行，MySQL可以利用索引返回select列表中的字段，而不必根据索引再次读取数据文件，即查询列被所建的索引覆盖。
>
>
>
>Using where
>
>表示使用了where过滤。
>
>
>
>Using join buffer
>
>表示使用了连接缓存。
>
>
>
>impossible where
>
>where子句的值总是false，不能用来获取任何元组。
>
>```mysql
>explain 
>select id,name from emp where name = '张三' and name = '李四';
># 名字不可能有两个值。
>```
>
>
>
>select tables optimized away
>
>在没有group by子句的情况下，基于索引优化MIN/MAX操作。
>
>
>
>distinct
>
>优化distinct操作，在找到第一匹配的元组后即停止找同样值的动作。



* ###### 案例

```mysql
alter table t1 add column name varchar(20) default null;
alter table t2 add column name varchar(20) default null;
alter table t3 add column name varchar(20) default null;

explain
select d1.name, (select id from t3) d2
from (select id, name from t1 where other_column ='') d1
union
(select name, id from t2);
#分析：
#第一行（执行顺序4）：id列为1，表示是union里的第一个select，select_type列的primary表示该查询为外层查询，table列被标记为<derived3>,表示查询结果来自一个衍生表，其中derived3中3代表该查询衍生自第三个select查询，即id为3的select。【select d1.name......】

#第二行（执行顺序2）：id为3，是整个查询中第三个select的一部分。因查询中包含在from中，所以为derived。【select id,name from t1 where other_column=''】

#第三行（执行顺序3）：select列表中的子查询select_type为subquery，为整个查询中的第二个select。【select id from t3】

#第四行（执行顺序1）：select_type为union，说明第四个select是union里的第二个select，最先执行。【select name,id from t2】

#第五行（执行顺序5）：代表从union的临时表中读取的阶段，table列的<union1,4>表示用第一个和第四个select的结果进行union操作。【两个结果union操作】
```

![image-20211225144328635](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225144328635.png)



#### 索引优化

##### 索引分析

###### 单表

```mysql
# 建表SQL
create table if not exists article(
	id int(10) UNSIGNED not null primary key auto_increment,
	author_id int(10) UNSIGNED not null,
	category_id int(10) UNSIGNED not null,
	views int(10) UNSIGNED not null,
	comments int(10) UNSIGNED not null,
	title VARBINARY(255) not null,
	content text not null
);
INSERT INTO `advance_mysql`.`article`(`id`, `author_id`, `category_id`, `views`, `comments`, `title`, `content`) VALUES (1, 1, 1, 1, 1, '1', '1');
INSERT INTO `advance_mysql`.`article`(`id`, `author_id`, `category_id`, `views`, `comments`, `title`, `content`) VALUES (2, 2, 2, 2, 2, '2', '2');
INSERT INTO `advance_mysql`.`article`(`id`, `author_id`, `category_id`, `views`, `comments`, `title`, `content`) VALUES (3, 1, 1, 3, 3, '3', '3');
```

需求：查询category_id为1且comments大于1的情况下，views最多的article_id

```mysql
#分析我的SQL
explain
select id
from article
where category_id = 1 and comments > 1
order by views desc
limit 1;
# 问题：type为ALL为最坏情况，Extra中出现了Using filesort也为最坏情况
```

![image-20211225151209641](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225151209641.png)

优化1.1：查询中使用了category_id，comments和views，所以建联合索引

```mysql
create index idx_article_ccv on article(category_id, comments, views);
show index from article; # 查看索引
```

优化后的分析：type变了为ref，解决了全表扫描的问题；因为comments>1导致views索引用不上，索引Extra还是有using filesort。

![image-20211225152012143](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225152012143.png)

优化1.2：由于comments使用的是">"，所以重建索引。

```mysql
drop index idx_article_ccv on article;
create index idx_article_cv on article(category_id, views);
# 此时type为ref,Extra中仅有using where，优化完成。
```

![image-20211225153009443](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225153009443.png)



###### 两表

```mysql
# 建表SQL，class为类别，book为书籍
create table if not exists class(
	id int(10) UNSIGNED not null auto_increment,
	card int(10) UNSIGNED not null,
	primary key (id)
);

create table if not exists book(
	bookid int(10) UNSIGNED not null auto_increment,
	card int(10) UNSIGNED not null,
	primary key(bookid)
);

insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));
insert into class(card) values(floor(1+(rand() * 20)));

insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
insert into book(card) values(floor(1+(rand() * 20)));
```

需求：将class和book进行左连接

```mysql
explain
select * from class left join book on class.card = book.card;
#问题：type出现了ALL，rows都为20
```

![image-20211225155100043](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225155100043.png)

优化：

```mysql
#只在class表上建索引
create index idx_class_card on class(card);
#book的type为ALL，rows都为20
```

![image-20211225155506212](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225155506212.png)

```mysql
#只在book表上建索引
drop index idx_class_card on class;
create index idx_book_card on book(card);
#class的type为ALL，book的rows为1。所以左连接在右表加索引才有效，因为LEFT JOIN决定了左表是一定会令rows为20.
```

![image-20211225155654106](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225155654106.png)

```mysql
#同时在class和book上建索引
create index idx_class_card on class(card);
# type为index或ref,rows和为21，所以得到的结论：两表连接时，需要给副表建索引。
```

![image-20211225155829419](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225155829419.png)



###### 三表

```mysql
# 在两表的基础上加第三张表

create table if not exists phone(
	phoneid int(10) UNSIGNED not null auto_increment,
	card int(10) UNSIGNED not null,
	primary key(phoneid)
);

insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));
insert into phone(card) values(floor(1+(rand() * 20)));

drop index id_class_card on class;
drop index id_book_card on book;
```

需求：三表连接

```mysql
explain
select * from class 
left join book on class.card = book.card
left join phone on book.card = phone.card;
# type全部为ALL,
```

![image-20211225162338247](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225162338247.png)

优化：在book和phone上建立索引，并再次执行explain

```mysql
create index id_book_card on book(card);
create index id_phone_card on phone(card);
#分析：book和phone的type为ref,且rows总是为22
```

![image-20211225162800198](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225162800198.png)

JOIN优化的tips

1.尽可能减少JOIN语句中的NestedLoop的循环总次数；“**永远用小结果集驱动大结果集**”（会让rows总数变少）。

2.优先优化NestedLoop的内层循环。

3.保证JOIN语句中被驱动表上JOIN条件字段已经被索引。

4.当无法保证被驱动表的JOIN条件字段被索引且内存资源充足的前提下，不要太吝啬JoinBuffer的设置。

##### 索引失效

```mysql
#脚本
create table if not exists staffs(
	id int primary key auto_increment,
	name varchar(24) not null default "" comment '姓名',
	age int not null default 0 comment '年龄',
	pos varchar(20) not null default "" comment '职位',
	add_time TIMESTAMP not null default CURRENT_TIMESTAMP comment '入职时间'
)charset utf8 comment '员工记录表';

insert into staffs(name, age, pos, add_time)values('z3', 22, 'manager', now());
insert into staffs(name, age, pos, add_time)values('July', 23, 'dev', now());
insert into staffs(name, age, pos, add_time)values('2000', 23, 'dev', now());

create index idx_staffs_nameAgePos on staffs(name,age,pos);
```

###### 1.全值匹配

```mysql
explain select * from staffs where name = 'July';
#索引有效，用到了name的索引，ref为一个const
```

![image-20211225221539208](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225221539208.png)

```mysql
explain select * from staffs where name = 'July' and age = 25;
#索引有效，用到了name和age的索引。ref为两个const
```

![image-20211225221802611](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225221802611.png)

```mysql
explain select * from staffs where name = 'July' and age = 25 and pos = 'dev';
#索引有效，ref为3个const，用到了3个索引。
```

![image-20211225222001955](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225222001955.png)

```mysql
explain select * from staffs where pos = 'dev' and age = 23;
#索引失效，name索引丢失，违背了“最左前缀法则”的带头大哥不能丢。
```

![image-20211225222359823](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225222359823.png)

```mysql
explain select * from staffs where pos = 'dev' ;
#索引失效，name索引丢失，违背了“最左前缀法则”的带头大哥不能丢。
```

![image-20211225222553449](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225222553449.png)

```mysql
explain select * from staffs where name = 'July' ;
#索引有效
```

![image-20211225222702163](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225222702163.png)

```mysql
explain select * from staffs where name = 'July' and pos = 'dev' ;
#因为ken_len=74，所以只用到了name的索引，没有用到pos的索引。违背了“最左前缀法则”的中间兄弟不能断。
```

![image-20211225225045674](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225225045674.png)



###### 2.最佳左前缀法则

如果索引了多列，要遵循最左前缀法则。即查询从索引的最左前列开始并且**不跳过索引中的列**。



###### 3.不在索引上做任何操作（计算、函数、（自动OR手动）类型转换），会导致索引失效而转向全表扫描

```mysql
explain select * from staffs where left(name, 4) = 'July';
#索引失效，在索引列上使用了函数
```

![image-20211225230348557](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225230348557.png)



###### 4.存储引擎不能使用索引中范围条件右边的列（范围之后全失效）

```mysql
explain select * from staffs where name = 'July' and age > 23 and pos = 'dev';
#ken_len为78，用到了name和age索引，但是在用age索引的时候，type是range，所以就用不到pos索引。
#此处，pos为范围条件age右边的列。
```

![image-20211225230947986](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225230947986.png)



###### 5.尽量使用覆盖索引（只访问索引的查询（索引列和查询列一致）），减少select *

```mysql
explain select * from staffs where name = 'July' and age = 23 and pos = 'dev';

```

![image-20211225231426954](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225231426954.png)

```mysql
explain select name, age, pos from staffs where name = 'July' and age = 23 and pos = 'dev';
# 没有用select *，extra中额外用到了using index。
```

![image-20211225231509945](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225231509945.png)

```mysql
explain select name, age, pos from staffs where name = 'July' and age > 23 and pos = 'dev';
# type为ref，ken_len为74，extra有using index。没用使用select *，即使有范围查找，type也还是ref。
```

![image-20211225231823454](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225231823454.png)



###### 6.MySQL在使用不等于（!=或者<>）的时候无法使用索引会导致全表扫描

```mysql
explain select * from staffs where name != 'July'
#type为ALL，索引失效。
```

![image-20211225232032124](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225232032124.png)



###### 7.is null，is not null也无法使用索引

```mysql
explain select * from staffs where name is null;
#key为NULL，索引失效。
```

![image-20211225232224136](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225232224136.png)

```mysql
explain select * from staffs where name is not null;
#key为NULL，索引失效。
```

![image-20211225232407196](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225232407196.png)



###### 8.like以通配符开头（'%abc...%'），MySQL索引失效会变成全表扫描的操作

```mysql
explain select * from staffs where name like '%July%';
explain select * from staffs where name like '%July';
#两条SQL分析都是type为ALL,key为null，索引失效。
```

![image-20211225232654186](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225232654186.png)

```mysql
explain select * from staffs where name like 'July%';
#like是范围查找，type为range。百分Like加右边
```

![image-20211225232900377](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225232900377.png)

如何解决like '%字符串%'时索引失效？

```mysql
create table if not exists tbl_user(
	id int(11) not null auto_increment,
	name varchar(20) default null,
	age int(11) default null,
	email varchar(20) default null,
	primary key (id)
)engine=INNODB auto_increment=1 default charset=utf8;
insert into tbl_user(name, age, email) values('1aa1', 21, 'b@163.com');
insert into tbl_user(name, age, email) values('2aa2', 23, 'a@163.com');
insert into tbl_user(name, age, email) values('3aa3', 31, 'c@163.com');
insert into tbl_user(name, age, email) values('4aa4', 24, 'd@163.com');
```

```mysql
#查询名字中包含aa的所有user
explain select name,age from tbl_user where name like '%aa%';

explain select id from tbl_user where name like '%aa%';
explain select name from tbl_user where name like '%aa%';
explain select age from tbl_user where name like '%aa%';

explain select id,name from tbl_user where name like '%aa%';
explain select id,name, age from tbl_user where name like '%aa%';
explain select name,age from tbl_user where name like '%aa%';

explain select * from tbl_user where name like '%aa%';
explain select  id,name, age, email from tbl_user where name like '%aa%';
#没建索引之前，只要用到了'%aa%'，不管selct啥，都是全表扫描
```

![image-20211225234044468](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225234044468.png)

```mysql
create index idx_user_nameAge on tbl_user(name,age);
```

```mysql
explain select name,age from tbl_user where name like '%aa%';

explain select id from tbl_user where name like '%aa%';
explain select name from tbl_user where name like '%aa%';
explain select age from tbl_user where name like '%aa%';

explain select id,name from tbl_user where name like '%aa%';
explain select id,name, age from tbl_user where name like '%aa%';
explain select name,age from tbl_user where name like '%aa%';
#以上SQL，分析结果一致，都用到了索引。因为查询列表都在覆盖索引的范围内
```

![image-20211225234723296](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225234723296.png)

```mysql
explain select * from tbl_user where name like '%aa%';
explain select  id,name, age, email from tbl_user where name like '%aa%';
#以上SQL，分析结果一致，都索引失效
```

![image-20211225234802974](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225234802974.png)

方法：用覆盖索引防止全表扫描。



###### 9.字符串不加单引号会索引失效

```mysql
explain select * from staffs where name = '2000';
#索引有效
```

![image-20211225235556565](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225235556565.png)

```mysql
explain select * from staffs where name = 2000;
#key为Null，索引失效。自动类型转换int -> varchar
```

![image-20211225235636955](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211225235636955.png)



###### 10.少用or，用它来连接时会索引失效

```mysql
explain select * from staffs where name = 'July' or name ='z3';
#索引失效
```

![image-20211226000032166](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226000032166.png)



###### 小总结：

带头大哥不能死，

中间兄弟不能断，

索引列上无计算，

like %  加右边，

范围之后全失效。

##### 练习

```mysql
create table test03(
	c1 char(10),
	c2 char(10),
	c3 char(10),
	c4 char(10),
	c5 char(10)
);

insert into test03(c1, c2, c3, c4, c5) values ('a1', 'a2', 'a3', 'a4', 'a5');
insert into test03(c1, c2, c3, c4, c5) values ('b1', 'b2', 'b3', 'b4', 'b5');
insert into test03(c1, c2, c3, c4, c5) values ('c1', 'c2', 'c3', 'c4', 'c5');
insert into test03(c1, c2, c3, c4, c5) values ('d1', 'd2', 'd3', 'd4', 'd5');
insert into test03(c1, c2, c3, c4, c5) values ('e1', 'e2', 'e3', 'e4', 'e5');

create index idx_test03_c1234 on test03(c1,c2,c3,c4);
```

```mysql
explain select * from test03 where c1='a1' and c2= 'a2' and c4='a4' and c3='a3';
#为什么用到了4个索引？？？ 全值匹配，MySQL会启用优化器自动会按索引顺序优化where条件的顺序。
```

![image-20211226113020300](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226113020300.png)

```mysql
explain select * from test03 where c1='a1' and c2= 'a2' and c3>'a3'  and c4='a4';
#key_len=93，所以用到了3个索引。因为有c3>'a3'，所以type为range，且范围之后全失效。
```

![image-20211226113733104](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226113733104.png)

```mysql
explain select * from test03 where c1='a1' and c2= 'a2' and c4>'a4' and c3='a3';
#MySQL调优按序，所以用到4个索引。而第四个条件是c4>'a4'，所以type为range
```

![image-20211226114311130](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226114311130.png)

```mysql
explain select * from test03 where c1='a1' and c2= 'a2' and c4='a4' order by c3;
#中间断了，查找只用到了c1和c2，而c3会被用于排序。
```

![image-20211226114655230](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226114655230.png)

```mysql
explain select * from test03 where c1='a1' and c2= 'a2' order by c3;
#c3依然只用于排序
```

![image-20211226114940136](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226114940136.png)

```mysql
explain select * from test03 where c1='a1' and c2= 'a2' order by c4;
#c1 -> c2 -> c4，发生断裂，所以mysql内部使用了using filesort做排序。
```

![image-20211226115152498](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226115152498.png)

```mysql
explain select * from test03 where c1='a1' and c5= 'a5' order by c2, c3;
#索引只有c1。c2，c3接在c1后用于排序，所以没有断裂，没有出现using filesort。c5压根就没有建立索引。
```

![image-20211226115605966](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226115605966.png)

 ```mysql
 explain select * from test03 where c1='a1' and c5= 'a5' order by  c3,c2;
 #索引只有c1。排序的时候是c3,c2，没有按序，所以出现了using filesort。c5压根就没有建立索引。
 ```

![image-20211226115935592](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226115935592.png)

```mysql
explain select * from test03 where c1='a1' and c2= 'a2' order by  c2, c3;
#索引用到了c1,c2。排序的时候c2,c3没有断裂，所以没有出现using filesort
```

![image-20211226120410624](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226120410624.png)

```mysql
explain select * from test03 where c1='a1' and c2= 'a2' and c5='a5' order by  c2, c3;
#索引用到了c1,c2。排序用到了c2,c3。没有出现using filesort。c5压根就没有建立索引。
```

![image-20211226120826693](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226120826693.png)

```mysql
explain select * from test03 where c1='a1' and c2= 'a2' and c5='a5' order by  c3, c2;
#特例：没有出现using filesort。因为排序字段c2已经是一个常量了，所以实际的排序就只有c3。如果去掉c2='a2'就会产生using filesort
```

![image-20211226121103177](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226121103177.png)

```mysql
explain select * from test03 where c1='a1' and c4='a4' group by  c2, c3;
#索引只有c1，因为c4前面断了。分组时的c2,c3有序，所以没有出现using filesort。
```

![image-20211226121723160](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226121723160.png)

```mysql
explain select * from test03 where c1='a1' and c4='a4' group by  c3, c2;
#法则：分组之前必排序，所以在使用索引方面gruop by近似order by。且如果group by无序，会产生临时表。
#因为c3,c2逆序，所以产Using temporary; Using filesort
```

![image-20211226121927132](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226121927132.png)



###### 一般性建议

1.对于单键索引，尽量选择针对当前query过滤性更好的索引

2.在选择组合索引时，当前query中过滤性最好的字段在索引字段顺序中，位置越靠前越好

3.在选择组合索引时，尽量选择可以包含当前query中的where子句中更多字段的索引

4.尽可能通过分析统计信息和调整query的写法来达到选择合适索引的目的

### 三、查询截取分析

#### 查询优化

##### 永远小表驱动大表★

###### Case

```mysql
#当A表的数据集 > B表的数据集时，in优于exists
select * from A where id in (select id from B)
#等价于
for select id from B
	for select * from A where A.id = B.id
```

```mysql
#当A表的数据集 < B表的数据集时，exists优于in
select * from A where exists (select 1 from B where B.id = A.id)
#等价于
for select * from A
	for select * from B where B.id = A.id
#以上都是A表与B表的id字段已建索引
```

###### Exists

select ... from table where exists(subquery);

该语法可以理解为：**将主查询的数据放到子查询中做条件验证，根据验证结果（true或false）来决定主查询的数据结果是否得以保留。**

###### 提示

1.exists子查询只返回布尔类型，因此子查询中的select *也可以时select 1或其他。实际执行时会忽略select清单，因此没差。

2.existss子查询的实际执行过程可能经过了优化而不是我们理解上的逐条对比，如果担心效率，可以实际验证。

##### order by关键字优化

###### order by子句，尽量使用index方法排序，可以避免使用filesort排序

```mysql
create table if not exists tblA(
	id int primary key not null auto_increment,
	age int,
	birth TIMESTAMP not null
);

insert into tblA(age, birth) values(22, now());
insert into tblA(age, birth) values(23, now());
insert into tblA(age, birth) values(24, now());

create index idx_A_ageBirth on tblA(age, birth);
```

现在只关注使用order by后，会不会产生using filesort，不关注有没有使用索引。

```mysql

explain select * from tblA where age > 20 order by age;
explain select * from tblA where age > 20 order by age, birth;
#order by中有age，所以使用index排序，不会出现using filesort
```

![image-20211226141604890](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226141604890.png)

```mysql
explain select * from tblA where age > 20 order by birth;
explain select * from tblA where age > 20 order by birth, age;
#没有按照索引顺序，因此产生filesort
```

![image-20211226141813956](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226141813956.png)

```mysql
explain select * from tblA  order by birth;
explain select * from tblA where birth > '2021-12-26 14:13:31' order by birth;
#order by没有接age，都会产生using filesort
```

![image-20211226150704068](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226150704068.png)

```mysql
explain select * from tblA  order by age asc, birth desc;
#asc和desc不一致，让索引失效产生using filesort
```

![image-20211226150834868](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226150834868.png)

order by满足两种情况，会使用index方式排序

1.order by语句使用索引最左前列

2.使用where子句与order by子句条件列组合满足索引最左前列

结论：尽量在索引列上完成排序操作，遵照索引建立的最佳左前缀

###### 如果不在索引列上，filesort有两种算法：双路排序和单路排序

双路排序

>扫描两次磁盘才能得到数据,耗费性能,MySQL4.1之后被废弃。
>
>从磁盘取排序字段，在buffer进行排序，再从磁盘取其他字段.

单路排序

>从磁盘读取查询需要的所有列,按照order by列在buffer对它们进行排序,然后扫描排序后的列表进行输出,避免第二次读取数据,提升了效率.
>
>并且把随机IO变成了顺序IO,但是它会使用更多的空间,因为它把每一行都保存在内存中了.
>
>单路排序如果一次无法取出数据,就会产生多路排序,此时比双路排序性能更差.

###### 优化策略

1.增大sort_buffer_size参数的只

2.增大max_length_for_sort_data参数的值

###### 小总结

>**为排序使用索引**
>
>1.MySQL两种排序方式:index和filesort
>
>2.MySQL能为排序与查询使用相同的索引

>Case
>
>key a_b_c(a,b,c)
>
>1.order by能使用索引最左前缀
>
>order by a
>
>order by a,b
>
>order by a,b,c
>
>order by a desc, b desc, c desc
>
>2.如果where使用索引的最左前缀定义为常量,则order by能使用索引
>
>where a = const order by b,c 
>
>where a = const and b = const order by c
>
>where a = const order by b,c
>
>where a = const and b > const order by b,c
>
>3.不能使用索引进行排序,即产生using filesort
>
>order by a asc, b desc, c desc #排序规则不一致
>
>where g = const order by b,c #丢失索引a
>
>where a = const order by c #丢失索引b
>
>where a = const order by a,d #d不是索引的一部分
>
>where a in(...) order by b,c #对于排序来说,多个相等条件也是范围查询

##### group by关键字优化

group by实质是先排序后进行分组,遵照索引建的最佳左前缀

当无法使用索引列,增大max_length_for_sort_data参数的设置+增大sort_buffer_size参数的设置

where高于having, 能写在where限定的条件就不要去having限定(和order by的区别,其他都一样.)

#### 慢查询日志

##### 是什么

它是MySQL提供的一种日志记录,用来记录在MySQL中响应时间**大于**阈值的语句,具体指运行时间超过long_query_time值的SQL,则会被记录在慢查询日志中,默认为10秒.

##### 怎么玩

###### 说明

默认情况下,MySQL没有开启慢查询日志.如果不是调优需要的话,一般不建议启动该参数,因为它需要将慢查询日志写入文件,耗费一定性能.

###### 查看是否开启以及如何开启

```mysql
#查看是否开启
show variables like '%slow_query_log%';
```

![image-20211226163812708](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226163812708.png)

```mysql
#暂时开启
set global slow_query_log = 1; #使用该命令只对当前数据库有效,如果MySQL重启,则失效.
#永久开启
#修改my.cnf文件,在[mysqld]下新增或修改参数,然后重启MySQL服务器
slow_query_log=1
slow_query_log_file=D:\ProgramData\MySQL\MySQL Server 5.5\Data\DESKTOP-T2FQ92N-slow.log
```

![image-20211226164120695](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226164120695.png)

```mysql
show variables like 'long_query_time%';
#查看默认阈值
```

![image-20211226164630303](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226164630303.png)

```mysql
#设置阈值时间为3秒
set global long_query_time = 3;
show variables like 'long_query_time%';
#发现还是10,这里需要新开一个会话才能看到变化
```

![image-20211226164937012](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226164937012.png)

```mysql
select sleep(4);
#模拟慢查询,让日志文件出现
```

![image-20211226165429426](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226165429426.png)

```mysql
#查询系统中有多少条慢日志
show global status like '%Slow_queries%';
```

```mysql
#永久生效法,[mysqld]下配置
slow_query_log=1;
slow_query_log_file=D:\ProgramData\MySQL\MySQL Server 5.5\Data\DESKTOP-T2FQ92N-slow.log
long_query_time=3;
log_output=FILE
```

##### 日志分析工具mysqldumpslow

###### 帮助信息

```mysql
mysqldumpslow --help;
```

>mysqldumpslow命令参数解析
>
>s：表示按照何种方式排序
>
>c：访问次数
>
>(a)l：(平均)锁定时间
>
>(a)r：(平均)返回记录
>
>(a)t：(平均)查询时间
>
>g：后边搭配一个正则匹配模式，大小写不敏感

>工作中常用demo
>
>1.得到返回记录集做多的10个SQL
>
>mysqldumpslow -s r -t 10 D:\ProgramData\MySQL\MySQL Server 5.5\Data\DESKTOP-T2FQ92N-slow.log
>
>2.得到访问次数最多的10个SQL
>
>mysqldumpslow -s c -t 10 D:\ProgramData\MySQL\MySQL Server 5.5\Data\DESKTOP-T2FQ92N-slow.log
>
>3.得到按照时间排序的前10条里面含有左连接的查询语句
>
>mysqldumpslow -s t 10 -g "left join" D:\ProgramData\MySQL\MySQL Server 5.5\Data\DESKTOP-T2FQ92N-slow.log
>
>4.建议使用这些命令时结合|和more使用，防止爆屏
>
>mysqldumpslow -s r -t 10 D:\ProgramData\MySQL\MySQL Server 5.5\Data\DESKTOP-T2FQ92N-slow.log | more



#### 批量数据脚本

目标：往表里插入1000W数据

##### 建表

```mysql
#建dept
create table if not exists dept(
	id int UNSIGNED PRIMARY key auto_increment,
	deptno MEDIUMINT UNSIGNED not null default 0,
	dname varchar(20) not null default "",
	loc varchar(13) default ""
)engine=INNODB default charset=utf8;
#建emp
create table if not exists emp(
id int UNSIGNED PRIMARY key auto_increment,
empno MEDIUMINT UNSIGNED  not null default 0 comment '编号',
ename varchar(20)  not null default "" comment  '名字',
job varchar(9) not null default "" comment '工作',
mgr MEDIUMINT UNSIGNED  not null default 0 comment '上级编号',
hiredate DATE not null comment '入职时间',
sal decimal(7,2) not null comment '薪水',
comm DECIMAL(7,2) not null comment '红利',
deptno MEDIUMINT UNSIGNED not null default 0 comment '部门编号'
)engine=INNODB default charset=utf8;
```

##### 设置参数log_bin_trust_function_creators

创建函数的时候可能报错，所以进行如下设置

```mysql
#临时版，重启消失
set global log_bin_trust_function_creators = 1;
show variables like 'log_bin_trust_function_creators';
#重启永久版
#在my.cnf下的[mysqld]加上log_bin_trust_function_creators = 1;
```

![image-20211226204351577](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226204351577.png)

##### 创建函数，保证每条数据都不同

```mysql
#随机产生字符串
delimiter $$
create FUNCTION rand_string(n int) returns varchar(255)
begin
	DECLARE chars_str varchar(100) default 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
	DECLARE return_str varchar(255) default '';
	DECLARE i int default 0;
	while i < n do
	set return_str = CONCAT(return_str,substring(chars_str,FLOOR(1+rand()*52),1));
	set i = i + 1;
	end while;
	return return_str;
end $$

#随机产生部门编号
delimiter $$
create function rand_num() returns int(5)
begin
	declare i int default 0;
	set i = floor(100+rand()*10);
return i;
end $$
```

##### 创建存储过程

```mysql
#创建往emp表中插入数据的存储过程
delimiter $$
create procedure insert_emp(in start int(10), in max_num int(10))
begin 
	declare i int default 0;
	set autocommit = 0;
	repeat 
	set i = i + 1;
	insert into emp(empno, ename, job, mgr, hiredate, sal, comm, deptno)
	values((start+i), rand_string(6),'SALESMAN', 0001, curdate(), 2000, 400, rand_num());
	until i = max_num  end repeat;
	commit;
end $$

#创建往dept表中插入数的存储过程
delimiter $$
create procedure insert_dept(in start int(10), in max_num int(10))
begin 
	DECLARE i int default 0;
	set autocommit = 0;
	repeat
	set i = i + 1;
	insert into dept(deptno, dname, loc) values ((start+i), rand_string(10), rand_string(8));
	until i = max_num end repeat;
	commit;
end $$
```

##### 调用存储过程

```mysql
delimiter ; #把结束符改回来
#调用insert_dept
call insert_dept(100, 10);
#调用insert_emp
call insert_emp(100001, 500000);
```



#### Show Profile

##### 是什么

是MySQL提供可以用来分析当前会话中语句执行的资源消耗情况。可以用于SQL的调优测量。

默认情况下，参数处于关闭状态，并保存最近15次运行结果。

##### 分析步骤

###### 是否支持，查看当前MySQL是否支持该功能

```mysql
show variables like 'profiling';
```

###### 开启功能

```mysql
set profiling=on;
```

###### 运行SQL

```mysql
#执行两条慢查询
select * from emp group by id % 10 limit 150000;
select * from emp group by id % 20 order by 5;
```

###### 查看结果，show profiles

```mysql
show profiles;
```

![image-20211226214734477](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226214734477.png)



###### 诊断SQL，show profile cpu, block io for query 上一步SQL的数字号码

```mysql
show profile cpu, block io for query 55;
#重要的status
#converting Heap to MyISAM,查询结果太大说明内存不够用，开始往磁盘迁移
#creating tmp table 创建临时表（拷贝数据到临时表，用完再删除）
#copying to tmp table on disk 把内存中临时表复制到磁盘。危险
#locked 上锁了
```

![image-20211226214823092](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226214823092.png)



#### 全局查询日志

###### **切记：永远不要在生产环境开启该功能！**

```mysql
set global general_log = 1;
set global log_output = 'TABLE';
#此后，编写的所有SQL，将会记录到MySQL库里面general_log表，可以通过以下命令查看
select * from mysql.general_log;
```

![image-20211226220609630](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211226220609630.png)



查询优化步骤的总结

1.慢查询的开启并捕获

2.explain+慢SQL分析

3.show profile查询SQL在MySQL服务器里面的执行细节和声明周期情况

4.SQL数据库服务器的参数调优。





