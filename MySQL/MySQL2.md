### 四、TCL（事务控制语言）

#### 概念：一个或一组SQL语句组成一个执行单元，这个执行单元要么全部执行，要么全不执行。

#### ACID属性：

 * 原子性(atomicity)：事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。

 * 一致性(consistency)：事务必须使数据库从一个一致状态变换到另一个一致状态。

 * 隔离性(isolation)：事务的执行不能被其他事务干扰，即事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能相互干扰。

   >对于同时运行的多个事务，当这些事务访问数据库中相同的数据时，如果没有采取必要的隔离机制，就会导致各种并发问题。
   >
   >* 脏读：对于两个事务T1、T2，T1读取了已经被T2更新但还**没有被提交**的字段。之后若T2回滚，T1读取的内容就是临时且无效的。
   >* 不可重复读：对于两个事务T1、T2，T1读取了一个字段，然后T2**更新**了该字段。之后若T1再次读取了该字段，值就不同了。
   >* 幻读：对于两个事务T1、T2，T1读取了某行的一个字段，然后T2在该表中**插入**了一些新的行。之后若T1再次读取这个表，就会多出几行。

 * 持久性(durability)：事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来的其他操作和数据库故障不应该对其有任何。

#### 事务的创建：

​	隐式事务：事务没有明显的开启和结束的标记。比如insert、update、delete语句。show 	 variables like '%autocommit%';

​	显示事务：事务具有明显的开启和结束标记。前提：必须禁用自动提交功能。set autocommit=0;

​	步骤1：开启事务：set autocommit = 0;

​	步骤2：编写事务中的sql语句

​	步骤3：结束事务：commit;提交事务/rollback; 回滚事务

> 例子
>
> ```mysql
> CREATE TABLE ACCOUNT(
> 	id INT(10) PRIMARY KEY,
> 	NAME VARCHAR(20),
> 	balance INT(10)
> 
> );
> INSERT INTO ACCOUNT (id, NAME, balance) VALUES(1,'张无忌',1000),(2,'张敏',1000);
> ```
>
> //////////////// 模拟提交
>
> 1.开启事务
>
> ```mysql
> SET autocommit = 0;
> START TRANSACTION; # 可选
> ```
>
> 2.编写事务
>
> ```mysql
> UPDATE ACCOUNT SET balance = 500 WHERE id = 1;
> UPDATE ACCOUNT SET balance = 1500 WHERE id = 2;
> ```
>
> 3提交事务
>
> ```mysql
> COMMIT;
> ```
>
> 选中所有语句一起执行。
>
> /////////////// 模拟回滚
>
> 1.开启事务
>
> ```mysql
> SET autocommit = 0;
> START TRANSACTION; # 可选
> ```
>
> 2.编写事务
>
> ```mysql
> UPDATE ACCOUNT SET balance = 1000 WHERE id = 1;
> UPDATE ACCOUNT SET balance = 1000 WHERE id = 2;
> ```
>
> 3.回滚事务
>
> ```mysql
> ROLLBACK;
> ```
>
> 余额没有都变为1000.

#### 事务的隔离级别

| 隔离级别                         | 描述                                                         |
| :------------------------------- | :----------------------------------------------------------- |
| READ UNCOMMITTED（读未提交数据） | 允许事务读取未被其他事务提交的变更。脏读，不可重复读和幻读的问题都会出现。 |
| READ COMMITTED（读已提交数据）   | 只允许事务读取已经被其他事务提交的变更。可避免脏读，但不可重复读和幻读无法避免。 |
| REPEATABLE READ（可重复读）      | 确保事务可以多次从一个字段中读取相同的值，在这个事务持续期间，禁止其他事务对这个字段进行更新。可以避免脏读和不可重复读，但幻读无法避免。 |
| SERIALIZABLE（串行化）           | 确保事务可以从一个表中读取相同的行，在这个事务持续期间，禁止其他事务对该表执行插入、更新和删除操作。所有并发问题都可以避免，但性能十分低下。 |

