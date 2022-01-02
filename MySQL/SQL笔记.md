#### 一、查询

##### 1.聚集函数

定义：运行在行组上，计算和返回单个值的函数。

AVG() ：返回某列的平均值

COUNT() ：返回某列的行数，count(列名)忽略null值，count(*)不会

MAX() ：返回某列的最大值

MIN() ：返回某列的最小值

SUM() ：返回某列值之和

说明：出了count(*)，不会忽略null值外，其他的函数都会忽略null值

样例：

SELECT AVG(prod_price) as avg_price FROM products;

SELECT AVG(DISTINCT prod_price) as avg_price FROM products; 默认为ALL, 可以用DISTINCT 

##### 2.分组

分组是在SELECT语句的GROUP BY子句中建立的。

分组允许把数据分为多个逻辑组，以便能对每个组进行聚集计算。

SELECT vend_id, COUNT(*)  AS num_prods 

FROM products

GROUP BY vend_id;

###### 规定：

1.GROUP BY子句可以包含任意数目的列。这使得能对分组进行嵌套，为数据分组提供更细致的控制。

2**.除聚集计算语句外，SELECT语句中的每个列都必须在GROUP BY子句中给出**。

3.如果分组列中具有NULL值，则NULL将作为一个分组返回。如果列中有多行NULL值，它们将分为一组。

###### 过滤分组

规定保留哪些分组，排除哪些分组。

目前为止所有类型的WHERE子句都可以用HAVING来替代。唯一的差别是WHERE过滤行，而HAVING过滤分组。

#### 3.子查询

嵌套在其他查询中的查询

##### 目的：

###### 1.利用子查询进行过滤

SELECT cust_name, cust_contact

FROM customers

WHERE cust_id IN (SELECT cust_id

​                                  FROM orders

​                                  WHERE order_num IN SELECT order_num

​                                                                          FROM orderitems

​                                                                          WHERE prod_id ='TNT2' ));

###### 2.作为计算字段使用子查询

SELECT cust_name, 

​			  cust_state,

​			  (SELECT COUNT(*)

 			  FROM orders

​			   WHERE orders.cust_id = customers.cust_id) AS orders

FROM customers;

相关子查询：涉及外部查询的子查询。任何时候只要列名可能有多义性，就必须使用这种语法（表名和列名由一个句点分隔）。否则会因为歧义导致出错。

###### 3.注意：

在WHERE子句中使用子查询（如这里所示），应该保证SELECT语句具有与WHERE子句中相同数目的列。



#### 4.联结表

联结(join)是利用SQL的SELECT能执行的最重要的操作。

##### 1.关系表

关系表的设计就是要保证把信息分解成多个表，一类数据一个表。各表通过某些常用的值（即关系设计中的关系（relational））互

相关联。

外键（foreign key）： 外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系。

作用：联结是一种机制，用来在一条SELECT语句中关联表，因此称之为联结。使用特殊的语法，可以联结多个表返回一组输出，联结在运行时关联表中正确的行。

创建联结：规定要联结的所有表以及它们如何关联即可。

SELECT vend_name, prod_name, prod_price

FROM vendors, products

WHERE vendors.vend_id = products.vend_id;

**在联结两个表时，你实际上做的是将第一个表中的每一行与第二个表中的每一行配对。WHERE子句作为过滤条件，它只包含那些匹配给定条件（这里是联结条件）的行。**

笛卡儿积（cartesian product）: 由没有联结条件的表关系返回的结果为笛卡儿积。检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。

##### 2.内部联结

基于两个表之间的相等测试，也成为等值联结。

对于这种联结可以使用稍微不同的语法来明确指定联结的类型。(推荐INNER JOIN语法，因为可以保证联结条件在ON后面)

SELECT vend_name, prod_name, prod_price

FROM vendors INNER JOIN products ON vendors.vend_id = products.vend_id

#### 5.高级联结

##### 1.不同类型的联结：自联结、自然联结、外部联结

###### 1.1 自联结

SELECT p1.prod_id, p1.prod_name

FROM products AS p1, products AS p2

WHERE p1.vend_id = p2.vend_id AND p2.prod_id = 'DTNTR';

###### 1.2自然联结

自然联结是这样一种联结，其中你只能选择那些唯一的**列**。这一般是通过对表使用通配符（SELECT *），对所有其他表的列使用明确的子集来完成的。自然联结排除多次出现，使每个列只返回一次。

SELECT c.*, o.order_num, o.order_date, oi.prod_id, oi.quantity, oi.item_price

FROM customers AS c, orders AS o, orderitems AS oi

WHERE c.cust_id = o.cust_id AND oi.order_num = o.order_num AND prod_id = 'FB';

迄今为止我们建立的每个内部联结都是自然联结，很可能我们永远都不会用到不是自然联结的内部联结。

###### 1.3外部联结

许多联结将一个表中的行与另一个表中的行相关联。但有时候会需要包含没有关联行的那些行。

联结包含了那些在相关表中没有关联行的行。这种类型的联结称为外部联结。

SELECT customers.cust_id, orders.order_num

FROM customers LEFT OUTER JOIN ordders ON customers.cust_id = orders_cust_id;

使用LEFT OUTER JOIN从FROM子句的左边表（customers表）中选择所有行。

在使用OUTER JOIN语法时，必须使用RIGHT或LEFT关键字指定包括其所有行的表（RIGHT指出的是OUTER JOIN右边的表，而LEFT指出的是OUTER JOIN左边的表）

##### 2.使用带聚集函数的联结

-- 检索所有客户及每个客户所下的订单数

SELECT customers.cust_name, customers.cust_id, COUNT(orders.order_num) AS num_ord

FROM customers INNER JOIN orders ON customers.cust_id = orders.cust_id

GROUP BY customers.cust_id;

