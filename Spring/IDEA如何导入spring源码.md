### 如何导入Spring5.1.x源码到IDEA

#### 源码的下载和处理：

##### 下载并解压

```txt
https://gitee.com/wmbyy/spring-framework-source/repository/archive/5.1.x
```

##### 修改根目录下的build.gradle

由

```xml
	repositories {
		mavenCentral()
		maven { url "https://repo.spring.io/libs-spring-framework-build" }
	}
```

改为

```groovy
	repositories {
		// mavenCentral()
		// maven { url "https://repo.spring.io/libs-spring-framework-build" }
		mavenLocal()
		maven { url 'https://maven.aliyun.com/repository/public/' }
        maven { url 'https://maven.aliyun.com/repository/spring/' }
	}
```

##### 查看gradle版本号（这个非常重要）

在gradle\wrapper\gradle-wrapper.properties文件，得到4.10.3的版本号

```properties
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-4.10.3-bin.zip
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
```

#### 配置gradle

##### 下载gradle，然后解压

```
去这个网址搜索指定gradle版本：https://gradle.org/releases/
```

##### 配置环境变量

GRADLE_HOME：D:\gradle\gradle-4.10.3

GRADLE_USER_HOME： F:\.m2\repository   这个是本地仓库目录

然后在path中添加：%GRADLE_HOME%\bin

##### 验证gradle是否安装正确

```cmd
gradle -v
```

#### 编译源码

##### 先复制一份gradle源码到gradle\wrapper目录

这样在使用gradlew进行预编译时，就不用再去下载执行文件了。

![image-20220430153532151](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220430153532151.png)

##### 修改gradle\wrapper\gradle-wrapper.properties文件

```properties
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=gradle-4.10.3-bin.zip
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
```

##### 配置“docs.gradle”文件

该文件位于**“spring-framework-5.1.x\gradle**”下

![image-20220430164916481](F:\assets\image-20220430164916481.png)

注释掉“dokka”以及“asciidoctor”内容，

![image-20220430165153989](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220430165153989.png)

这一步是去除编译过程中关于JavaDoc文档的构建，在Spring源码编译过程中，JavaDoc文档编译非常的消耗时间。

##### 进入源码根目录，并执行下列命令

```cmd
gradlew.bat cleanIdea :spring-oxm:compileTestJava
```

#### 导入并配置IDEA

* 修改IDEA的gradle配置

![image-20220428211854741](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220428211854741.png)

#### 新建spring-study模块

spring-study模块的build.gradle依赖配置如下

```groovy
dependencies {

    compile(project(":spring-context"))
    
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.7.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.7.0'
}
```

##### 确保setting.gradle包含了spring-study

```groovy
include 'spring-study'
```

##### 跳过测试

添加 -x testClasses -x test -x javadoc -x compileTestKotlin -x checkstyleMain

这样可以加快build的速度

![image-20220501160002951](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220501160002951.png)

##### 重新构建，要确保构建成功

![image-20220430151927083](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220430151927083.png)

##### 刷新整个项目

![image-20220430152018859](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220430152018859.png)

#### 常见问题（踩过的坑）

**大部分都是因为gradle版本不是spring中指定的版本**

##### java: 程序包org.springframework.aop.target不存在

![image-20220418172216023](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220418172216023.png)

target是因为我在Idea的设置中，将名称为target的目录给隐藏了，把target栏删除即可：

![image-20220418172347272](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220418172347272.png)

target包出现了

![image-20220418172748235](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220418172748235.png)

##### Kotlin: Language version 1.1 is no longer supported; please, use version 1.3 or greater.

![image-20220428211744633](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220428211744633.png)



##### 在checkstyleMain阶段无法通过编译

```txt
Execution failed for task ':spring-debug:checkstyleMain'.
> Checkstyle rule violations were found. See the report at: file:///D:/study/spring6.0M3/spring-framework/spring-debug/build/reports/checkstyle/main.html
  Checkstyle files with violations: 1
  Checkstyle violations by severity: [error:5]

* Try:
> Run with --stacktrace option to get the stack trace.
> Run with --info or --debug option to get more log output.
> Run with --scan to get full insights.
```

出现这个错，一般是我们没有按照Spring自己的代码规范进行编码，根据他给的网址`file:///D:/study/spring6.0M3/spring-framework/spring-debug/build/reports/checkstyle/main.html`，查看对应的行的错误修改就行了，本身也是中文提示，一般都是什么开头没注释、空格只能用tab之类的问题。

##### 查看spring-study是否引入了它依赖的jar包

![image-20220428203301495](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220428203301495.png)

重新build整个工程，然后在刷新整个项目

##### 构建dokka失败

![image-20220430163128800](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220430163128800.png)

删除：F:\.m2\repository\caches\modules-2\files-2.1\org.jetbrains.kotlin\ 文件夹，重新构建

#### Spring各个模块的依赖关系

![img](https://img2020.cnblogs.com/blog/572188/202101/572188-20210106093153235-975601605.png)

#### 总结

很多报错都是因为版本号导致的：JDK、Gradle以及Spring，都需要对应的版本。



参考文档：

https://segmentfault.com/a/1190000037451971