Oracle支持READ COMMITTED和SERIALIZABLE，默认为READ COMMITTED。

MySQL支持4种事务隔离级别，默认是REPEATABLE READ。

##### 演示4中隔离级别

>进入cmd
>
>```mysql
>mysql -uroot -p6623;
>
>select @@tx_isolation; # 查看隔离级别
>
>set session transaction isolation level read uncommitted; # 设置 read uncommitted的级别的隔离。仅针对当前连接有效
>
>set global session transaction isolation level read uncomitted; # 设置全局隔离级别
>
>set names gbk; # 防止中文乱码
>```
>
>演示前提：确保每次演示数据都是一样的。
>
>```mysql
>CREATE TABLE ACCOUNT(
>	id INT(10) PRIMARY KEY,
>	NAME VARCHAR(20),
>	balance INT(10)
>
>);
>INSERT INTO ACCOUNT (id, NAME, balance) VALUES(1,'张无忌',1000),(2,'张敏',1000);
>```
>
>打开窗口1和窗口2，进入account所在的库。
>
>set names gbk;
>
>* 演示READ UNCOMMITTED
>
>分别执行：set session transaction isolation level read uncommitted; 
>
>1.在窗口1开启事务
>
>```mysql
>set autocommit = 0;
>
>update account set name='jack' where id = 1;
>```
>
>2.在窗口2进行查询
>
>```mysql
>set autocommit = 0; # 开启事务
>
>select name from account where id = 1; # 得到的结果为jack，但此时窗口1的事务并未提交。出现脏读。
>
>commit;
>```
>
>3.在窗口1执行回滚
>
>rollback; # 此时id=1对应的name为张无忌。
>
>* 演示READ COMMITTED
>
>分别执行：set session transaction isolation level read committed; 
>
>1.在窗口执行
>
>```mysql
>set autocommit =0;
>
>update account set name='jack' where id  = 1;
>```
>
>2.在窗口2执行
>
>```mysql
>set autocommit = 0;
>
>select name from account where id = 1; # 得到的结果依然为'张无忌'，避免了脏读。
>```
>
>3.在窗口1中提交事务
>
>```mysql
>commit;
>```
>
>4.在窗口2执行
>
>```mysql
>select name from account where id = 1; # 得到的结果为'jack'，和步骤2不一致，出现不可重复读取。
>
>commit;
>```
>
>
>
>* 演示REPEATABLE READ
>
>分别执行：set session transaction isolation level REPEATABLE READ; 
>
>1.在窗口1执行：
>
>```mysql
>set autocommit = 0;
>
>update account set name='jack' where id = 1;
>```
>
>2.在窗口2执行：
>
>```mysql
>set autocommit = 0;
>
>select name from account where id = 1; # 得到的结果为'张无忌';
>```
>
>3.在窗口1执行：
>
>```mysql
>commit;
>```
>
>4.在窗口2执行
>
>```mysql
>select name from account where id = 1; # 得到的结果依然为'张无忌'，和步骤2一致，所以避免了不可重复读。
>
>commit;
>```
>
>继续：在该隔离界别下演示幻读
>
>1.在窗口1执行
>
>```mysql
>set autocommit = 0;
>
>select name from account;  # 看到2条记录
>```
>
>2.在窗口2执行
>
>```mysql
>set autocommit = 0;
>
>insert into account(id, name, balance) values(3,'张三', 10000);
>
>commit;
>```
>
>3.在窗口1执行
>
>```mysql
>update account set name = '佚名'; # 发现3行受影响！！！步骤1明明查出了2行记录，步骤3却有3行。出现幻读。
>
>commit;
>```
>
>
>
>* 演示SERIALIZABLE
>
>分别执行：set session transaction isolation level SERIALIZABLE; 
>
>1.在窗口1中执行
>
>```mysql
>set autocommit = 0;
>
>select name from account; # 看到2条记录
>```
>
>2.在窗口2中执行
>
>```mysql
>set autocomit = 0;
>
>insert into account(id, name, balance) values(3,'张三', 10000); # 执行报错！！！
>```
>
>3.在窗口1执行
>
>```mysql
>update account set name = '佚名';# 发现受影响的为2行，和步骤1一致。避免了幻读。
>```
>
>
>
>* 演示savepoint
>
>```mysql
>set autocommit = 0;
>
>delete from account where id = 1;
>
>savepoint a; # 设置保存点
>
>delete from account where id = 2;
>
>rooback to a;  #回滚到保存点a
>```
>
>
>
>* 演示delete和truncate在事务使用时的区别
>
>1.演示delete，支持回滚
>
>```mysql
>set autocommit = 0;
>
>delete from account;
>
>rollback;
>```
>
>2.演示truncate，不支持回滚。
>
>```mysql
>set autocommit = 0;
>
>truncate table account;
>
>rollback;
>```
>
>



