Redis传统五大数据类型的落地应用

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

实现分布式锁的方式有mysq、zookeeper、redis，一般用redis做分布式锁。

redis分布式锁由redisson框架实现。

### base案例（boot+redis)

#### 使用场景

> 多个服务间 + 同一时刻内 + 同一用户只能有一个请求（防止关键业务(如超卖)出现数据冲突和并发错误。）

##### 建modlue

redis_lock01,redis_02

##### 改pom 

##### 写yml

##### 主启动

##### 业务类

```java
@RestController
@Slf4j
public class GoodsController {


    @Autowired
    private StringRedisTemplate redisTemplate;


    @Value("${server.port}")
    private String serverPort;

	// 下单扣库存代码
    @GetMapping("/buyGoods")
    public String buyGoods(){

        String result = redisTemplate.opsForValue().get("goods:001");// get(key) ==> 看看库存够不够
        int goodsNumber = result == null ? 0 : Integer.parseInt(result);

        if (goodsNumber > 0) {
            int realNumber = goodsNumber - 1;
            redisTemplate.opsForValue().set("goods:001", String.valueOf(realNumber));
            log.info(String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber));
            return String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber);
        } else {
            return "商品已经售完.......+ 服务提供端口：" + serverPort;
        }

    }


}
```

### 问题再现(buyGoods方法)

#### 1.单机版没有加锁，并发下出现超卖现象

```java
 	@GetMapping("/buyGoods")
    public String buyGoods() {
		// 加锁
        synchronized (this) {
            String result = redisTemplate.opsForValue().get("goods:001");// get(key) ==> 看看库存够不够
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);

            if (goodsNumber > 0) {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001", String.valueOf(realNumber));
                log.info(String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber));
                return String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber);
            } else {
                return "商品已经售完.......+ 服务提供端口：" + serverPort;
            }

        }
    }
```

#### 2.分布式部署后，单机锁还是出现超卖现象，需要分布式锁

库存重置为100，新建一个服务order80，通过Ribbon负载均衡调用1111和2222的buyGoods接口，然后用jmeter（1秒钟发100次1请求）测试order80，出现超卖（1111和2222同时卖出96、91号商品）现象。

![image-20220104233459817](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220104233459817.png)

![image-20220104233526690](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220104233526690.png)

![image-20220104235237519](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220104235237519.png)

程序3.0加入分布式锁后

```java
@RestController
@Slf4j
public class GoodsController {


    @Autowired
    private StringRedisTemplate redisTemplate;


    @Value("${server.port}")
    private String serverPort;

    private static final String REDIS_LOCK = "mylock";


    @GetMapping("/buyGoods")
    public String buyGoods() {
        String value = UUID.randomUUID().toString() + Thread.currentThread().getName();
        Boolean flag = redisTemplate.opsForValue().setIfAbsent(REDIS_LOCK, value);// 等价于SETNX 
        if (!flag) {
            return "抢锁失败";
        }
        String result = redisTemplate.opsForValue().get("goods:001");// get(key) ==> 看看库存够不够
        int goodsNumber = result == null ? 0 : Integer.parseInt(result);
        if (goodsNumber > 0) {
            int realNumber = goodsNumber - 1;
            redisTemplate.opsForValue().set("goods:001", String.valueOf(realNumber));
            redisTemplate.delete(REDIS_LOCK); // 释放锁
            log.info(String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber));
            return String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber);
        } else {
            return "商品已经售完.......+ 服务提供端口：" + serverPort;
        }
    }
    
}
```

#### 3.如果出现异常，可能无法释放锁。因此必须要在代码层面加finally释放锁

```java
 @GetMapping("/buyGoods")
    public String buyGoods() {
        String value = UUID.randomUUID().toString() + Thread.currentThread().getName();

        try {
            Boolean flag = redisTemplate.opsForValue().setIfAbsent(REDIS_LOCK, value);// 等价于SETNX
            if (!flag) {
                return "抢锁失败";
            }
            String result = redisTemplate.opsForValue().get("goods:001");// get(key) ==> 看看库存够不够
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0) {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001", String.valueOf(realNumber));
                log.info(String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber));
                return String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber);
            } else {
                return "商品已经售完.......+ 服务提供端口：" + serverPort;
            }
        } finally {
            redisTemplate.delete(REDIS_LOCK); // 释放锁
        }

    }
```

