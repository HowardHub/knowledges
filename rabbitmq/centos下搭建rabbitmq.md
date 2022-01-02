### 一.前提条件

弹性云服务器所在安全组添加了如下表所示的安全组规则，具体步骤参见为安全组添加安全组规则。

表1 安全组规则
方向          类型        协议        端口/范围         源地址    
入方向       IPv4         TCP           5672           0.0.0.0/0
入方向       IPv4         TCP          15672           0.0.0.0/0

### 二.操作步骤

1.安装相关依赖包和perl。
a.执行以下命令，安装相关依赖包。

```bash
yum -y install make gcc gcc-c++ m4 ncurses-devel openssl-devel unixODBC-devel
```

b.执行如下命令，安装perl。

```bash
yum install perl
```

2.安装erlang
a.添加erlang存储库到系统

```bash
wget https://packages.erlang-solutions.com/erlang-solutions-2.0-1.noarch.rpm
rpm -Uvh erlang-solutions-2.0-1.noarch.rpm
```

b.在/etc/yum.repos.d/目录下新建一个文件rabbitmq-erlang.repo，
  然后将下面的粘帖进去

```bash
cd /etc/yum.repos.d/
vi rabbitmq-erlang.repo
```

```bash
[erlang-solutions]
name=CentOS $releasever - $basearch - Erlang Solutions
baseurl=https://packages.erlang-solutions.com/rpm/centos/$releasever/$basearch
gpgcheck=1
gpgkey=https://packages.erlang-solutions.com/rpm/erlang_solutions.asc
enabled=1
```


c.执行以下命令安装erlang

```bash
sudo yum install erlang
```

d.执行如下命令，检查安装结果。

```bash
erl -version
```

3.安装RabbitMQ
a.执行如下命令，进入用户主目录。

  cd

b.执行如下命令，下载RabbitMQ安装包。
打开Rabbit官网。
点击“Get Started”。
找到并单击“Download+Installation”。
根据云服务器的操作系统选择下载地址（选中后右键，然后复制地址加上,到主机上用wget下载）。

c.执行以下命令安装RabbitMQ安装包。

```bash
yum install rabbitmq-server-3.8.12-1.el7.noarch.rpm # 看你下载得是哪个版本。
```

d.安装完毕，启动RabbMQ

```bash
service rabbitmq-server start
```

e.查看RabbMQ状态。

```bash
service rabbitmq-server status
```

4.执行如下命令，启用RabbitMQ的web管理界面。

```bash
rabbitmq-plugins enable rabbitmq_management
```

5.执行如下命令，创建一个新用户。

```bash
rabbitmqctl add_user 用户名 密码
```

命令示例：
rabbitmqctl add_user root 123456

6.执行如下命令，设置用户为管理员。

```bash
rabbitmqctl set_user_tags 用户名 administrator
```

命令示例：
rabbitmqctl set_user_tags root administrator

7.执行如下命令，赋予用户所有权限。

```bash
rabbitmqctl set_permissions -p / 用户名 '.*' '.*' '.*'
```

命令示例：
rabbitmqctl set_permissions -p / root '.*' '.*' '.*'

8.执行如下命令，在后台启动RabbitMQ。

```bash
rabbitmq-server -detached
```

使用浏览器访问 “http://弹性公网IP:15672”，显示如下页面，说明RabbitMQ安装成功。

### 三.其他命令

启动rabbitmq服务 : 

```bash
systemctl start rabbitmq-server
```

停止rabbitmq服务：

```bash
systemctl stop rabbitmq-server
```

重启rabbitmq服务：

```bash
systemctl restart rabbitmq-server
```

自启rabbitmq服务 : 

```bash
systemctl enable rabbitmq-server
```

自启web界面：

```bash
rabbitmq-plugins enable rabbitmq_management
```

### 四.命令行批量删除

首先定位到 rabbitMQ 安装目录的sbin 目录下。打开cmd窗口。

关闭应用的命令为： 

```bash
rabbitmqctl stop_app
```

清除的命令为：

```bash
 rabbitmqctl reset
```

重新启动命令为： 

```bash
rabbitmqctl start_app
```

查看所有队列命令：

```bash
rabbitmqctl list_queues
```

### 五.其他

https://support.huaweicloud.com/bestpractice-ecs/zh-cn_topic_0143982115.html