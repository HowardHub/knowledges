# SpringCloud

## 微服务架构理论



## 微服务架构编码构建

### 父工程构建

#### 微服务cloud整体聚合工程

##### 1.new project

![image-20211230144650921](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211230144650921.png)

##### 2.父工程的名字

![image-20211230145022058](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211230145022058.png)

##### 3.maven版本选择

![image-20211230145534488](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211230145534488.png)

##### 4.字符编码

![image-20211230145813800](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211230145813800.png)

##### 5.注解生效激活

![image-20211230145924329](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211230145924329.png)

##### 6.java编译版本选8

![image-20211230150006986](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211230150006986.png)

##### 7.File Type过滤

![image-20211230150250128](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211230150250128.png)

##### 8.maven跳过单元测试

![image-20211230150831952](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211230150831952.png)

#### 父工程POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.ln</groupId>
    <artifactId>springcloud</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <!--统一管理jar包版本-->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <log4j.version>1.2.17</log4j.version>
        <lombok.version>1.16.18</lombok.version>
        <mysql.version>8.0.19</mysql.version>
        <druid.version>1.1.16</druid.version>
        <spring.boot.version>2.2.2.RELEASE</spring.boot.version>
        <spring.cloud.version>Hoxton.SR1</spring.cloud.version>
        <spring.cloud.alibaba.version>2.1.0.RELEASE</spring.cloud.alibaba.version>
        <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
    </properties>

    <!--子模块继承后,提供作用:锁定版本+子module不用groupId和version-->
    <dependencyManagement>
        <dependencies>
            <!--springboot 2.2.2-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring.boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--Spring cloud Hoxton.SR1-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring.cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--Spring cloud alibaba 2.1.0.RELEASE-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring.cloud.alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <!--第三方maven私服-->
    <repositories>
        <repository>
            <id>nexus-aliyun</id>
            <name>Nexus aliyun</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

</project>
```

#### 发布父工程

父工程创建完成执行mvn:insall将父工程发布到仓库方便子工程继承。

### REST子工程构建

#### 1.建module

![image-20211230152239465](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20211230152239465.png)

#### 2.改POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springcloud</artifactId>
        <groupId>com.ln</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>user_center</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>

        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>

    </dependencies>

</project>
```

#### 3.写YML 

#### 4.主启动

#### 5.业务类

### common模块

可以把各个模块通用的类（如Result，BaseVo）抽到该模块，然后再maven install供其他模块使用。



## Eureka服务注册与发现





## Ribbon负载均衡服务调用



## OpenFeign服务接口调用



## Hystrix断路器



## Zuul路由网关



## Gateway新一代网关



## SpringCloud Config分布式配置中心



## SpringCloud Bus消息总线



## SpringCloud Stream消息驱动





## SpringCloud Sleuth分布式请求链路跟踪