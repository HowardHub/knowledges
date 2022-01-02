MySQL

一、DQL（数据查询语言）

1.基础查询

select 查询列表 from 表名;

select 100; # 查询常量

select 100* 21; #查询表达式

select version(); #查询函数

select 'jack'+90; # 结果为90，因为mysql将无法转化为数值的操作数都转为0

select null+90; # 只要一方为null，结果为null

desc user; # 显示user表的结构

2.条件查询

select 查询列表 from 表名 where 筛选条件 #先执行from,再执行where，最后执行select

select * from user where age > 60 and age < 10 or salary > 15000; # 用不用括号结果都是一样的，因为每条结果都必须满足这三个条件。

like, between and, in , is (not) null # 模糊查询的关键字

like通常和%一起使用，%是通配符，代表任意多个字符。而_代表任意单个字符

select user_name from user where user_name like '_\_%'; # 查询用户名中第二个字符为 _ 的用户名

select user_name from user where user_name like '_$_%' escape '$'; #escape是转译的意思，类似反斜杠

![image-20211130082649474](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211130082649474.png)

age between 10 and 120 # 等价于 age >= 10 and age <= 120



3.排序查询



>order by 子句支持单个字段、多个字段、表达式、函数、别名。
>
>order by 子句一般放在查询语句的最后面（limit子句除外）。
>
>select 查询列表
>
>from 表
>
>where 筛选条件
>
>order by 排序列表
>
>执行顺序：from -> where -> select -> order by



4.常见函数

select 函数名(实参列表)  [from 表];

* 单行函数：字符函数，数学函数，流程控制函数

>* 字符函数：concat,length,upper.lower,substr,substring,trim,lpad
>
>length返回字节长度，其他的比如substr是针对字符的
>
>select trim('a', from 'aaaaaa何志a鹏aaaa') as out_put; # 去前后a
>
>select LPAD('何志鹏', 4, '*') as output; # *何志鹏
>
>* 数学函数：round(四舍五入), ceil(向上取整), floor(向下取整),truncate(截断)
>
>select round(1.567,2); # 1.57
>
>select truncate(1.55555,2); # 1.55
>
>* 日期函数：now(日期+时间), curdate(日期),curtime(时间)，str_to_date,date_format
>
>str_to_date('9-13-1999', '%m-%d-%Y'); # 1999-09-13,将字符转换日期
>
>date_format('2018/6/6','%Y年%m月%d日') # 2018年6月6日，将日期转为字符
>
>![image-20211203001850274](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211203001850274.png)
>
>* 流程控制函数：if(实现if else的效果), case(类似switch case的效果)
>
>   -- 这种case类似Java中的switch case用于等值判断
>SELECT salary 原始工资, department_id,
>	case department_id
>	when 30 then salary * 1.1
>	when 40 then salary * 1.2
>	when 50 then salary * 1.3
>	ELSE salary 
>	END AS 新工资
>FROM employees;
>	
>-- 类似Java的多重if,用于区间判断
>SELECT salary, 
>	case 	
>	when salary > 20000 THEN 'A'
>	when salary > 15000 then 'B'
>	when salary > 10000 then 'C'
>	ELSE 'D' END AS 工资级别
>FROM employees;
>
>### 具体的函数可以使用Heidi SQL的F1来进行查找



* 分组函数

>用于统计，又称聚合函数或组函数，给定一组输入，得到一个输出
>
>* sum,avg,max,min,count 
>
>##### 这几个函数都会忽略Null值，且都可以和distinct搭配使用。
>
>count的详细介绍
>
>select count(*) from user; # 统计总行数，比count(name)更实用
>
>select count(1) from user; # 相当于在表中加了一列常量值1，并统计个数
>
>效率：在INNODB下，count(*)和count(1)的效率差不多，但比count(id)要高一些
>
>** 和分组函数一同查询的字段的限制：只能配合group by 后面的字段使用
>
>select avg(age), user_id from user; 
>
>#####  上面的查询结果逻辑上是一张不规则的表，所以没有意义。
>
>





