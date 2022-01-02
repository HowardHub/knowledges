# Centos重装MySQL

##### 说明：如果首次安装，请直接从步骤二开始执行

## 一、MySQL卸载

#### 1.执行

`yum remove mysql   `

#### 2.查看当前安装mysql情况

```bash
rpm -qa|grep -i mysql
```

![image-20210429103112198](C:\Users\GTLM\AppData\Roaming\Typora\typora-user-images\image-20210429103112198.png)

#### 3.执行 

##### 用rpm -ev --nodeps把2的结果都删除

```bash
rpm -ev --nodeps mysql-community-release-el7-5.noarch
rpm -ev --nodeps mysql-community-common-5.6.51-2.el7.x86_64
rpm -ev --nodeps mysql-community-libs-5.6.51-2.el7.x86_64
```

#### 4.查找之前老版本mysql的目录、并且删除老版本mysql的文件和库

```bash
find / -name mysql
```

```bash
rm -rf 查到的所有目录
```

#### 5.删除/etc/my.cnf

```bash
rm -rf /etc/my.cnf
```

#### 6.删除mysql日志

```bash
rm -rf /var/log/mysqld.log
```

## 二、MySQL安装

#### 1.安装mysql

```bash
wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm    #下载mysql源
rpm -ivh mysql-community-release-el7-5.noarch.rpm    #安装mysql源
yum install mysql-community-server    #安装mysql
```

#### 2.启动mysql

```bash
systemctl start mysqld      #启动
systemctl enable mysqld     #开机启动
systemctl daemon-reload     #开机启动
```

#### 3.修改登录密码

```bash
grep 'temporary password' /var/log/mysqld.log    //查看临时生成的密码
mysql -uroot -p    //使用临时密码登录
> ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';    //修改密码a

```

## 三、MySQL安全

### 1.安装mysql_secure_installation

```bash
sudo mysql_secure_installation
```

2.