### 五、视图

##### 概念：

​	MySQL从5.0.1版本开始提供视图功能。它是一种虚拟存在的表，行和列的数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的，只保存SQL逻辑，不保存查询结果。

##### 应用场景：

	- 多个地方用到同样的查询结果，即重用SQL语句。
	- 该查询结果使用的SQL语句较复杂。
	- 保护数据，提高安全性。基于视图，无法推测原始表的设计。

##### 视图创建语法：

>​	create view 视图名
>
>​	as
>
>​	查询语句;

##### 例子：

>案例一：查询姓名中包含a字符的员工名，部门名和工种信息
>
>// 非视图版
>
>```mysql
>SELECT e.last_name, d.department_name, j.job_title
>FROM employees e 
>JOIN departments d ON e.department_id = d.department_id
>JOIN jobs j ON e.job_id = j.job_id
>WHERE e.last_name LIKE '%a%';
>```
>
>// 视图版
>
>1.创建视图
>
>```mysql
>CREATE VIEW myv1
>AS 
>SELECT e.last_name, d.department_name, j.job_title
>FROM employees e 
>JOIN departments d ON e.department_id = d.department_id
>JOIN jobs j ON e.job_id = j.job_id;
>```
>
>2.从视图中查询数据
>
>```mysql
>SELECT *
>FROM myv1 
>WHERE last_name LIKE '%a%'
>```
>
>案例2：查询各部门的平均工资级别
>
>// 查询版
>
>```mysql
>SELECT f.department_id, j.grade_level
>FROM (
>SELECT department_id, AVG(salary) avg_sal
>FROM employees 
>GROUP BY department_id) f
>JOIN job_grades j ON f.avg_sal BETWEEN j.lowest_sal AND j.highest_sal AND f.department_id IS NOT null
>```
>
>// 视图版
>
>1.创建视图
>
>```mysql
>CREATE VIEW myv2
>AS
>SELECT department_id, AVG(salary) avg_sal
>FROM employees 
>GROUP BY department_id;
>```
>
>2.查询视图
>
>```mysql
>SELECT f.department_id, j.grade_level
>FROM myv2 f
>JOIN job_grades j ON f.avg_sal BETWEEN j.lowest_sal AND j.highest_sal AND f.department_id IS NOT null
>```
>
>

##### 视图修改

>方式一：
>
>create or replace view 视图名
>
>as
>
>查询语句;
>
>方式二：
>
>alter view 视图名
>
>as 
>
>查询语句;

##### 视图修改的例子

>// 创建视图
>
>```mysql
>CREATE VIEW myv3
>AS
>SELECT last_name
>FROM employees;
>```
>
>// 修改视图
>
>```mysql
>CREATE OR REPLACE VIEW myv3
>AS 
>SELECT last_name,first_name
>FROM employees;
>```
>
>// 修改视图
>
>```mysql
>ALTER VIEW myv3
>AS
>SELECT last_name, salary
>FROM employees;
>```
>
>

##### 删除视图

>drop view 视图名1， 视图名2, ...;

##### 视图的更新（CUD）