6.分组查询

> select 分组函数, 列(要求出现在group by 的后面)
>
> from 表
>
> [where 筛选条件]
>
> group by 分组的列表
>
> [order by 子句]
>
> 注意：查询列表必须特殊，要求是分组函数和group by后出现的字段
>
> |            | 数据源         | 位置               | 关键字 |
> | ---------- | -------------- | ------------------ | ------ |
> | 分组前筛选 | 原始表         | group by子句的前面 | where  |
> | 分组后筛选 | 分组后的结果集 | group by子句的后面 | having |
>
> 

7.连接查询

![image-20211206080929395](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211206080929395.png)

> 又叫多表查询，当查询的字段来自多个表时，就会用到连接查询。
>
> 按功能分类
>
> * 内连接(SQL92语法)
>
>   ##### 等值连接，非等值连接和自连接都可以转换为SQL99语法形式，用inner join关键字
>
>   * 等值连接：(连接条件用了等号)
>
>     select name,boyName from boys,beauty where beauty.boyfriend_id  = boys.id;
>
>     1.多表等值连接的结果为多表的交集部分
>
>     2.n表连接，至少需要n-1个连接条件
>
>     3.多表的顺序没有要求
>
>     4.一般需要为表取别名
>
>     5.可以搭配前面介绍的所有子句使用
>
>   * 非等值连接：(连接条件用非等号)
>
>     查询员工的工资和工资级别
>
>     SELECT e.salary, g.grade_level
>     FROM employees e, job_grades g
>     WHERE e.salary between g.lowest_sal AND g.highest_sal
>
>     SQL99写法(inner join 实现)
>
>     查询每个工资级别的个数，并按工资级别降序
>     SELECT j.grade_level, COUNT(*) 个数
>     FROM employees e
>     inner JOIN job_grades j ON e.salary BETWEEN j.lowest_sal AND j.highest_sal
>     GROUP BY j.grade_level
>     ORDER BY j.grade_level DESC;
>
>   * 自连接：（相当于等值连接，不过是自己连接自己）
>
>     查询员工名和上级名称
>     SELECT e.last_name 员工名, m.last_name 上级名
>     FROM employees e, employees m
>     WHERE e.manager_id = m.employee_id;
>
> * 外连接 (SQL99语法)
>
>   **优势**：可以查询一个表中有，另一个表中没有的记录，内连接只能查询两表交集。
>
>   外连接分为主从表，查询结果为主表中的所有记录；如果从表中有和它匹配的，则显示匹配的值，如果从表中没有和它匹配的，则显示null。所以（左右）外连接查询结果 = 内连接结果+主表中有而从表没有的记录。
>
>   语法：
>
>   select 查询列表
>
>   from 表1 别名【连接类型】join 表2 别名on 连接条件
>
>   【where】
>
>   【group by】
>
>   【having】
>
>   【order by】
>
>   ##### SQL99优点：筛选条件放在where后面，连接条件放在on后面，提高分离性，便于阅读。所以后面就要确认是筛选还是连接条件。
>
>   * 左外连接：left join左边的是主表
>
>     查询男朋友不在男神表中 的女神名。（左外）
>
>     SELECT be.name, bo.boyName
>     FROM  beauty be
>     LEFT JOIN boys bo ON be.boyfriend_id = bo.id 
>     WHERE bo.id IS NULL;
>
>     **注意**：这里用bo.id is null不用userCP,因为如果userCP为null，boyName不为null，就会令结果多一条记录。而id是key，它是非空的。
>
>     查询哪个部门没有员工：查部门，所以部门是主表，用左外连接，所以部门表在left join左边。
>
>     SELECT distinct d.department_name
>     FROM  departments d 
>     LEFT JOIN employees e ON e.department_id = d.department_id
>     WHERE e.employee_id IS NULL;
>
>     这个”查没有的“在工作中很常用！
>
>     ![image-20211206081027566](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211206081027566.png)
>
>     查询哪个城市没有部门
>
>     SELECT lo.city, de.department_name
>     FROM locations lo
>     LEFT JOIN departments de ON lo.location_id = de.location_id
>     WHERE de.department_id IS NULL;
>
>     查询部门名为SAL或IT的员工信息。
>
>     **分析：**要确保所有SAL和IT的部门都能出来，叫IT部门有三个，但只有一个有员工，所以结果中多了两条为NULL的员工记录
>
>     SELECT em.*
>     FROM   departments de 
>
>     LEFT JOIN    employees em   ON em.department_id = de.department_id
>     WHERE de.department_name = 'SAL' OR de.department_name = 'IT' 
>
>   * 右外连接： right join右边的是主表 
>
>     查询男朋友不在男神表中 的女神名。（右外）
>
>     SELECT be.name, bo.boyName
>     FROM boys bo
>     RIGHT JOIN  beauty be   ON be.boyfriend_id = bo.id 
>     WHERE bo.id IS NULL;
>
>   * 全外连接：full  join，MySQL不支持全外。它的结果集=内连接的结果+表1有但表2没有的 + 表2有但表1没有的
>
>     ![image-20211206081158452](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211206081158452.png)
>
>   左外和右外交换两个表的顺序可以实现同样的效果
>
> * 交叉连接: cross join，结果集就是笛卡尔乘积。
>
>   SELECT bo.*, be.*
>   FROM boys bo
>   CROSS JOIN beauty be



