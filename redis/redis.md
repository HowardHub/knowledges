## Redis传统五大数据类型的落地应用

经典五大类型

string,list,hash,set,zset(sorted set)

其他类型

bitmap,hyperLogLog,GEO,Stream

tips：redis命令不区分大小写，但key区分

### String

#### 常用命令

##### 最常用

>set key value
>
>get  key

##### 同时设置/获取多个键值

>mset key v1 [key value ...]
>
>mget key [key...]

##### 数值增减

##### 递增数字

>INCR key

##### 增加指定的整数

> INCR key increment

##### 递减整数

> DECR key

##### 减少指定的整数

> DECRBY key decrement

##### 获取字符串长度

> strlen key

#### 分布式锁

>setnx key value # 不存在就创建
>
>set key value [EX seconds] [PX milliseconds] [NX|XX]
>
>说明：
>
>EX:key在多少秒之后过期
>
>PX:key在多少毫秒之后过期
>
>NX:当key不存在的时候，才创建key,效果等同于setnx
>
>XX:当key存在的时候，覆盖key
>
>例子
>
>set lock pay ex 10 NX
>
>set lock order ex 10 NX

#### 应用场景

##### 商品编号、订单号采用INCR命令生成

> INCR  item-id # 执行一次，id加1

##### 用户是否喜欢的文章

> 阅读数：只要点击了rest地址，直接使用incr key命令增加一个数字1，完成记录数字



### hash

redis hash 对应 java Map<String, Map<Object,Object>>

#### 常用命令

##### 一次设置一个字段值

> HSET key field value

##### 一次获取一个字段值

> HGET key field

##### 一次设置多个字段值

> HMSET key field value [field value ...]

##### 一次获取多个字段值

> HMGET key field [field ...]

##### 获取所有字段值

> hgetall key

##### 获取某个key内的全部数量

> hlen key

##### 删除一个key

> hdel key

#### 应用场景

> 购物车早期
>
> 新增商品： hset shopcar:uid6623 334477 1
>
> 新增商品： hset shopcar:uid6623 334488 1
>
> 增加商品数量： hincrby shopcar:uid6623 334477 1
>
> 商品总数：hlen shopcar:uid6623
>
> 全部选择：hgetall shopcar:uid6623

![image-20220103154027968](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220103154027968.png)



### list

里面的元素可以重复

#### 常用命令

##### 向列表左边添加元素

> LPUSH key value [value ...]

##### 向列表右边添加元素

> RPUSH key value [value ...]

##### 查看列表

> LRANGE key start stop

##### 获取列表中元素的个数

> LLEN key

#### 使用场景

> 微信文章订阅公众号
>
> 1.大V作者李永乐和CSDN发布了文章分别是11和22
>
> 2.我关注了他们两个，只要他们发布了新文章，就会安装进我的List
>
> ​	lpush liearticle:我的id 11 22
>
> 3.查看我自己订阅的全部文章，并分页
>
> ​	lrange likearticle:我的id 0 10



### Set

里面的元素是唯一，且无序的。

#### 常用命令

##### 添加元素

> SADD key member [member ...]

##### 删除元素

>SREM key member [member ...]

##### 获取集合中所有元素

> SMEMBERS key

##### 判断元素是否在集合中

> SISMEMBER key member

##### 获取集合中的元素个数

> SCARD key

##### 从集合中随机弹出若干个元素，元素不删除

> SRANDMEMBER key [数字]

##### 从集合中随机弹出若干个元素，元素删除

> STOP key [数字]

##### 集合运算

###### 差集A-B

> SDIFF key [key ...]

###### 交集A∩B

> SINTER key [key ...]

###### 并集A∪B

> SUNION key [key ...]

#### 应用场景

##### 微信小程序抽奖

| 1.用户点击立即参与按钮             | sadd key 用户id                                              |
| ---------------------------------- | ------------------------------------------------------------ |
| 2.显示已经有多少人参与了           | scard key                                                    |
| 3.抽奖（从set中任意选取N个中奖人） | srandmember key 2 #随机抽2个人，元素不删除<br />spop key 3 #随机抽奖3个人，元素会删除（抽到后就不能再参与了） |

##### 微信朋友圈点赞

| 1.新增点赞                               | sadd pub:msgID 点赞用户ID1 点赞用户ID2 |
| ---------------------------------------- | -------------------------------------- |
| 2.取消点赞                               | srem pub:msgID 点赞用户ID              |
| 3.展现所有点赞过的用户                   | smembers pub:mgsID                     |
| 4.点赞用户数统计，就是常见的点赞红色数字 | scard pub:msgID                        |
| 5.判断某个朋友是否对楼主点赞过           | sismember pub:msgID 用户ID             |

##### 微博好友关注社交关系

###### 共同关注的人

> SINTER s1 s2

我关注的人也关注了他

> sismember s2 3 #我关注了3号，那么S2是否也关注了3号

##### QQ内推可能认识的人

> SDIFF s1 s2 #把s1的朋友推荐给s2



### zset

向有序集合中加入一个元素和该元素的分数

#### 常用命令

##### 添加元素

> ZADD key score member [socre member ...]

##### 按照元素分数从小到大的顺序返回索引从start到stop之间的所有元素

> ZRANGE key start stop [withscores ]

##### 获取元素的分数

> ZSCORE key member 

##### 删除元素

> ZREM key member [member ...]

##### 获取指定分数范围的元素

> ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]

##### 增加某个元素的分数

> ZINCRBY key increment member 

##### 获取集合中元素的数量

> ZCARD key

##### 获取指定分数范围内的元素个数

> ZCOUNT key min max

##### 获取元素排名

>ZRANK key member #从小到大
>
>ZREVRANK key member #从大到小

#### 应用场景

##### 根据商品销售对商品进行排序显示

| 商品编号1001的销量是9，商品编号1002的销量是15 | zadd goods:sellsort 9 1001 15 1002    |
| --------------------------------------------- | ------------------------------------- |
| 有一个客户又买了2件商品1001                   | zincrby goods:sellsort 2 1001         |
| 求商品销量前10                                | zrange goods:sellsort 0 10 withscores |

##### 抖音热搜

| 点击视频         | zincrby hotvcr:20220103 1 西安疫情<br />zincrby hotvcr:20220103 100 西安疫情  2 三只松鼠 |
| ---------------- | ------------------------------------------------------------ |
| 展示当日排行前10 | zrevrange hotvcr:20220103 0 9 withsocres                     |



## Redis分布式锁













## redis缓存过期淘汰策略





## redis的LRU算法