> 对视图里面的数据进行CUD操作，会在原始表中同步。这是一个危险的操作。
>
> 具备以下特点的视图不允许更新
>
> ①包含以下关键字的SQL语句：分组函数、distinct、group by、having、union或union all
>
> ②常量视图，如create view myv1 as select 'john' name;
>
> ③select中包含子查询
>
> ④join
>
> ⑤from一个不能更新的视图
>
> ⑥where子句的子查询引用了from子句中的表

### 六、变量

* 系统变量

  由系统提供，非用户定义，属于服务器层面。

  语法：

  注意：如果是全局变量，则需要加global，如果是会话级别，则需要加session；默认session。

  1.查看所有的系统变量：

  ​	show global  |【session】 variables;

  ```mysql
  show global variables;
  
  show session variables;
  ```

  2.查看满足条件的部分系统变量：

  ​	show global  |【session】 variables like '%char%';

  ```mysql
  	SHOW GLOBAL VARIABLES LIKE '%character%';
  
  	show session variables like '%character%';
  ```

  3.查看指定的某个系统变量的值:

  ​	select @@global |【session】 .系统变量名;

  ```mysql
  	SELECT @@GLOBAL.autocommit;
  
  	select @@session.autocommit; // select @@tx_isolation;
  ```

  4.为某个系统变量复制

  ​	set @@global |【session】 .系统变量名 = 值; 

  ```mysql
  	set @@global.autocommit=0;
  
  	set @@session.tx_isolation = 'read-uncommitted'; 
  
  	# set session tx_isolation = 'read_committed';
  ```

  

  * 全局变量

    服务器每次启动将为所有的全局变量赋初始值。针对所有的会话（连接）有效，但是不能跨重启。

  * 会话变量

    作用域：仅仅针对当前会话（连接）有效。

* 自定义变量

   * 用户变量

     说明：用户自定义的，不是系统提供的。

     ​	作用域：针对当前会话（连接）有效，等同于会话变量的作用域。它可以用在任何地方。

     ​	使用步骤：

     ​		声明：声明并初始化

     ​			set @用户变量名=值;   # set @name='john';

     ​			set @用户变量名:=值;

     ​			select @用户变量名:=值;

     ​		赋值：更新用户变量的值

     ​			通过set或select

     ​			set @用户变量名=值; 

     ​			set @用户变量名:=值;

     ​			select @用户变量名:=值;			

     ​			通过select into

     ​			select 字段 into 变量名 from 表;  # select count(*) into @count from user;

     ​		使用：（查看、比较、运算等）

     ​			select @用户变量名; # select @count;

   * 局部变量

     作用域：仅仅在定义它的begin end中有效。只用在begin end的第一句话。

     声明：

     ​	declare 变量名 类型;

     ​	declare 变量名 类型 default 值;

     赋值：

     ​	通过set或select

     ​	set  局部变量名 = 值; 

     ​	set  局部变量名 := 值;

     ​	select @局部变量名 := 值;			

     ​	通过select into 局部变量名 from 表;

     使用：

     ​	select 局部变量名;

     |          | 作用域      | 定义和使用位置                  | 语法                        |
     | -------- | ----------- | ------------------------------- | --------------------------- |
     | 用户变量 | 当前会话    | 会话中的任何地方                | 必须加@符号，不用限定类型   |
     | 局部变量 | begin end中 | 只能在begin end中，且为第一句话 | 一般不加@符号，需要限定类型 |

     案例：声明两个变量并赋初始值，求和，并打印。

     >1.用户变量
     >
     >```mysql
     >SET @m = 1;
     >SET @n = 2;
     >SET @SUM = @m + @n;
     >SELECT @SUM ;
     >```
     >
     >2.局部变量式的写法
     >
     >

### 七、存储过程和函数

类似于Java中的方法

