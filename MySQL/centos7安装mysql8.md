### 一.安装

1.下载并添加存储库

```bash
sudo yum localinstall https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm
```

2.安装MySQL 8.0包
与其他使用yum的软件包一样安装MySQL：

```bash
yum module disable mysql
yum install mysql-community-server --nogpgcheck  #禁用gpg检测，防止报错
```

### 二.启动MySQL

安装完成后，启用并启动MySQL服务类型：

```bash
sudo systemctl enable mysqld
sudo systemctl start mysqld
```

我们可以通过键入以下命令来检查MySQL服务状态

```bash
sudo systemctl status mysqld
```

### 三.MySQL安全

1.当第一次启动MySQL服务器时，为MySQL根用户生成一个临时密码。 您可以通过运行以下命令找到密码：

```bash
sudo grep 'temporary password' /var/log/mysqld.log
```

输出应该看起来像这样：
2018-05-26T23:50:09.270656Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: 0iZPQZkEfY*W
记下密码0iZPQZkEfY*W，因为下一个命令会要求您输入临时的root密码。

2.运行mysql_secure_installation命令来提高MySQL安装的安全性：

```bash
sudo mysql_secure_installation
```

输入xwEPP-Fd2zcf临时密码后，系统会要求您为root用户设置新密码。 密码必须至少包含8个字符并且至少包含一个大写字母，
一个小写字母，一个数字和一个特殊字符。Hezhipeng6623.
输出示例：
The existing password for the user account root has expired. Please set a new password.

New password:

Re-enter new password:

该脚本还会要求您删除匿名用户，限制root用户对本地计算机的访问权限并删除测试数据库。 你应该对所有问题回答“y”（是）。

### 四.开机自启动。

编辑文件/etc/rc.local

```bash
vim /etc/rc.local
```

```bash
service mysqld start # 添加一行
```

### 五.用户权限相关
1.登录Mysql

2.建立新用户

```bash
mysql-> create user 'hzp'@'%' identified by '6623.*aA';
```

3.给新用户分权限

```bash
mysql> grant all privileges on *.* to 'hzp'@'%' with grant option;
```

### 六.其他

参考文献：https://www.cnblogs.com/jie1521/p/10286604.html
参考文献：https://www.linuxidc.com/Linux/2018-05/152574.htm

卸载mysql:
https://www.jianshu.com/p/a5f7642879a8
