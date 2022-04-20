### 1.部分主键重复

2022年4月13日

##### 表结构

```mysql
CREATE TABLE `user` (
  `id` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '主键',
  `name` varchar(255) NOT NULL,
  PRIMARY KEY (`id`(2)) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3;
```

##### 纪录数

![image-20220413171204249](F:\assets\image-20220413171204249.png)

##### 执行语句

```mysql
INSERT INTO `db1`.`user`(`id`, `name`) VALUES (126789, '张三');
```

##### 出现的问题

![image-20220413171611420](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220413171611420.png)

##### 原因分析

主键重复？123456不等于1236789的，但是提示上说12，所以看看索引的长度。

![image-20220413171839846](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220413171839846.png)

这里的索引长度为2，导致插入的时候索引重复，所以插入失败。

##### 解决方法

将键长度设置为和主键一样的长度，即32即可。

##### 小结

索引的长度是可以自己设置的。