### 如何导入Spring源码到IDEA

#### 配置gradle

##### 下载gradle

![image-20220417230255207](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220417230255207.png)

##### 配置环境变量

GRADLE_HOME：D:\gradle\gradle-5.6.1

GRADLE_USER_HOME： F:\.m2\repository   这个是本地仓库目录

然后在path中添加：%GRADLE_HOME%\bin

#### 导入源码

把源码从github的fork到gitee，这样导入起来快好多，然后用gitee的地址导入到IDEA

#### 配置IDEA

* 切换spring版本到5.1.x，因为更高级的版本需要更高版本的gradle和jdk（本次用JDK1.8）

* 在源码的目录/gradle/wrapper/gradle-wrapper.properties修改如下

```properties
distributionUrl=file:///D:/gradle/gradle-5.6.1-all.zip
```

* 修改IDEA的gradle配置

![image-20220417231239091](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220417231239091.png)

* 配置项目的的kotlin版本，全部改为1.3，如果报错

![image-20220417231409283](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220417231409283.png)

* 构建spring-core项目

![image-20220417231139983](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220417231139983.png)

#### 验证是否可以跑项目

随便找一个源码中的测试类，点击运行，如果没有报错，说明成功了。

![image-20220417231658632](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220417231658632.png)

#### 在项目上新建一个自己的模块spring-study

spring-study模块的build.gradle依赖配置如下

```groovy
dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.7.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.7.0'

    compile(project(":spring-beans"))
    compile(project(":spring-context"))
    compile(project(":spring-core"))
    compile(project(":spring-expression"))
}
```

#### 常见问题

##### java: 程序包org.springframework.aop.target不存在

![image-20220418172216023](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220418172216023.png)

target是因为我在Idea的设置中，将名称为target的目录给隐藏了，把target栏删除即可：

![image-20220418172347272](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220418172347272.png)

target包出现了

![image-20220418172748235](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220418172748235.png)

##### Kotlin: Language version 1.1 is no longer supported; please, use version 1.3 or greater.

修改build.gradle文件的kotlin版本，把1.1改为1.5，然后再build即可。

```groovy
	compileKotlin {
		kotlinOptions {
			jvmTarget = "1.8"
			freeCompilerArgs = ["-Xjsr305=strict"]
			apiVersion = "1.1"
			languageVersion = "1.1"
		}
	}
```

改为

```groovy
	compileKotlin {
		kotlinOptions {
			jvmTarget = "1.8"
			freeCompilerArgs = ["-Xjsr305=strict"]
			apiVersion = "1.5"
			languageVersion = "1.5"
		}
	}
```



##### 

#### Spring各个模块的依赖关系

![img](https://img2020.cnblogs.com/blog/572188/202101/572188-20210106093153235-975601605.png)

#### 总结

很多报错都是因为版本号导致的：JDK、Gradle以及Spring，都需要对应的版本。



参考文档：

https://segmentfault.com/a/1190000037451971