8.子查询

>含义：出现在其他语句(CRUD语句都可以，一般select作为主语句的较多)中的select语句，称为子查询或内查询。外部查询称为主查询
>
>特点：1.放在小括号内、2.放在条件右侧、3.标量子查询一般搭配单行操作符(>,<,=...)使用、4.列子查询一般搭配多行操作符(in,any,all)使用。
>
>分类：
>
>按子查询出现的位置：
>
>* select后面：仅仅支持标量只查询（一行一列的结果集）
>
>  1.查询每个部门的员工个数
>  SELECT department_id, (SELECT COUNT(t2.employee_id) FROM employees t2 WHERE t2.department_id = t1.department_id   ) count
>  FROM departments t1
>  GROUP BY department_id
>
>  2.查询员工号为102的部门名
>  SELECT (SELECT department_name FROM departments d WHERE e.department_id = d.department_id) AS department_name
>  FROM employees e
>  WHERE e.employee_id = 102
>
>* from后面：支持表子查询（查询结果充当数据源，并且必须取别名）
>
>  1.查询每个部门的平均工资的工资等级
>
>  // 我的写法
>
>  SELECT f.department_id, j.grade_level
>  FROM job_grades j
>  RIGHT    JOIN 
>  	(SELECT e.department_id, ifnull(AVG(e.salary),0) avg
>  	FROM employees e
>  	GROUP BY e.department_id) 
>  f ON f.avg BETWEEN j.lowest_sal AND j.highest_sal
>
>  // 标准写法
>
>  SELECT f.department_id, j.grade_level
>  FROM (
>  	SELECT e.department_id, ifnull(AVG(e.salary),0) avg
>  	FROM  employees e
>  	GROUP BY e.department_id
>  ) f
>  inner JOIN job_grades j ON  f.avg BETWEEN j.lowest_sal AND j.highest_sal
>
>  2.查询各部门中工资比本部门平均工资高的员工的工号、姓名和工资
>  ①各部门平均工资 -- 多行多列-表子查询
>
>  SELECT department_id, AVG(salary) sal
>  FROM employees 
>  GROUP BY department_Id;
>  ②查询比①高的员工号、姓名和工资
>  SELECT e.employee_id, e.last_name, e.salary,e.department_id
>  FROM (
>  	SELECT department_id, AVG(salary) sal
>    FROM employees 
>    GROUP BY department_Id
>  ) f
>  JOIN employees e ON e.department_id = f.department_id 
>  WHERE e.salary>f.sal;
>
>* where或having后面：
>
>  ★支持标量子查询、
>
>  1.谁的工资比Abel高的
>
>  SELECT last_name,salary
>  FROM employees
>  WHERE salary > (SELECT salary FROM employees WHERE last_name = 'Abel');
>
>  2.返回job_id与141号员工相同，salary比143号员工多的员工、姓名，job_id和工资
>  SELECT last_name, job_id, salary
>  FROM employees
>  WHERE job_id = (SELECT job_id FROM employees WHERE employee_id = 141) 
>  		AND salary > (SELECT salary FROM employees WHERE employee_id = 143)；
>
>  3.返回公司工资最少的员工的last_name, job_id和salary
>  SELECT last_name, job_id, salary
>  FROM employees
>  WHERE salary = (SELECT MIN(salary) FROM employees);
>
>  4.查询最低工资大于50号部门最低工资的部门id和其最低工资
>  SELECT department_id, MIN(salary)
>  FROM employees
>  GROUP BY department_id
>  HAVING  MIN(salary) > (SELECT MIN(salary) as salary FROM employees WHERE department_id = 50 );
>
>  5.50号部门的最低工资
>  SELECT MIN(salary) salary
>  FROM employees
>  WHERE department_id = 50;
>
>  6.查询每个部门的最低工资
>  SELECT MIN(salary)
>  FROM employees
>  GROUP BY department_id;
>
>  ★列子查询
>
>  1.返回location_id是1400或1700的部门的所有员工姓名
>
>  SELECT last_name
>  FROM employees
>  WHERE department_id IN (SELECT department_id FROM departments WHERE location_id IN(1400, 1700));
>
>  2.返回其他工种中比job_id为"IT_PROG"工种任一工资低的员工的员工号、姓名、job_id以及salary。分析：比最大值小就是比任一一个小
>
>  SELECT employee_id, last_name, job_id, salary
>  FROM employees 
>  WHERE job_id != 'IT_PROG' AND salary < any(SELECT salary FROM employees WHERE job_id = 'IT_PROG');
>
>  SELECT employee_id, last_name, job_id, salary
>  FROM employees 
>  WHERE job_id != 'IT_PROG' AND salary < (SELECT MAX(salary) FROM employees WHERE job_id = 'IT_PROG');
>
>  行子查询
>
>* exists后面（相关子查询）：支持所有的子查询
>
>  语法：exists(完整的查询语句)
>
>  结果：1或0
>
>  1.查询有员工的部门名
>  SELECT t1.department_name
>  FROM departments t1
>  WHERE EXISTS(SELECT * FROM employees t2 WHERE  t2.department_id = t1.department_id);
>
>  // 用in取代，https://www.bilibili.com/video/BV12b411K7Zu?p=93&spm_id_from=pageDriver
>
>  // 自己的写法
>
>  SELECT f.department_name FROM (
>  	SELECT t1.department_name, count(t2.employee_id) COUNT
>  	FROM departments t1
>  	LEFT JOIN employees t2 ON t1.department_id = t2.department_id
>  	GROUP BY t1.department_id
>  	HAVING COUNT > 0
>  )f
>
>按结果集的行列数不同：
>
>* 标量子查询（结果集只有一行一列）
>
>* 列子查询（结果集只有一列多行）
>
>* 行子查询（结果集为多行多列）
>
>  查询员工编号最小并且工资最高的员工信息
>
>  // 普通写法
>
>  SELECT *
>  FROM employees
>  WHERE employee_id = (SELECT MIN(employee_id) FROM employees) AND salary = (SELECT MAX(salary) FROM employees);
>
>  // 使用具有局限性，要求多个条件用相同操作符
>
>  SELECT *
>  FROM employees
>  WHERE (employee_id,  salary ) = (SELECT MIN(employee_id),MAX(salary) FROM employees);
>
>* 表子查询（结果集为多行多列）
>
>

