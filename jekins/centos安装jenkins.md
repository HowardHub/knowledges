#### 1、安装JDK

```bash
yum install -y java # 有的话就不要装了
```

#### 2、安装jenkins

添加Jenkins库到yum库，Jenkins将从这里下载安装。

```bash
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
yum install -y jenkins  # 如果报错提示没有证书，则执行 yum install jenkins --nogpgcheck
```

如果不能安装就到官网下载jenkis的rmp包，官网地址（http://pkg.jenkins-ci.org/redhat-stable/）

```bash
wget http://pkg.jenkins-ci.org/redhat-stable/jenkins-2.7.3-1.1.noarch.rpm
rpm -ivh jenkins-2.7.3-1.1.noarch.rpm
```

配置jenkis的端口

```bash
vi /etc/sysconfig/jenkins
```

找到修改端口号：

```bash
JENKINS_PORT="8080"  此端口不冲突可以不修改 
```

#### 3、jenkins命令

```bash
service jenkins start   #启动
service jenkins stop    #关闭
service jenkins restart #重启
```

安装成功后Jenkins将作为一个守护进程随系统启动
系统会创建一个“jenkins”用户来允许这个服务，如果改变服务所有者，
同时需要修改/var/log/jenkins, /var/lib/jenkins, 和/var/cache/jenkins的所有者
启动的时候将从/etc/sysconfig/jenkins获取配置参数
默认情况下，Jenkins运行在8080端口，在浏览器中直接访问该端进行服务配置
Jenkins的RPM仓库配置被加到/etc/yum.repos.d/jenkins.repo

#### 4、打开jenkins 

在浏览器中访问 
首次进入会要求输入初始密码如下图， 

```bash
cat /var/lib/jenkins/secrets/initialAdminPassword # 获取初始密码
97286d7c2d774debbdc63523e429fc75
```

选择“Install suggested plugins”安装默认的插件，下面Jenkins就会自己去下载相关的插件进行安装。 

创建超级管理员账号 



jenkins基础操作教程
https://www.jianshu.com/p/5f671aca2b5a