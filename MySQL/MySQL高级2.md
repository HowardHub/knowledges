### 四、锁机制

#### 概述

锁是计算机协调多个进程或线程并发访问某一资源的机制。

在数据库中，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。

#### 锁的分类

##### 从对数据操作的类型分类

###### 读锁（共享锁）

针对同一份数据，多个读操作可以同时进行而不会相互影响。

###### 写锁（排它锁）

当前写操作没有完成前，它会阻断其他写锁和读锁。

##### 从对数据操作的粒度分类

行锁、表锁和页锁

#### 三锁

##### 表锁（偏读）

###### 特点

偏向MyISAM存储引擎，开销小，加锁快；无死锁；锁定粒度大，发生锁冲突的概率最高，并发度最低。

###### 案例分析

```mysql
#建表
create table if not exists mylock(
	id int not NULL PRIMARY key auto_increment,
	name varchar(20)
) ENGINE=MyISAM;

insert into mylock(name) values('a');
insert into mylock(name) values('b');
insert into mylock(name) values('c');
insert into mylock(name) values('d');
insert into mylock(name) values('e');
```

```mysql
#手动增加表锁
#lock table 表名 read(write), 表名2 read(write), 其它;
lock table mylock read, staffs write;
show open tables;
# 发现两张表的In_use已经为1
```

![image-20211227105657139](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227105657139.png)

```mysql
#手动解锁
unlock tables;
show open tables;
#发现所有的In_user都变为0
```

![image-20211227110017895](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227110017895.png)

###### 案例1：session1给mylock表加读锁

```mysql
#session1给mylock表加读锁
lock table mylock read;
```

```mysql
#session1可以读mylock
select * from mylock;
```

![image-20211227111116245](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227111116245.png)

```mysql
#session1不可以更新mylock
update mylock set name = 'ccc' where id = 1;
```

![image-20211227111227121](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227111227121.png)

```mysql
#session1不可以读其他表
select * from staffs;
```

![image-20211227111302774](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227111302774.png)

```mysql
#session2可以读mylock和其他表
select * from mylock;
select * from staffs;
```

```mysql
#session2不可以更新mylock，发生阻塞。
update mylock set name = 'aa' where id = 1;
```

![image-20211227112322293](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227112322293.png)

```mysql
#session1解锁mylock
unlock tables;
#session2立马更新成功
```

![image-20211227112426532](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227112426532.png)

###### 案例2：session1给mylock表加写锁

```mysql
#sesson1给mylock加写锁
lock table mylock write;
```

```mysql
#session1可以读mylock
select * from mylock;
```

![image-20211227115651062](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227115651062.png)

```mysql
#session1可以更新mylock
update mylock set name='aaa' where id = 1;
```

![image-20211227115857912](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227115857912.png)

```mysql
#session1不可以读（写）其他表
select * from staffs;
```

![image-20211227115944451](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227115944451.png)

```mysql
#session2可以读（写）其他表
select * from staffs;
```

![image-20211227120110103](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227120110103.png)

```mysql
#sessioin2不可以读（写）mylock，会发生阻塞，需要等待锁被释放。
select * from mylock where id = 1;
#注意，如果读成功，请换成不同的id来测试，因为MySQL有缓存，第二次条件会从缓存中取得，影响锁演示效果。
```

![image-20211227120550699](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227120550699.png)

###### 案例结论

MyISAM在执行select前，会自动给涉及的表加读锁；在执行增删改操作前，会自动给涉及的表加写锁。

读锁会阻塞写，但不会阻塞读；而写锁则会把读和写都阻塞。

表共享读锁，表独占写锁。

###### 表锁分析

可以通过检查table_locks_waited和table_locks_immeidate状态变量来分析系统上的表锁定；

```mysql
show status like 'table%';
#Table_locks_immediate：产生表级锁定的次数，表示可以立即获取锁的查询次数，每立即获取锁值加1。
#Table_locks_waited：出现表级锁定争用而发生等待的次数（不能立即获取锁的次数，每等待一次锁值加1），此值高说明存在着较严重的表级锁争用情况。
```

![image-20211227121524770](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227121524770.png)



##### 行锁（偏写）

###### 特点