9.分页查询

>limit offset, size;
>
>offset：要显示条目的起始索引（从0开始）
>
>size：要显示的条目个数
>
>1.查询前5条员工信息
>SELECT *
>FROM employees
>LIMIT 0, 5;
>
>2.查询第11条到第25条。offset=11-1, size = 25-11+1
>SELECT *
>FROM employees
>LIMIT 10, 15;
>
>公式：要显示的页数page,每页显示的条目数size
>
>limit (page-1) * size, size;

小结：执行顺序

>##### 语法：
>
>select 查询列表
>
>from 表
>
>连接类型 join 表2 on 连接条件
>
>where 筛选条件
>
>group by 分组列表
>
>having 分组后的筛选
>
>order by 排序列表
>
>limit 偏移,条目数;
>
>##### 执行顺序：from -> join -> on -> where -> group by -> having -> select -> order by -> limit 
>
>##### 每条子句的执行都会生成一个虚拟表

例子：

>1.查询平均工资最低的部门信息
>SELECT *
>FROM departments dept
>JOIN ( SELECT department_id, AVG(salary) avg_sal
>       FROM employees
>       GROUP BY department_id
>		 ORDER BY avg_sal ASC
>		 LIMIT 1
>) f ON f.department_id = dept.department_id
>
>-- 优化版
>SELECT *
>FROM departments 
>WHERE department_id = (SELECT department_id FROM employees GROUP BY department_id ORDER BY AVG(salary)  ASC LIMIT 1);

