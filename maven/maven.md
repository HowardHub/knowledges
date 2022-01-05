### maven的安装和IDEA的maven配置

##### maven的安装和配置

>maven：软件目录
>
>repository：本地仓库目录
>
>settings.xml：maven的配置文件

![image-20220103222914242](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220103222914242.png)

##### maven远程仓库的配置

配置maven的settings.xml

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                          https://maven.apache.org/xsd/settings-1.0.0.xsd">
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
</settings>
```

##### IDEA的maven配置

![image-20220103223059604](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220103223059604.png)





### 聚合工程的构建

tips：

​	父工程配置parent，让所有子工程都能用。

​	父工程的pom.xml写好后，执行install

父工程pom.xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>com.ln</groupId>
    <artifactId>jnything</artifactId>
    <version>1.0-SNAPSHOT</version>

    <packaging>pom</packaging>

    <!-- 统一管理jar包版本 -->
    <properties>

    </properties>

    <repositories>



    </repositories>


    <dependencyManagement>


    </dependencyManagement>

</project>
```