偏向InnoDB，开销大，加锁慢；会出现死锁；锁粒度最小，发生锁冲突的概率最低，并发度最高。

InnoDB相比MyISAM，除了支持事务，还有采用了行级锁。

###### 案例分析

```mysql
#建表SQL
create table if not exists test_innodb_lock(
	a int(11),
	b varchar(16)
)engine = INNODB;
#加数据
insert into test_innodb_lock values(1,'b2');
insert into test_innodb_lock values(3,'3');
insert into test_innodb_lock values(4,'4000');
insert into test_innodb_lock values(5,'5000');
insert into test_innodb_lock values(6,'6000');
insert into test_innodb_lock values(7,'7000');
insert into test_innodb_lock values(8,'8000');
insert into test_innodb_lock values(9,'9000');
insert into test_innodb_lock values(1,'b1');
#建两个单值索引
create index test_innodb_a_ind on test_innodb_lock(a);
create index test_innodb_a_ind on test_innodb_lock(b);
```

```mysql
#演示1：行锁定
#在session1和session2中都关闭自动提交
set autocommit = 0;

#在session1上执行更新，但并不提交
update test_innodb_lock set b='4001' where a = 4;
#如果再开启一个session3，没有设置autocommit=1，那么等session1提交后，seesion3能看到最新结果，但session2因为没有commit，所以它依然看不到最新结果。
```

![image-20211227140036294](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227140036294.png)

```mysql
#在session2上执行更新。发生阻塞，因为该行被session1加了行锁。
update test_innodb_lock set b='4002' where a = 4;
```

![image-20211227140122406](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227140122406.png)

```mysql
#在session上和session2上都进行提交
commit;
#在session2上进行查询
select * from test_innodb_lock where  a=4;

```

![image-20211227140330633](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227140330633.png)



```mysql
#“索引失效，导致行锁升级为表锁”的演示
#在session1中执行更新第四行记录
update test_innodb_lock set a=41 where b = 4000; 
#没加引号，发生自动类型转换，导致索引失效，行锁变为表锁
```

![image-20211227141800534](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227141800534.png)

```mysql
#在seesion2中更新第9行记录，发生阻塞，因为已经升级为表锁
update test_innodb_lock set b='9002' where a = 9;
```

![image-20211227141925416](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227141925416.png)

```mysql
#在session1和session2中执行
commit;
```

>间隙锁（Next-key锁）
>
>当用范围条件而不是相等条件检索数据，并请求共享或排他锁时，InnoDB会给符合条件的已有数据记录的**索引项**加锁；
>
>对于键值在条件范围内但并不存在的记录，叫做间隙（GAP）
>
>危害：发生间隙锁时，即使某些不存在的键值也会被无辜的锁定，而造成在锁定的时候无法插入锁定键值范围内的任何数据。

```mysql
#演示2：”间隙锁“，因为例子中没有a=2的记录
#在session1中执行下面的更新
update test_innodb_lock set b = '7758' where a > 1 and a < 6;
```

![image-20211227142508698](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227142508698.png)

```mysql
#在session2中执行
insert into test_innodb_lock values(2,'2000');
#新增一条不存在的记录，理论上不会发生阻塞，但实际上发生了，这就是间隙锁
```

![image-20211227142759809](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227142759809.png)

```mysql
#在session1和session2中都执行提交
commit;
#然后session才能添加成功
```

```mysql
#演示3：如何锁定一行
```

```mysql
#在session1中执行以下命令
begin;
	select * from test_innodb_lock where a = 8 for update;
#并没有执行commit
```

```mysql
#在seesion2中执行
update test_innodb_lock set b = '8001' where a=8;
#发现a=8的行已经被锁定，进入阻塞
```

![image-20211227144649377](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227144649377.png)

```mysql
#在session1中执行提交
commit;
#发现session2的更新操作执行完成
```

![image-20211227144733855](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227144733855.png)

###### 行锁分析

可以通过检查InnoDB_row_lock状态变量来分析系统上的行锁的争夺情况；