* 存储过程

  含义：一组预先编译好的SQL语句的集合，理解成批处理语句。

  优点：

  * 提高代码的重用性
  * 简化操作
  * 减少了编译次数并且减少了和服务器的连接次数，提高了效率

  创建语法：

  >create procedure 存储过程名(参数列表)
  >
  >begin
  >
  >​	存储过程体（一组合法的SQL语句）
  >
  >end
  >
  >注意：
  >
  >* 参数列表包含三部分：
  >
  >  * 参数模式
  >    * in：该参数可以作为输入，即该参数需要调用方传入值。
  >    * out：该参数可以作为输出，即该参数可以作为返回值。
  >    * inout：该参数具有in和out两种功能。
  >  * 参数名
  >  * 参数类型
  >
  >  举例： 
  >
  >  ​	in stuname varchar(20)
  >
  >* 如果存储过程体仅仅只有一句话，begin end可以省略。
  >
  >* 存储过程体中的每条SQL语句的结尾要求必须加分号。
  >
  >* 存储过程的结尾可以使用delimiter重新设置。
  >
  >  * 语法
  >
  >    delimiter 结束标记
  >
  >    delimiter $
  >
  >

  调用语句：

  >call 存储过程名(实参列表);

  举例：

  >1.空参列表
  >
  >// 案例：插入到user表中2条记录
  >
  >```mysql
  >DELIMITER $
  >
  >CREATE PROCEDURE myp1()
  >
  >begin
  >
  >	insert into user(id, name) values(1,'jack'),(2,'rose');
  >
  >end $
  >
  >call myp1() $  // 调用
  >```
  >
  >2.创建带in模式参数的存储过程
  >
  >//案例：创建存储过程实现：根据女神名，查询对应的男神信息
  >
  >```mysql
  >create procedure myp2(IN beautyName VARCHAR(20))
  >BEGIN 
  >	SELECT bo.*
  >	FROM boys bo
  >	RIGHT JOIN beauty b ON bo.id = b.boyfriend_id
  >	WHERE b.name =beautyName;
  >END $
  >
  >call myp2('赵敏') $  // 调用
  >```
  >
  >//案例2：创建存储过程实现：用户是否登录成功
  >
  >```mysql
  >create procedure myp3(in username varchar(20), in password varchar(20))
  >
  >begin
  >
  >	declare result int default 0; # 声明并初始化
  >
  >	select count(*) into result # 赋值
  >
  >	from admin
  >
  >	where admin.username = username
  >
  >	and admin.password = password;
  >
  >	select if(result>0, '登录成功', '登录失败'); # 使用
  >
  >end $
  >
  >call myp3('张飞', '1234234'); # 调用
  >```
  >
  >3.创建带out模式的存储过程
  >
  >// 案例1：根据女神名，返回对应的男神名
  >
  >```mysql
  >create procedure myp5(in beautyName varchar(20), out boyName varchar(20))
  >
  >begin
  >
  >	select bo.boyName into boyName
  >
  >	from boys bo
  >
  >	join beauty b on bo.id = b.boyfriend_id
  >
  >	where b.name = beautyName; 
  >
  >end $
  >
  >call myp5('赵敏',bName ) $ #调用
  >
  >select @bName$
  >```
  >
  >// 案例2：根据女神名，返回对应的男神名和男神魅力值
  >
  >```mysql
  >create procedure myp6(in beautyName varchar(20), out boyName varchar(20), out userCP INT)
  >
  >	begin
  >
  >	select bo.boyName, bo.userCP into boyName, userCP
  >
  >	from boys bo
  >
  >	join beauty b on bo.id = b.boyfriend_id
  >
  >	where b.name = beautyName; 
  >
  >end $
  >
  >call myp6('赵敏', @bName, @usercp) $ #调用
  >
  >select @bName, @usercp;
  >```
  >
  >4.创建带inout的模式参数的存储过程
  >
  >// 案例1：传入a和b两个值，最终a和b都翻倍并返回
  >
  >```mysql
  >create procedure myp8(inout a int, inout b int)
  >
  >begin
  >
  >	set a = a*2;
  >
  >	set b = b*2;
  >
  >end$
  >```
  >
  >

  删除存储过程

  >语法
  >
  >​	drop procedure 存储过程名;

  查看存储过程

  >语法
  >
  >​	show create procedure myp2;