#### 4.服务宕机了，代码层面根本没有走到finally块，没法保证解锁，因此需要加入一个过期时间限定key

```java
 @GetMapping("/buyGoods")
    public String buyGoods() {
        String value = UUID.randomUUID().toString() + Thread.currentThread().getName();
        try {
            Boolean flag = redisTemplate.opsForValue().setIfAbsent(REDIS_LOCK, value);// 等价于SETNX
            redisTemplate.expire(REDIS_LOCK, 10L, TimeUnit.SECONDS); // 设置过期时间
            if (!flag) {
                return "抢锁失败";
            }
            String result = redisTemplate.opsForValue().get("goods:001");// get(key) ==> 看看库存够不够
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0) {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001", String.valueOf(realNumber));
                log.info(String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber));
                return String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber);
            } else {
                return "商品已经售完.......+ 服务提供端口：" + serverPort;
            }
        } finally {
            redisTemplate.delete(REDIS_LOCK); // 释放锁
        }

    }
```

#### 5.设置“key+过期时间”分开了，必须要合并成一行使其具备原子性

```java
 @GetMapping("/buyGoods")
    public String buyGoods() {
        String value = UUID.randomUUID().toString() + Thread.currentThread().getName();

        try {
            Boolean flag = redisTemplate.opsForValue().setIfAbsent(REDIS_LOCK, value, 10L, TimeUnit.SECONDS);// 等价于SETNX
            if (!flag) {
                return "抢锁失败";
            }
            String result = redisTemplate.opsForValue().get("goods:001");// get(key) ==> 看看库存够不够
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0) {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001", String.valueOf(realNumber));
                log.info(String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber));
                return String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber);
            } else {
                return "商品已经售完.......+ 服务提供端口：" + serverPort;
            }
        } finally {
            redisTemplate.delete(REDIS_LOCK); // 释放锁
        }

    }
```

#### 6.张冠李戴，删除别人的锁，这是非常严重的bug；因此要保证只能删除自己的锁

举例：A线程进入代码块，发现没有锁，A线程加锁并设置过期时间10秒;第8秒钟，B线程进入，发现有锁，只能等待；第12秒钟，锁自动被Redis删除；第13秒钟，B发现没有锁，所以B加锁成功；第15秒钟，A业务结束，执行解锁操作，就这样把B的锁给删掉了。

```java
 @GetMapping("/buyGoods")
    public String buyGoods() {
        String value = UUID.randomUUID().toString() + Thread.currentThread().getName();

        try {
            Boolean flag = redisTemplate.opsForValue().setIfAbsent(REDIS_LOCK, value, 10L, TimeUnit.SECONDS);// 等价于SETNX
            if (!flag) {
                return "抢锁失败";
            }
            String result = redisTemplate.opsForValue().get("goods:001");// get(key) ==> 看看库存够不够
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0) {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001", String.valueOf(realNumber));
                log.info(String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber));
                return String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber);
            } else {
                return "商品已经售完.......+ 服务提供端口：" + serverPort;
            }
        } finally {
            if (redisTemplate.opsForValue().get(REDIS_LOCK).equalsIgnoreCase(value)) { // 自己只能删自己加的锁
                redisTemplate.delete(REDIS_LOCK); // 释放锁
            }

        }

    }
```

#### 7.finally块的判断+解锁操作不是原子性的。处理方式有两种

##### 用redis自身的事务

```java
 @GetMapping("/buyGoods")
    public String buyGoods() {
        String value = UUID.randomUUID().toString() + Thread.currentThread().getName();

        try {
            Boolean flag = redisTemplate.opsForValue().setIfAbsent(REDIS_LOCK, value, 10L, TimeUnit.SECONDS);// 等价于SETNX
            if (!flag) {
                return "抢锁失败";
            }
            String result = redisTemplate.opsForValue().get("goods:001");// get(key) ==> 看看库存够不够
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0) {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001", String.valueOf(realNumber));
                log.info(String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber));
                return String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber);
            } else {
                return "商品已经售完.......+ 服务提供端口：" + serverPort;
            }
        } finally {
            while (true) {
                redisTemplate.watch(REDIS_LOCK);
                if (redisTemplate.opsForValue().get(REDIS_LOCK).equalsIgnoreCase(value)) {
                    redisTemplate.setEnableTransactionSupport(true); // 开启事务支持
                    redisTemplate.multi();//开启事务
                    redisTemplate.delete(REDIS_LOCK);//删除自己的锁
                    List<Object> list = redisTemplate.exec();//提交事务
                    if (list == null) {
                        continue; // 删除失败，继续
                    }
                }
                redisTemplate.unwatch();
                break; //删除成功，跳出循环
            }

        }

    }
```

