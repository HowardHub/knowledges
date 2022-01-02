#### 一、安装redis 

```bash
yum install redis #出现选择就一直 y
```

#### 二、启动redis服务 

```bash
/bin/systemctl  start  redis.service
```

#### 三、测试redis

```bash
redis-cli
set  'test'  'hello'
get  'test'
```

#### 四、修改配置

  1.找配置文件 

```bash
whereis  redis.config  
```

  2.打开配置文件  

```bash
vi /etc/redis.conf  
```

>允许远程访问(需要注释掉所有的bind!!!)
>#bind 127.0.0.1
>端口号
>port 6379
>设置密码
>requirepass FA86D6708231EACC2EC22F0_20190624
>关闭保护
>protected-mode no
>ESC  :wq  保存
>systemctl restart redis.service 

#### 五.设置开机启动

```bash
systemctl daemon-reload
systemctl start redis.service
systemctl enable redis.service
```

#### 六.服务操作命令

```bash
systemctl start redis.service   #启动redis服务
systemctl stop redis.service   #停止redis服务
systemctl restart redis.service   #重新启动服务
systemctl status redis.service   #查看服务当前状态
systemctl enable redis.service   #设置开机自启动
systemctl disable redis.service   #停止开机自启动 
```

#### 七.卸载redis

1.关闭redis服务
2.删除redis文件相关目录

```bash
rm -rf /usr/local/redis/bin/redis*
rm -rf /etc/redis.conf  
```

```bash
yum remove redis
```

