### centos下搭建Nexus仓库

#### 一、安装Nexus

前提：安装JDK

1.安装maven

```bash
yum install maven
```

2.下载安装包

```txt
官网：https://help.sonatype.com/repomanager3/product-information/download
官网下载慢，请直接用百度网盘
链接：https://pan.baidu.com/s/1kVhE7RMVAYoWZQvcV64YgA 
提取码：wvel
```

```bash
wget https://download.sonatype.com/nexus/3/nexus-3.37.3-02-unix.tar.gz
tar -zxvf nexus-3.4.0-02-unix.tar.gz
```

3.启动nexus

```bash
cd nexus-3.4.0-02/bin/
./nexus start
```

4.访问nexus

```txt
http://124.223.54.117:8081/
默认admin/admin123
```



#### 二、将项目发布到Nexus

1.修改maven的setting文件：添加servers、profiles和activeProfiles节点的配置

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                          https://maven.apache.org/xsd/settings-1.0.0.xsd">
						  
	<servers>    
      <server>
        <id>ln-releases</id>
        <username>admin</username>
        <password>admin123</password>
      </server>
      <server>
          <id>ln-snapshots</id>
          <username>admin</username>
          <password>admin123</password>
      </server>   
	</servers>					  
						  
    <mirrors>

        <mirror>
            <id>central</id>
            <name>aliyun maven</name>
            <url>https://maven.aliyun.com/repository/central</url>
            <mirrorOf>central</mirrorOf>
        </mirror>
        <mirror>
            <id>jcenter</id>
            <name>aliyun jcenter</name>
            <url>https://maven.aliyun.com/repository/public</url>
            <mirrorOf>jcenter</mirrorOf>
        </mirror>
        <mirror>
            <id>public</id>
            <name>aliyun public</name>
            <url>https://maven.aliyun.com/repository/public</url>
            <mirrorOf>public</mirrorOf>
        </mirror>
        <mirror>
            <id>google</id>
            <name>aliyun google</name>
            <url>https://maven.aliyun.com/repository/google</url>
            <mirrorOf>google</mirrorOf>
        </mirror>
        <mirror>
            <id>gradle-plugin</id>
            <name>aliyun gradle plugin</name>
            <url>https://maven.aliyun.com/repository/gradle-plugin</url>
            <mirrorOf>gradle-plugin</mirrorOf>
        </mirror>
        <mirror>
            <id>spring</id>
            <name>aliyun spring</name>
            <url>https://maven.aliyun.com/repository/spring</url>
            <mirrorOf>spring</mirrorOf>
        </mirror>
        <mirror>
            <id>spring-plugin</id>
            <name>aliyun spring-plugin</name>
            <url>https://maven.aliyun.com/repository/spring-plugin</url>
            <mirrorOf>spring-plugin</mirrorOf>
        </mirror>
        <mirror>
            <id>grails-core</id>
            <name>aliyun grails-core</name>
            <url>https://maven.aliyun.com/repository/grails-core</url>
            <mirrorOf>grails-core</mirrorOf>
        </mirror>
        <mirror>
            <id>apache-snapshots</id>
            <name>aliyun apache-snapshots</name>
            <url>https://maven.aliyun.com/repository/apache-snapshots</url>
            <mirrorOf>apache-snapshots</mirrorOf>
        </mirror>

    </mirrors>
	
	
	<profiles> 
		<profile>
			<id>ln-private-repo</id>
			<repositories>
				<repository>
					<id>ln-releases</id>
					<url>http://124.223.54.117:8081/repository/maven-releases/</url>
					<releases>
						<enabled>true</enabled>
					</releases>
					<snapshots>
						<enabled>false</enabled>
					</snapshots>
				</repository>
				<repository>
					<id>ln-snapshots</id>
					<url>http://124.223.54.117:8081/repository/maven-snapshots/</url>
					<releases>
						<enabled>false</enabled>
					</releases>
					<snapshots>
						<enabled>true</enabled>
						<updatePolicy>always</updatePolicy>
					</snapshots>
				</repository>
			</repositories>
			</profile>
	</profiles>  
	
	<activeProfiles>
		<activeProfile>ln-private-repo</activeProfile>
	</activeProfiles>
</settings>

```

2.修改项目中的pom.xml：distributionManagement元素是project的子元素

```xml
    <distributionManagement>
        <!--pom.xml这里<id> 和 settings.xml 配置 <id> 对应  -->
        <repository>
            <id>ln-releases</id>
            <url>http://124.223.54.117:8081/repository/maven-releases/</url>
        </repository>
        <snapshotRepository>
            <id>ln-snapshots</id>
            <url>http://124.223.54.117:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>

```

3.点击发布deploy，**每次deploy都需要改版version，不然会失败**

![image-20220220004302769](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220004302769.png)



4.去页面查看

![image-20220220004337663](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220004337663.png)

5.其他项目引入该jar

```xml
        <dependency>
            <groupId>java8_paly</groupId>
            <artifactId>com.ln</artifactId>
            <version>1.0</version>
        </dependency>
```

参考文档：

nexus的安装教程：https://segmentfault.com/a/1190000040220525

maven的setting.xml元素解释： https://www.cnblogs.com/hongmoshui/p/10762272.html