10.union联合查询

>将多条查询语句的结果合并为一个结果。
>
>select * from employees where email LIKE '%a%'
>
>UNION
>
>select * from employees where department_id > 90;
>
>应用场景：要查询的的结果来自多个表，且多个表没有直接的连接关系，但查询的信息一致时。
>
>注意事项：
>
>​	①：要求多条查询语句的查询列数是一致的。
>
>​	②：要求多条查询语句的查询的每一列的类型和顺序是一致的。
>
>​	③：union关键字默认去重、union all可以包含重复项。

二、DML（数据操作语言）

1.插入语句

>##### 方式1 
>
>语法：
>
>insert [ignore] into 表名(列名1, ...) values(值1, ...);
>
>例子：
>
>insert into user(id, name) values(1111, 'Jack');
>
>insert into user values(2323, 'Rose');
>
>注意：
>
>​	插入的值的类型要与列的类型一致或兼容。
>
>​	不可以为null的列必须插入值。
>
>​	列数和值的个数必须一致。
>
>​	可以省略列名，默认是所有列，而且列的顺序和表中列的顺序是一致的。
>
>​	ignore表示，如果表中已经存在相同的记录，则忽略当前新数据。
>
>##### 方式2
>
>语法
>
>insert into 表名 set 列名=值, 列名2=值, ...;
>
>例子
>
>insert into user set id = 12323, name = '王一博';
>
>##### 方式1和方式2的区别
>
>* 方式1支持插入多行，方式2不支持
>
>  insert into user(id, name) 
>
>  ​	values(1111, 'Jack'),(11222, '马中');
>
>* 方式一支持子查询，方式2不支持
>
>  insert into user(id, name) select 222, '黎明';

2.修改语句

>##### 修改单表记录
>
>语法：
>
>update 表名 set 列1=新值, ...,列n=新值  where 筛选条件;
>
>例子：
>
>update user set name = '李敏' where id = 11222;
>
>##### 修改多表的记录
>
>语法：
>
>update 表1 别名, 表2 别名 set 列=值, ... where 连接条件 and 筛选条件;    // 92语法
>
>update 表1 别名 inner|left|right join 表2 别名 on 连接条件 
>
>​	set  列=值, ...  where 筛选条件; // 99
>
>例子
>
>// 修改张无忌的女朋友的手机号为134234
>
>update boys b JOIN girls g on b.id = g.boyfriend_id 
>
>​	set b.'phone' = '134234' where b.'name' = '张无忌';