```mysql
show status like 'innodb_row_lock%';
#Innodb_row_lock_current_waits：当前正在等待锁定的数量
#Innodb_row_lock_time★：从系统启动到现在锁定总时长
#Innodb_row_lock_time_avg★：每次等待所花的平均时间
#Innodb_row_lock_time_max：从系统启动到现在等待最长的一次所花的时间
#Innodb_row_lock_waits★：从系统启动到现在总共等待的次数
```

![image-20211227145449733](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227145449733.png)

##### 页锁

开销和加锁时间介于表锁和行锁之间；会出现死锁；锁定粒度介于表锁和行锁之间，并发度一般。

#### 结论

开销、加锁速度、死锁、粒度、并发性能，只能就具体应用的特点来说哪种锁更合适。



### 五、主从复制

#### 复制的原理

slave会从master读取binlog来进行数同步。

>复制过程的三步
>
>1.master将改变记录到二进制日志（binary log）。这些记录过程叫做二进制日志事件（binary log events）。
>
>2.slave将master的binary log events拷贝到它的中继日志（relay log）。
>
>3.slave重做中继日志中的事件，将改变应用到自己的数据库中。MySQL复制时异步的且串行化的。

![image-20211227161449449](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227161449449.png)



#### 复制的原则

每个slave只有一个master

每个slave只能有一个唯一的服务器ID

每个master可以有多个slave

#### 复制的最大问题

延时

#### 一主一从常见配置

前提：MySQL版本一致。win主，linux从，且能ping通。

主从都配置在[mysqld]结点下，都是小写。

##### 1.修改配置文件

###### 1.1主机修改my.ini配置文件

```bash
server-id = 1 #【必须】主服务器id
log-bin="自己本地的路径/mysqlbin" #【必须】启用二进制日志，如log-bin="D:/Program Files (x86)/MySQL/MySQL Server 5.5/mysqlbin"
log-err="自己本地的路径/mysqlerr"#【可选】启用错误日志，如log-err="D:/Program Files (x86)/MySQL/MySQL Server 5.5/mysqlerr"
basedir="自己的目录" #【可选】根目录，如basedir="D:/Program Files (x86)/MySQL/MySQL Server 5.5"
tmpdir="自己本地路径"#【可选】临时目录，如tmpdir="D:/Program Files (x86)/MySQL/MySQL Server 5.5"
datadir="自己本地路径/Data/" #【可选】数据目录，如datadir="D:/Program Files (x86)/MySQL/MySQL Server 5.5/Data/"

```

###### 1.2从机修改my.cnf配置文件

```bash
vim /etc/my.cnf
server_id = 2 # 修改服务器id，注意这里连接符是下划线~~

```

##### 2.重启主机和从机的MySQL服务

##### 3.主机从机都关闭防火墙

>windows手动关闭
>
>linux执行 service iptables stop

##### 4.配置账户并授权

###### 在windows上授权命令（先重启服务，让前面的配置生效）

```mysql
#新建一个slave/6623的用户给slave用
CREATE USER 'slave'@'%' IDENTIFIED BY '6623';
GRANT REPLICATION SLAVE ON *.* TO 'slave'@'%';
flush privileges; #刷新，让授权生效
show master status; #查看主机状态
#从机应该从 mysqlbin.000001 文件的486号开始复制
```

![image-20211227170042348](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227170042348.png)

###### 在linux上执行命令授权（先重启服务，让前面的配置生效）

```mysql
CHANGE MASTER TO MASTER_HOST='183.218.164.7',
MASTER_USER='slave',
MASTER_PASSWORD='6623',
MASTER_LOG_FILE='mysqlbin.000001',
MASTER_LOG_POS=486;
#每次重新配置MASTER_LOG_FILE时，都必须取得最新的，否则会出错。
#启动slave的复制功能
start slave;
#查看slave状态
show slave status\G;
#出现以下信息，则配置成功
```

![image-20211227212220490](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211227212220490.png)

###### 5.主机新建库、新建表、insert记录，并在从机查看

```mysql
#主机操作
create database mysqlcopy;
use mysqlcopy;
create table dog(
	id int,
    name varchar(20)
);
insert into dog values(1,'ww1');
insert into dog values(1,'ww1');
```

```mysql
#从机操作
use mysqlcopy;
select * from dog;
```

###### 6.如何停止从机复制功能

```mysql
stop salve;
```