##### 用Lua脚本

```java
@GetMapping("/buyGoods")
    public String buyGoods() throws Exception {
        String value = UUID.randomUUID().toString() + Thread.currentThread().getName();

        try {
            Boolean flag = redisTemplate.opsForValue().setIfAbsent(REDIS_LOCK, value, 10L, TimeUnit.SECONDS);// 等价于SETNX
            if (!flag) {
                return "抢锁失败";
            }
            String result = redisTemplate.opsForValue().get("goods:001");// get(key) ==> 看看库存够不够
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0) {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001", String.valueOf(realNumber));
                log.info(String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber));
                return String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber);
            } else {
                return "商品已经售完.......+ 服务提供端口：" + serverPort;
            }
        } finally {
            Jedis jedis = RedisUtils.getJedis();
            String script = "if redis.call(\"get\",KEYS[1]) == ARGV[1]\n" +
                    "then\n" +
                    "    return redis.call(\"del\",KEYS[1])\n" +
                    "else\n" +
                    "    return 0\n" +
                    "end";
            try{
                Object res = jedis.eval(script, Collections.singletonList(REDIS_LOCK), Collections.singletonList(value));
                if ("1".equals(res.toString())) {
                    log.info("---------解锁成功");
                } else {
                    log.info("---------解锁失败");
                }
            }finally {
                if (jedis != null) {
                    jedis.close();
                }
            }
        }

    }
```

#### 8.

#### 确保RedisLock过期时间大于业务执行时间的问题分布式锁如何续期。

#### Redis异步复制造成锁的丢失：主节点没来得及把刚刚set进来的锁同步给从节点，主节点就宕机重启变为从节点，新的主节点有没有这把锁。这一点可以和Zookeeper进行对比。

结论：对于Redis单机版，自己写的RedisLock有效果，但是一但Redis是集群版的，就会出现问题。因此推出了Redisson框架

#### 9.Redisson（Java版的redis分布式锁落地实现）

![image-20220105012037135](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220105012037135.png)

##### 1.加入一个配置 方法

```java
    @Bean
    public Redisson redisson(){
        Config config = new Config();
        config.useSingleServer().setAddress("redis://1.15.72.181:6379").setDatabase(0).setPassword("6623");;
        return (Redisson) Redisson.create(config);
    }
```

```java
 @GetMapping("/buyGoods")
    public String buyGoods() throws Exception {
        String value = UUID.randomUUID().toString() + Thread.currentThread().getName();
        RLock redissonLock = redisson.getLock(REDIS_LOCK);
        redissonLock.lock();
        try {
            String result = redisTemplate.opsForValue().get("goods:001");// get(key) ==> 看看库存够不够
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0) {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001", String.valueOf(realNumber));
                log.info(String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber));
                return String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber);
            } else {
                return "商品已经售完.......+ 服务提供端口：" + serverPort;
            }
        } finally {
            redissonLock.unlock();
        }

    }
```

#####  用jmeter测试发现，没有出现超卖

![image-20220105014751079](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220105014751079.png)

##### 但是在大规模并发的情况下，解锁时可能会出现以下异常

![image-20220105015421678](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220105015421678.png)

##### 修改代码如下