* 函数

  含义和存储过程一样。

  区别：

  > 存储过程可以有0个返回，也可以有多个返回，适合做批量插入、批量更新；而函数有且仅有1个返回，适合做处理数据后返回一个结果。

  语法：

  >创建语法：
  >
  >create funcation 函数名(参数列表) returns 返回类型
  >
  >begin
  >
  >​	函数体
  >
  >end
  >
  >注意：
  >
  >* 参数列表包含两部分
  >  * 参数名
  >  * 参数类型
  >* 函数体肯定要有return语句，如果没有也不报错，建议把return放最后
  >* 当函数体只有一句话，则可以省略begin end
  >* 使用delimiter语句设置结束标记
  >
  >调用语法
  >
  >select 函数名(参数列表)
  >
  >查看语法
  >
  >show create function 函数名;
  >
  >删除语法
  >
  >drop function 函数名;
  >
  >

  案例

  >```mysql
  >delimiter $   # 结尾不加分号！！
  >```
  >
  >1.无参有返回
  >
  >// 返回公司的员工个数
  >
  >```mysql
  >create function myf1() returns int
  >begin
  >	declare c int default 0;
  >	select count(*) into c
  >	from employees;
  >	return c;
  >end $
  >
  >select myf1() $ # 调用
  >```
  >
  >2.有参有返回
  >
  >// 根据员工邮箱，返回它的工资
  >
  >```mysql
  >create function myf4(myemail varchar(20)) returns double
  >begin 
  >	set @sal=0; 
  >	select salary into @sal
  >	from employees
  >	where email = myemail;
  >	return @sal;
  >end $
  >
  >select myf4('NGREENBE') $
  >```
  >
  >

### 八、流程控制结构

MySQL有顺序，分支和循环结构。并且如果没有else语句且没有符合条件的分支，则返回NULL。

* #### 分支结构

>1.if函数
>
>功能：
>
>​	实现简单的双分支
>
>语法：
>
>​	if(表达式1, 表达式2, 表达式3)
>
>执行顺序：
>
>​	如果1成立，则返回2，否则返回3.
>
>2.case结构
>
>* 等值判断（类似Java的switch）
>
>语法：
>
>​	case 变量|表达式|字段
>
>​	when 要判断的值 then 返回的值1或语句1;
>
>​	when 要判断的值 then 返回的值2或语句2;
>
>​	...
>
>​	else 要返回的值n或语句n;
>
>​	end case;
>
>* 区间判断（类似Java多重if）
>
>语法：
>
>​	case
>
>​	when 要判断的条件1 then 返回的值1或语句1;
>
>​	when 要判断的条件2 then 返回的值2或语句2;
>
>​	...
>
>​	else 要返回的值n或语句n;
>
>​	end case;
>
>特点：
>
>​	①：可以作为表达式，嵌套在其他语句中使用，可以放在任何地方
>
>​	②：可以作为独立的语句使用，只能放在begin end中。
>
>案例：创建存储过程，根据传入的成绩，来显示等级，比如90-100显示A,80-90显示B，60-80显示C，否则显示D
>
>```mysql
>delimiter $
>CREATE PROCEDURE mygrade(score INT)
>BEGIN 
>	case
>	when score >= 90 AND score < 100 then  SELECT 'A';
>	when score >= 80 AND score < 90  then SELECT 'B';
>	when score >= 60 AND socre < 80  then  SELECT 'C';
>	ELSE SELECT 'D';
>	END case; 
>END $
>call mygrade(88) $
>```
>
>3.if结构
>
>功能：实现多重分支
>
>语法：
>
>​	if 条件1 then 语句1;
>
>​	elseif 条件2 then 语句2;
>
>​	elseif 条件3 then 语句3;
>
>​	....
>
>​	【else 语句n;】
>
>​	end if;
>
>场景：只能用在begin end中
>
>案例：创建存储过程，根据传入的成绩，来显示等级，比如90-100显示A,80-90显示B，60-80显示C，否则显示D
>
>```mysql
>delimiter $
>CREATE PROCEDURE testGrade(score INT)
>BEGIN 
>	if(score >= 90 AND score < 100) then  SELECT 'A';
>	elseif(score >= 80 AND score < 90)  then  SELECT 'B';
>	elseif(score >= 60 AND score < 80)  then  SELECT 'C';
>	ELSE SELECT 'D';
>	END if;
>END $
>call testGrade(88) $
>```
>
>

