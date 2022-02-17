#### 1.配置MongoDB的yum源

	vim /etc/yum.repos.d/mongodb-org-3.4.repo
	#添加以下内容：
	[mongodb-org-3.4]  
	name=MongoDB Repository  
	baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/  
	gpgcheck=1  
	enabled=1  
	gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
	
	#这里可以修改 gpgcheck=0, 省去gpg验证
	[root@localhost ~]# yum makecache      

#### 2.安装MongoDB

安装命令：

```bash
yum -y install mongodb-org
```

查看mongo安装位置 :

```bash
whereis mongod
```



#### 3.启动MongoDB 

启动mongodb ：

```bash
systemctl start mongod.service
```

停止mongodb ：

```bash
systemctl stop mongod.service
```

查到mongodb的状态：

```bash
systemctl status mongod.service
```

开机启动   

```bash
systemctl enable mongodb.service 
```

#### 4.设置mongodb开机自启动

1.建立脚本

```bash
cd /lib/systemd/system/
vim mongodb.service
```

2.编辑脚本（用whereis mongod,查看mongodb的二进制文件和配置文件的路径，并替换）

```bash
[Unit]

Description=mongodb
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStart=/usr/bin/mongod  --config  /etc/mongod.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/usr/bin/mongod  --shutdown --config  /etc/mongod.conf
PrivateTmp=true 

[Install]
WantedBy=multi-user.target
```

3.设置权限

```bash
chmod 754 mongodb.service 
```

#### 5.创建用户

1.在mongo启动的情况下，命令行输入mongo，进入mongo命令行界面

```bash
mongo
```

2.切换到admin库

```bash
use admin
```

3.创建一个超级用户

```bash
db.createUser(
  {
    user: "root",
    pwd: "6623",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ]
  }
);
```

4.退出mongo Shell界面，重新进入，看用户是否存在

```bash
use admin;
db.auth('root','6623');
```

5.启用身份验证

修改mongodb的配置文件（/etc/mongod.conf）
bindIp改为0.0.0.0这样外网也能访问（默认是127.0.0.1，只能在内网访问）

```bash
vim /etc/mongod.conf

net:
  port: 27017
  bindIp: 0.0.0.0
```

启用身份验证配置

```bash
security:
  authorization: enabled
```

保存文件
重启mongodb服务

```bash
sudo service mongod restart
```