3.删除语句

>##### 方式1
>
>语法：
>
>* 单表删除
>
>delete from 表名 where 筛选条件; 
>
>例子：
>
>delete from user where id = '1311';
>
>* 多表删除
>
>// sql92
>
>delete 表1的别名, 表2的别名
>
>from 表1 别名, 表2 别名
>
>where 连接条件 and 筛选条件;
>
>// sql 99
>
>delete 表1的别名, 表2的别名
>
>from 表1 别名
>
>inner|left|right join 表2 别名 on 连接条件
>
>where 筛选条件;
>
>例子：
>
>删除张无忌的女朋友信息
>
>// sql 92
>
>delete g
>
>from girls g
>
>inner join boys b on g.boyfriend_id = b.id
>
>where b.name='张无忌';
>
>// sql 99
>
>delete g
>
>from girls g
>
>inner join boys b on g.boyfriend_id = b.id
>
>where b.name = '张无忌';
>
>删除黄晓明以及他女朋友的信息
>
>delete g, b
>
>from girls g
>
>inner join boys b on g.boyfriend_id = b.id
>
>where b.name='黄晓明'
>
>##### 方式2
>
>语法：
>
>truncate table 表名; // 删表中的所以记录
>
>delete和truncate的区别：
>
>①：delete可以加where条件，truncate 语句不允许加where条件。
>
>②：truncate删除效率比delete高
>
>③：假设要删除的表中有自增长列，如果用delete删除后，再插入数据，自增长列的值从断点开		始；而truncate删除后，再插入数据，自增长的列的值从1开始。
>
>④：truncate删除没有返回值，delete删除有返回值
>
>⑤：truncate删除后不能回滚，delete删除后可以回滚。

三、DDL（数据定义语言）

