

### is_deleted作为连接条件和筛选条件的区别

#### 一、数据源

1.user表

![image-20211217162722809](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211217162722809.png)

2.departments表

![image-20211217162856721](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211217162856721.png)

#### 二、作为连接条件

>案例1：只给主表加is_deleted = 0
>
>```mysql
>select t1.id,t1.name, t1.is_deleted as 't1.is_deleted', t2.department_id, t2.department_name,t2.is_deleted as 't2.is_deleted'
>from user  t1
>left join departments t2 on t1.department_id = t2.department_id and  t1.is_deleted = 0 
>order  BY t1.id asc;
>```
>
>![image-20211217163131050](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211217163131050.png)
>
>结果分析：3条记录，因为左连接把t1都查出来了，而在t1.is_deleted = 0的这个条件下，让t1.is_deleted = 1记录的不参与连接。
>		因此t1.is_deleted = 1记录的右边列没有关联数据。
>
>
>
>案例2：只给副表加is_deleted = 0
>
>```mysql
>select t1.id,t1.name, t1.is_deleted as 't1.is_deleted', t2.department_id, t2.department_name,t2.is_deleted as 't2.is_deleted'
>from user  t1
>left join departments t2 on t1.department_id = t2.department_id and  t2.is_deleted = 0 
>order  BY t1.id asc;
>```
>
>![image-20211217163713521](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211217163713521.png)
>
>结果分析：3条记录，因为左连接把t1都查出来了，而在t2.is_deleted = 0的这个条件下，让t2.is_deleted = 1记录的不参与连接。
>		因此t2.is_deleted = 1记录没有出现出现在结果集中。
>
>
>
>案例3：同时给主表和副表加is_deleted = 0
>
>```mysql
>select t1.id,t1.name, t1.is_deleted as 't1.is_deleted', t2.department_id, t2.department_name,t2.is_deleted as 't2.is_deleted'
>from user  t1
>left join departments t2 on t1.department_id = t2.department_id  and  t1.is_deleted = 0  and  t2.is_deleted = 0 
>order  BY t1.id asc;
>```
>
>![image-20211217163819626](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211217163819626.png)
>
>结果分析：3条记录，因为左连接把t1都查出来了，而在' t1.is_deleted = 0  and  t2.is_deleted = 0 '的这个条件下，
>		让t1.is_deleted = 1和t2.is_deleted = 1记录的不参与连接。
>		因此只有t2.is_deleted = 1记录没有出现出现在结果集中。



#### 三、作为筛选条件

>案例1：只给主表加is_deleted = 0
>
>```mysql
>select t1.id,t1.name, t1.is_deleted as 't1.is_deleted', t2.department_id, t2.department_name,t2.is_deleted as 't2.is_deleted'
>from user  t1
>left join departments t2 on t1.department_id = t2.department_id 
>where t1.is_deleted = 0 
>order  BY t1.id asc;
>```
>
>![image-20211217164109594](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211217164109594.png)
>
>结果分析：2条记录，因为左连接把t1都查出来了，而筛选条件t1.is_deleted = 0  把 't1.is_deleted = 1'的过滤掉了。
>	
>
>案例2：只给副表加is_deleted = 0
>
>```mysql
>select t1.id,t1.name, t1.is_deleted as 't1.is_deleted', t2.department_id, t2.department_name,t2.is_deleted as 't2.is_deleted'
>from user  t1
>left join departments t2 on t1.department_id = t2.department_id  
>where  t2.is_deleted = 0 
>order  BY t1.id asc;
>```
>
>![image-20211217164227521](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211217164227521.png)
>
>结果分析：1条记录，把t2.is_deleted = 1的两条记录过滤掉了
>
>
>
>案例3：同时给主表和副表加is_deleted = 0
>
>```mysql
>select t1.id,t1.name, t1.is_deleted as 't1.is_deleted', t2.department_id, t2.department_name,t2.is_deleted as 't2.is_deleted'
>from user  t1
>left join departments t2 on t1.department_id = t2.department_id  
>where t1.is_deleted = 0   and  t2.is_deleted = 0 
>order  BY t1.id asc;
>```
>
>![image-20211217164300346](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211217164300346.png)
>
>结果分析：1条记录，把t1.is_deleted = 1 和 t2.is_deleted = 1的两条记录过滤掉了。



#### 四、总结

##### ①：is_deleted = 0 作为连接条件不会让记录条数减少，记录总数取决于主表和筛选条件，和连接条件没关系。

##### ②：is_deleted = 0 作为连接条件会和副表相关的列值为null。

##### ③：is_deleted = 0 作为筛选条件，会让记录条数减少。



#### 五、如果你也想跑一下

```mysql
-- user表及数据

DROP TABLE IF EXISTS `user`;
CREATE TABLE `user`  (
  `id` int(11) NOT NULL,
  `name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `department_id` int(11) NULL DEFAULT NULL,
  `is_deleted` smallint(1) NOT NULL DEFAULT 0,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

INSERT INTO `user` VALUES (1, '张翠', 1, 1);
INSERT INTO `user` VALUES (2, '李华', 1, 0);
INSERT INTO `user` VALUES (3, '王一', 2, 0);

-- departments表及数据

DROP TABLE IF EXISTS `departments`;
CREATE TABLE `departments`  (
  `department_id` int(11) NOT NULL COMMENT 'id',
  `department_name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '部门名字',
  `is_deleted` smallint(1) NOT NULL DEFAULT 0,
  PRIMARY KEY (`department_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

INSERT INTO `departments` VALUES (1, 'IT', 1);
INSERT INTO `departments` VALUES (2, '人力资源', 0);


```

