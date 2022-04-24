### 一，安装elasticsearch

前提条件：已经安装JDK1.8+
1.下载安装文件

```bash
wget https://repo.huaweicloud.com/elasticsearch/7.6.1/elasticsearch-7.6.1-linux-x86_64.tar.gz
```

2.解压文件

```bash
tar -zxvf elasticsearch-7.6.1-linux-x86_64.tar.gz
# 移动并改名为es
mv elasticsearch-7.6.1 /usr/local/es/
```

3.改大jvm内存

```bash
vi /etc/sysctl.conf
vm.max_map_count=262144 # 改大jvm内存
/sbin/sysctl -p # 立即生效
```

4.修改配置

```bash
cd /usr/local/es/
vi /usr/local/es/config/elasticsearch.yml

node.name: master
network.host: 0.0.0.0
http.port: 9200
cluster.initial_master_nodes: ["master"]
```

在elasticsearch.yml文件最后加入配置如下，让head插件能连上。

```yaml
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-methods: OPTIONS, HEAD, GET, POST, PUT, DELETE
http.cors.allow-headers: "X-Requested-With, Content-Type, Content-Length, X-User"
```

5.创建普通用户（因为ES无法用root启动）
a.liunx创建新用户 

```
adduser hezp
```

b.然后给创建的用户加密码 

```bash
passwd hezp  
#输入两次密码。
```

c.root给hezp赋权限，

```bash
chown -R hezp /你的elasticsearch安装目录。
```

5.切换到新用户,并在elasticsearch-7.6.1目录下

```bash
su hezp
./bin/elasticsearch -d  #-d是后台启动 ==============================================>
```

6.访问：

```bash
curl localhost:9200
```

7.关闭es（用stop.sh脚本停止es更优雅）

```bash
ps -ef | grep elastic
kill -9 进程号
```

8.es关闭脚本

在es/bin目录下建立一个stop脚本

```
vim /usr/local/es/bin/stop.sh
```

添加内容

```bash
#!/bin/bash
es_ps=`jps|grep Elasticsearch`
OLD_IFS="$IFS"
IFS=" "
arr=($es_ps)
IFS="$OLD_IFS"
echo '正在停止ElasticSearch进程:' ${arr[0]}...
kill -9 ${arr[0]}
echo '已停止'
```

再给脚本设置启动权限和es用户权限：

```bash
chmod +x stop.sh
chown -R hezp stop.sh
chgrp -R hezp stop.sh
```

执行

```bash
./stop.sh
```



### 二，安装ik分词器插件

1.下载安装文件

```bash
wget https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.6.1/elasticsearch-analysis-ik-7.6.1.zip  # 不要下载源代码，尴尬。。
```

2.把文件解压到elasticsearch安装目录/plugin/ik目录下

```bash
mk dir /plugin/ik
tar -zxvf elasticsearch-analysis-ik-7.6.1.zip
```

3.重新启动es

4.查看插件

```bash
./elasticsearch-plugin list
```

### 三，安装Kibana

1.下载安装文件

```bash
wget https://mirrors.huaweicloud.com/kibana/7.6.1/kibana-7.6.1-linux-x86_64.tar.gz
tar -zxvf kibana-7.6.1-linux-x86_64.tar.gz
```

2.修改 kibana 配置文件

```bash
vi kibana-6.2.4/config/kibana.yml
server.host: "0.0.0.0"
i18n.locale: "zh-CN" # 汉化
```

3.启动 kibana

```bash
cd /opt/kibana-6.2.4/bin/
./kibana  #   #前台启动，接 ctrl + c 停止
./kibana &    #后台启动
```

4.访问

```bash
http://1.15.72.181:5601/app/kibana
fuser -n tcp 5601
kill -9 进程IP #用kill掉这个进程
```



### 四，安装elasticsearch-head插件

1.安装nodejs

```bash
yum install -y nodejs
```

2.验证

```bash
node -v
npm -v
```

3.安装git

```bash
yum install git
```

4.安装head

```bash
git clone git://github.com/mobz/elasticsearch-head.git
cd elasticsearch-head
npm install phantomjs-prebuilt@2.1.16 --ignore-scripts
npm install
npm run start
```

5.打开：http://host:9100

6.设置http://host:9200/   #连接es

### 五，地址

https://repo.huaweicloud.com/elasticsearch/7.6.1/elasticsearch-7.6.1-linux-x86_64.tar.gz
https://mirrors.huaweicloud.com/kibana/7.6.1/kibana-7.6.1-linux-x86_64.tar.gz
https://mirrors.huaweicloud.com/logstash/7.6.1/logstash-7.6.1.tar.gz
https://github.com/medcl/elasticsearch-analysis-ik/archive/refs/tags/v7.6.1.tar.gz