>作用：库和表的管理
>
>* 库的管理
>
>  * 创建
>
>    create database [if not exists] 库名;
>
>    create database books;
>
>  * 修改（一般不修改库名）
>
>    真要改库名的话，直接修改文件夹，然后重启数据库。
>
>    alter database books character set gbk; // 修改字符集
>
>  * 删除
>
>    drop database [if exists] 库名;
>
>    drop database books;
>
>* 表的管理
>
>  * 创建★
>
>    语法
>
>    create table [if not exists] 表名(
>
>    ​	列名  列的类型  【(长度)】 【约束】，
>
>    ​    列名  列的类型  【(长度)】 【约束】，
>
>     	...
>
>    ​    列名  列的类型  【(长度)】 【约束】，
>
>    );
>
>    例子
>
>    create table book(
>
>      id INT,#编号
>
>      name varchar(20),#书名
>
>      price double,#价格
>
>      author varchar(20), #作者
>
>      publishDate datetime # 出版日期
>
>    );
>
>  * 修改
>
>    通用语法
>
>    alter table 表名 add|drop|modify|change column 列名 列类型 约束;
>
>    * 修改列名
>
>      alter table book change column name book_name varchar(20);
>
>    * 修改列的类型或约束
>
>      alter table book modify column publishDate TIMESTAMP;
>
>    * 添加新列
>
>      alter table book add column type int(10);
>
>    * 删除列
>
>      alter table book drop column type;
>
>    * 修改表名
>
>      alter table book rename to tool_book;
>
>  * 删除
>
>    语法
>
>    drop table 表名;
>
>  * 表的复制
>
>    * 仅仅复制表的结构
>
>      create table book_copy like book;
>
>    * 复制表的结构+数据
>
>      create table  book_copy2 select * from author;
>
>    * 只复制部分数据
>
>      create table book_copy3 select id, name from book where author='莫言';
>
>    * 仅仅复制某些字段
>
>      create table book_copy4 select id, name from book where 1=2;
>
>* MySQL数据类型
>
>  * 数值型
>
>    * 整型
>
>      * tinyint：1字节
>      * amallint：2字节
>      * mediumint：3字节
>      * int、integer：4字节
>      * bigint：8字节
>
>      特点：
>
>      ①：默认有符号；如果要设置无符号，需要加unsigned关键字。
>
>      ​	    create tab_int(t1 int unsigned);
>
>      ②：若插入的数值超过了临界值，会报out of range异常，并插入临界值。
>
>      ③：如果不设置长度，会有默认长度。长度代表显示的最大宽度，如果不够会		用0在左边填充，但必须搭配zerofill使用。
>
>      ​		create table_int(t1 int(7) zerofill)；#不足7位，补0显示。
>
>    * 小数
>
>      * 浮点型
>        * float(m,d)：4字节
>        * double(m,d)：8字节
>      * 定点型
>        * dec(m,d)：m+2字节
>        * decimal(m,d)：m+2字节
>
>      特点：
>
>      ①：d表示小数点的位数：不足补0，超过四舍五入。
>
>      ②：m表示整数部位+小数部位
>
>      ③：m和d都可以省略。如果是decimal，则m默认为10，d默认为0；如果是		float或doble，则会根据插入的数值的精度来决定精度。
>
>      ④：定点型的精度高，货币可以使用它（但钱都是以分为单位，直接用整型）
>
>  * 字符型
>
>    * 较短的文本：
>
>      * char(m)：最多m个字符数。固定长度的字符，耗费空间。效率高
>      * varchar(m)：最多m个字符数。可变长度字符，节省空间。效率低
>      * 其他：binary,enum,set类型
>
>      特点：char(m)的m可以省略，默认为1；varchar(m)的m不可以省略。
>
>    * 较长的文本：
>
>      * text 
>      * blob：较大的二进制
>
>  * 日期型：
>
>    * date：4，只保存日期
>    * datetime：8，日期+时间，1000-9999
>    * timestamp：4，日期+时间,1970-2038
>    * time：3，只保存时间
>    * year：1，只保存年
>
>* 常见约束
>
>  约束用来对表中的数据进行限制，从而保证数据的准确和可靠性。有六种约束。
>
>  添加约束的时机：创建表时、修改表时。
>
>  create table 表名(
>
>  ​	字段名 字段类型  列级约束,
>
>  ​	字段名 字段类型  列级约束,
>
>  ​    表级约束
>
>  );
>
>  * NOT NULL：非空，用于保证该字段的值不能为空。
>  * DEFAULT：默认，用于保证该字段有默认值。
>  * PRIMARY KEY：主键，用于保证该字段的值唯一且非空。
>  * UNIQUE：唯一，用于保证该字段的值具有唯一性，可以为空。
>  * CHECK：检查约束，mysql不支持。
>  * FOREIGN KEY：外键约束，用于限制两个表的关系，保证该字段的值必须来自主表的                 关联列的值。在从表添加外键约束。现在基本不用了。
>
>  列级约束：
>
>  ​	六大约束语法上都支持，但外键约束没有效果。
>
>  ​	create table major(
>
>  ​		id int primary key,
>
>  ​		majorName varchar(10)
>
>  ​	);
>
>  ​    create table stuinfo(
>
>  ​		id int primary key, #主键
>
>  ​		stuName varchar(20) not null, # 非空
>
>  ​		gender char(1) check(gender='男' or gender='女'), # 检查
>
>  ​		seat int unique,#唯一
>
>  ​		age int default 18, # 默认约束
>
>  ​		majorId int foreign key references major(id) #外键
>
>  ​	);
>
>  表级约束：
>
>  ​	除了非空、默认，其他的4种都支持。
>
>  ​	drop table if exists stuinfo;
>
>  ​	create table major(
>
>  ​		id int primary key,
>
>  ​		majorName varchar(10)
>
>  ​	);
>
>  ​    create table stuinfo(
>
>  ​		id int , 
>
>  ​		stuName varchar(20) , 
>
>  ​		gender char(1), 
>
>  ​		seat int,
>
>  ​		age int  
>
>  ​		majorId int ,
>
>  ​		constraint pk primary key(id), #主键
>
>  ​		constraint uq unique(seat), # 唯一键
>
>  ​		constraint ck check(gender='男' or gender='女'), # 检查
>
>  ​		constranit fk_stuinfo_major foreign key(majorid) references major(id) #外键
>
>  ​	);
>
>  ​	show index from stuinfo;
>
>  主键和唯一的对比
>
>  |      | 保证唯一性 | 是否允许为空 | 一个表中可以有多少个 | 是否允许组合 |
>  | :--: | :--------: | :----------: | :------------------: | :----------: |
>  | 主键 |     √      |      ×       |      至多有1个       | √，但不推荐  |
>  | 唯一 |     √      |      √       |      可以有多个      | √，但不推荐  |
>
>  修改表时添加约束
>
>  ​	语法：
>
>  ​	alter table 表名 modify column 字段名 字段类型 新约束; #列级
>
>  ​	alter table 表名 add 【constraint 约束名】约束类型(字段名) 外键的引用; #表级
>
>  ​	例子：
>
>  ​	  create table stuinfo(
>
>  ​		id int , 
>
>  ​		stuName varchar(20) , 
>
>  ​		gender char(1), 
>
>  ​		seat int,
>
>  ​		age int  
>
>  ​		majorId int
>
>  ​		);
>
>  ​	1.添加非空约束
>
>  ​	alter table stuinfo modify column stuname varcahr(20) not null;
>
>  ​	2.添加默认约束
>
>  ​	alter table stuinfo modify column age int default 18;
>
>  ​	3.添加主键
>
>  ​	alter table stuinfo modify column id int primary key;
>
>  ​	alter table stuinfo add primary key(id);
>
>  ​	4.添加唯一
>
>  ​	alter table stuinfo modify column seat int unique;
>
>  ​	alter table stuinfo add unique(seat);
>
>  ​	5.添加外键
>
>  ​	alter table stuinfo add foreign key(majorid) references major(id);
>
>  修改表时删除约束
>
>  ​	1.删除非空约束
>
>  ​	alter table stuinfo modify column stuname varchar(20) null;
>
>  ​	2.删除默认约束
>
>  ​	alter table stuinfo modify column age int;
>
>  ​	3.删除主键
>
>  ​	alter table stuinfo drop primary key;
>
>  ​	4.删除唯一键
>
>  ​	alter table stuinfo drop index seat; # show index from stuinfo得到键名
>
>  ​	5.删除外键
>
>  ​	alter table stuinfo drop foreign key fk_stuinfo_major;
>
>* 标识列
>
>  又称为自增长列，可以不用手动插入值，系统提供默认的序列值。
>
>  特点：
>
>  ​	标识列是一个key（可以是主键或唯一）。
>
>  ​	一个表最多一个标识列。
>
>  ​	标识列只能是数值类型
>
>  ​	
>
>  例子：
>
>  CREATE TABLE tab_identity(
>  	id INT PRIMARY KEY AUTO_INCREMENT ,
>  	NAME VARCHAR(20)
>  );
>
>  insert into tab_identity values(null, 'john');
>
>  insert into tab_identity values(null, 'jack');
>
>  show variables like '%auto_increment%';
>
>  修改表示设置标识列
>
>  alter table tab_identity modify column id int primary key auto_increment;
>
>  修改表时删除标识列
>
>  alter table tab_identity modify column id int primary key;

