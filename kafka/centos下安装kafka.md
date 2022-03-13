

1.下载kafka安装包

```bash
wget https://downloads.apache.org/kafka/2.8.1/kafka_2.13-2.8.1.tgz
# 如果版本不存在，请访问 https://www.apache.org/dist/kafka/ 查找某个合适的版本
```

2.然后解压存档文件：

```bash
tar xzf kafka_2.13-2.8.1.tgz
mv kafka_2.13-2.8.1 /usr/local/kafka
```

3.设置 Kafka Systemd 单元文件

3.1.创建 `systemd` Zookeeper 的单元文件

```bash
nano /etc/systemd/system/zookeeper.service
```

添加以下内容：

```bash
[Unit]
Description=Apache Zookeeper server
Documentation=https://zookeeper.apache.org
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
ExecStart=/usr/local/kafka/bin/zookeeper-server-start.sh /usr/local/kafka/config/zookeeper.properties
ExecStop=/usr/local/kafka/bin/zookeeper-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```

3.2.创建一个 Kafka `systemd` 的单元文件

```bash
nano /etc/systemd/system/kafka.service
```

添加以下内容：

```bash
[Unit]
Description=Apache Kafka Server
Documentation=https://kafka.apache.org/documentation.html
Requires=zookeeper.service

[Service]
Type=simple
Environment="JAVA_HOME=/usr/lib/jvm/jre-11-openjdk"
ExecStart=/usr/local/kafka/bin/kafka-server-start.sh /usr/local/kafka/config/server.properties
ExecStop=/usr/local/kafka/bin/kafka-server-stop.sh

[Install]
WantedBy=multi-user.target
```

3.3.重新加载 `systemd` 应用更改的守护进程：

```bash
systemctl daemon-reload
```

4.启动 Kafka 服务器

```bash
#启动zookeeper
sudo systemctl start zookeeper
#启动kafka
sudo systemctl start kafka
#查看kafka的状态
sudo systemctl status kafka

#暂停kafka
sudo systemctl stop kafka

#暂停zookeeper
sudo systemctl stop zookeeper
```

5.创建主题 Apache 卡夫卡

“生产者”是负责将数据放入我们的 Kafka 的过程。 Kafka 带有一个命令行客户端，它将从文件或标准输入中获取输入，并将其作为消息发送到 Kafka 集群：

```bash
#可以通过运行与下述相同的命令来创建多个主题
cd /usr/local/kafka
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic testTopic

Created topic testTopic.
```

在 Kafka 上查看创建的主题

```bash
bin/kafka-topics.sh --list --zookeeper localhost:2181

#结果如下
testTopic
KafkaonCentOS8
KafkaonCentOS8
```

6.运行生产者，然后在控制台中输入一些消息以发送到服务器：

```bash
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic testTopic
Welcome to kafka
Hello Kafka
```

7.运行消费者，用于从 Kafka 集群读取数据并将消息显示到标准输出：

```bash
cd /usr/local/kafka
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic testTopic --from-beginning

Welcome to kafka
Hello Kafka
```













