#### 1.在单机版的基础上，创建集群配置文件

```bash
mkdir /opt/hconfig/redis_cluster
cd /opt/hconfig/redis_cluster
cp /etc/redis.conf  /opt/hconfig/redis_cluster/redis_79.conf
cp redis_79.conf redis_80.conf
cp redis_79.conf redis_81.conf
```

修改redis_80.conf，redis_81.conf的四项配置（加入端口后缀即可）
1、端口			  port 
2、pid 名字       pidfile
3、log文件名字    logfile
4、dump.rdb 名字  dbfilename

#### 2.启动Redis

```bash
redis-server redis_79.conf
redis-server redis_80.conf
redis-server redis_81.conf
```

#### 3.查看启动状态

```bash
ps -ef|grep redis
```

#### 4.使用redis-cli 进行连接测试

```bash
redis-cli -p 6379
```

#### 5.变成一主二从，79为主，80，81为从。默认情况下每台redis都是主节点

只需要对从机配置即可，这是暂时性配置。
配置80

```bash
redis-cli -p 6380
SLAVEOF 127.0.0.1 6379
masterauth FA86D6708231EACC2EC22F0_20190624 # 只有配置了主机密码，才能主从复制
eixt
```

配置81

```bash
redis-cli -p 6381
SLAVEOF 127.0.0.1 6379
masterauth FA86D6708231EACC2EC22F0_20190624 
info replication # 查看
eixt
```

#### 6.哨兵模式配置，它是主从配置的升级，在主节点出问题的时候，会自动选举新的主节点

```bash
vi /opt/hconfig/redis_cluster sentinel.conf
sentinel monitor myredis 127.0.0.1 6380 1
```

#### 7.查看日志

```bash
cat  /var/log/redis/redis_6379.log
```

##### 8.其他

linux查找文件命令：

```bash
find / -name dump.rdb
```