1.安装之前先检查一下系统有没有自带open-jdk

```bash
rpm -qa |grep java;
rpm -qa |grep jdk;
rpm -qa |grep gcj;
```


如果没有输出信息表示没有安装。

如果安装，则执行下面命令卸载

```bash
rpm -qa | grep java | xargs rpm -e --nodeps  #批量卸载所有带有Java的文件  这句命令的关键字是java
```

2.首先检索包含java的列表

```bash
yum list java*
```

3.检索1.8的列表

```bash
yum list java-1.8*   
```

4.安装1.8.0的所有文件

```bash
yum install java-1.8.0-openjdk* -y
```

5.使用命令检查是否安装成功

```bash
java -version
```

到此安装结束了。这样安装有一个好处就是不需要对path进行设置，自动就设置好了