```java
@GetMapping("/buyGoods")
public String buyGoods() throws Exception {
    String value = UUID.randomUUID().toString() + Thread.currentThread().getName();
    RLock redissonLock = redisson.getLock(REDIS_LOCK);
    redissonLock.lock();
    try {
        String result = redisTemplate.opsForValue().get("goods:001");// get(key) ==> 看看库存够不够
        int goodsNumber = result == null ? 0 : Integer.parseInt(result);
        if (goodsNumber > 0) {
            int realNumber = goodsNumber - 1;
            redisTemplate.opsForValue().set("goods:001", String.valueOf(realNumber));
            log.info(String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber));
            return String.format("从【%s端口】成功买到一个商品，库存还剩下%d件", serverPort, realNumber);
        } else {
            return "商品已经售完.......+ 服务提供端口：" + serverPort;
        }
    } finally {
        if (redissonLock.isLocked()) {
            if (redissonLock.isHeldByCurrentThread()) {
                redissonLock.unlock();
            }
        }
    }

}
```



## redis缓存过期淘汰策略

### Redis内存满了怎么办

#### Redis默认内存多少？

> 默认内存在32位系统下，是3G，在64位系统下，不限制。推荐设置最大物理内存的3/4

#### 在哪里看？如何修改？

>在redis.conf看，设置maxmemory参数，maxmemory时bytes字节类型
>
>maxmemory 104857600 # 通过修改redis.confg配置，1024 * 1024 * 100配置100MB内存
>
>config get maxmemory # 查看最大内存
>
>config set maxmemory 1023 * 1024 #通过命令配置
>
>info memory # 查看redis当前内存使用情况

#### 如果Redis内存使用超出了设置得最大值会怎样？

>改改配置，故意把最大值设置为1byte
>
>然后执行set k1 v1，得到OOM

![image-20220105233809497](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220105233809497.png)

#### 结论：

1.尽量不要让redis出现打满

2.redis只存热点高频的数据

3.maxmemory要么用默认配置，要么用最大内存的3/4

### Redis缓存淘汰策略

![image-20220105234408044](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220105234408044.png)

#### 往redis里写的数据到期后是怎么没了的？

##### Key到期后，是不是马上从内存中被删除？

> 不是

##### 那到期后什么时候被删除？是个什么操作？

> Redis有3种删除策略
>
> * 定时删除：到期后立马被删除。这种操作耗费CPU，因为它需要一直监测过期key，并执行删除。（拿CPU时间换Redis的空间）
>
> * 惰性删除：数据到期后不做处理；下次访问时，如果未过期，返回数据；如果已过期，删除数据，返回nil。这种操作对cpu友好，但对内存不友好。（拿空间换CPU时间）
>
> * 定期删除：上面两种方案的折中。每隔一段时间执行一次删除过期键操作，并通过限制限制删除操作执行的时长和频率来减少删除操作对CPU时间的影响。定期删除的难点是确定删除操作的执行时长和频率：如果删除操作太频繁或执行时间太长，定期删除策略就会退化成定时删除策略，出现耗费CPU的情况。如果删除操作执行得太少或执行时间太短，定期删除策略就会退化成惰性删除策略，出现浪费内存的情况。所以要合理地设置删除操作的执行时长和频率。

##### 上述3种过期策略还是会有漏网之鱼，因此Redis提供了8种内存淘汰策略

noeviction：当内存使用超过配置的时候会返回错误，不会驱逐任何键

allkeys-lru：加入键的时候，如果过限，首先通过LRU算法驱逐最久没有使用的键

volatile-lru：加入键的时候如果过限，首先从设置了过期时间的键集合中驱逐最久没有使用的键

allkeys-random：加入键的时候如果过限，从所有key随机删除

volatile-random：加入键的时候如果过限，从过期键的集合中随机驱逐

volatile-ttl：从配置了过期时间的键中驱逐马上就要过期的键

volatile-lfu：从所有配置了过期时间的键中驱逐使用频率最少的键

allkeys-lfu：从所有键中驱逐使用频率最少的键

>上面8种内存淘汰策略是从”两个维度“和”四个方面“组合出来的
>
>两个维度
>
>* 过期键中筛选
>* 所有键中筛选
>
>四个方面
>
>* LRU
>* LFU
>* random
>* ttl

默认用哪种，一般用哪种，以及如何配置内存淘汰策略？

>Redis默认用noeviction，一般用allkeys-lru。配置有修改redis.conf和使用命令两种
>
>* maxmemory-policy allkeys-lru # 修改redis.conf
>
>* config set maxmemory-policy allkeys-lru

## redis的LRU算法