* #### 循环结构

MySQL具有while、loop和repeat三种循环结构。两种循环控制，iterate类似于java的continue，结束本次循环，继续下一次;leave类似于break，跳出，结束当前所在的循环。

>1.while
>
>语法：
>
>​	【标签:】while 循环条件 do
>
>​		循环体;
>
>​	end while 【标签】;
>
>案例：没有添加循环控制
>
>```mysql
># 批量插入，根据次数插入到admin表中多条记录
>delimiter $
>create procedure pro_while(in insertCount int)
>begin
>	declare i int default 1;
>	while i <= insertCount do
>		insert into admin(username, `password`) values (concat('rose',i), '666');
>		set i = i+1;
>	end while;
>end $
>call pro_while(20)$
>```
>
>案例：添加leave语句
>
>```mysql
># 批量插入，根据次数插入到admin表中多条记录，如果次数>20则停止
>delimiter $
>truncate table admin$
>drop procedure pro_while$
>create procedure pro_while(in insertCount int)
>begin
>	declare i int default 1;
>	a:while i <= insertCount do
>		insert into admin(username, `password`) values (concat('rose',i), '666');
>		if i >= 20 then leave a;
>		end if;
>		set i = i+1;
>	end while a;
>end $
>call pro_while(20)$
>
>```
>
>案例：添加iterator
>
>```mysql
># 批量插入，根据次数插入到admin表中多条记录，只插入偶数次
>delimiter $
>truncate table admin$
>drop procedure pro_while$
>create procedure pro_while(in insertCount int)
>begin
>	declare i int default 0;
>	a:while i <= insertCount do
>		set i = i+1;
>		if mod(i, 2) != 0 then iterate a;
>		end if;
>		insert into admin(username, `password`) values (concat('rose',i), '666');
>	end while a;
>end $
>call pro_while(20)$
>
>```
>
>2.loop
>
>语法：
>
>​	【标签:】loop
>
>​			循环体;
>
>​	end loop 【标签】;
>
>
>
>3.repeat
>
>语法：
>
>【标签:】repeat
>
>​		循环体;
>
>unitl 结束循环的条件;
>
>end repeat 【标签】;
>
>案例：
>
>```mysql
># 已知表stringcontent，id自增长，content varchar(20)，向该表插入指定个数的随机字符串
>drop table if exists stringcontent;
>create table stringcontent(
>	id int primary key auto_increment,
>    content varchar(20)
>);
>delimiter $
>create procedure test_rand_str_insert(in insertCount int)
>begin
>	declare i int default 1; # 循环变量，代表插入次数
>	declare str varchar(20) default 'abcdefghijklmnopqrstuvwxyz';
>	declare startIndex int default; # 代表起始索引
>	declare len int default 1; # 代表截取的字符长度
>	while i <= insertCount do
>		set len = floor(rand() * (20 -startIndex + 1)+1); # 产生一个随机的整数，代表截取长度 1-(26 - startIndex+1)
>		set startIndex = floor(rand() * 26 + 1); # 产生一个随机整数，代表起始索引1-26
>		insert into stringcontent(content) values(substr(str, startIndex, len));
>		set i = i+1; # 循环变量更新
>	end while;
>end $
>
>call test_rand_str_insert(10) $
>select * from stringcontent$
>```

