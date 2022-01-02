## Spring的循环依赖

循环依赖：A类中有个引用B,B类中有个引用A。这就构成了一个简单的循环依赖。

### 一、为什么Spring不支持构造方法形式的循环依赖？

先看代码：这是一段构造方法形式的循环依赖示例。（用BeanFactory容器debug更简单）。

```java
@Configuration
public class ConsCircularRefConfig {

    @Bean
    @Lazy
    public A a() {
        return new A(b());
    }

    @Bean
    @Lazy
    public B b() {
        return new B(a());
    }

    public static void main(String[] args) {

        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(ConsCircularRefConfig.class);
        A a = applicationContext.getBean(A.class);

    }

}

class A {
    private B b;
    public A(B b) {
        this.b = b;
    }
}

class B {
    private A a;

    public B(A a) {
        this.a = a;
    }
}

```

#### 执行的时序图

![Spring循环依赖-构造方法注入的循环依赖](F:\download\Spring循环依赖-构造方法注入的循环依赖.png)

#### 主流程：

1.getBean(A.class)，把A的beanName加入到singletonsCurrentlyInCreation，添加成功；

2.发现A构造参数依赖B，去getBean("b")，把B的beanName加入到singletonsCurrentlyInCreation，添加成功；

3.发现B构造参数依赖A，去getBean("a")，尝试把A的beanName加入到singletonsCurrentlyInCreation，

 添加失败，因为singletonsCurrentlyInCreation中已经有A的beanName。抛出异常，提示存在循环依赖。



### 二、为什么Spring支持setter方法形式的循环依赖？

先看代码：这是一段setter循环依赖的代码示例。（用BeanFactory容器debug更简单）

```java
@Configuration
public class SetterCircularRefConfig {

    @Bean
    @Lazy
    public A a() {
        return new A();
    }

    @Bean
    @Lazy
    public B b() {
        return new B();
    }

    public static void main(String[] args) {
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SetterCircularRefConfig.class);
        A a = applicationContext.getBean(A.class);

    }


}

class A {
    private B b;

    public void setB(B b) {
        this.b = b;
    }
}

class B {
    private A a;
    public void setA(A a) {
        this.a = a;
    }
}
```



#### 执行的时序图

![Spring循环依赖-setter方法注入的循环依赖](F:\download\Spring循环依赖-setter方法注入的循环依赖.png)



#### 说明：

1.当a已经被实例化了即执行完了instantiateClass(A)后，继续执行后面的方法，
  发现 earlySingletonExposure 为true，在doCreateBean内调用addSingletonFactory方法。
  addSingletonFactory执行前
  singletonFactories{a},earlySingletonObjects{},registeredSingletons{a}
  addSingletonFactory执行后
  singletonFactories{a},earlySingletonObjects{},registeredSingletons{a}

2.装配a,执行populateBean->applyPropertyValues->resolveValueIfNecessary->resolveReference，
  发现依赖b没有,执行bean=this.beanFactory.getBean('b');此时b并没有被实例化。

3.实例化b,doGetBean->getSingleton('b')不进行循环依赖处理->getSingleton('b',singletonFactory)->
   createBean('b')->doCreateBean(),b实例化成功->
   earlySingletonExposure为true，执行addSingletonFactory:
   addSingletonFactory执行前
   singletonFactories{a},earlySingletonObjects{},registeredSingletons{a}
   addSingletonFactory执行后
   singletonFactories{a,b},earlySingletonObjects{},registeredSingletons{a,b}

4.装配b，执行populateBeanpopulateBean->applyPropertyValues->resolveValueIfNecessary->resolveReference，
  发现需要依赖a,调用this.beanFactory.getBean('a');->doGetBean('a')
  ->getSingleton('a')从缓存中获取a,singletonFactories包含{a},缓存命中。执行循环依赖处理。
  getSingleton执行前
  singletonFactories{a,b},earlySingletonObjects{},registeredSingletons{a,b}
  getSingleton执行后
  singletonFactories{b},earlySingletonObjects{a},registeredSingletons{a,b}

5.此时已经获取到a的实例，继续执行4,把a注入b内，b这样就装配好了，此时步骤4执行完了。
  然后再执行addSingleton('b',singletonObject)处理循环依赖。
  addSingleton执行前
  singletonFactories{b},earlySingletonObjects{a},registeredSingletons{a,b}
  addSingleton执行后
  singletonFactories{},earlySingletonObjects{a},registeredSingletons{a,b}

6.把b返回给调用方,继续执行2，把b注入a内，这样a就装配好了，此时步骤2执行完了
  然后调用getSingleton('a',false)，因为a已经初始化，所以allowEarlyReference为false，
  所以下面三个集合不变化
  getSingleton执行后
  singletonFactories{},earlySingletonObjects{a},registeredSingletons{a,b}

7.此时a已经装配完了，newSingleton为true，对a进行注册再执行addSingleton('a', singletonObject);
  addSingleton执行前
  singletonFactories{},earlySingletonObjects{a},registeredSingletons{a,b}
  addSingleton执行后
  singletonFactories{},earlySingletonObjects{},registeredSingletons{a,b}

8.最后对a进行包装，并把它返回给main函数，setter依赖注入结束。

#### 主流程

1.getBean(A.class)，执行 红线的逻辑，实例化a,装配a时,发现a依赖b;

2.执行getBean("b")，执行红线逻辑，并且执行绿线逻辑,实例化b,装配b,时,发现依赖a;

3.执行getBean("a")，执行到第一个绿线getSingleton()的时候，有a，直接返回,

4.初始化b,执行setA(a)

5.初始化a，执行setB(b)

6.把a返回给main

注意：

1.AbstractBeanFactory.doGetBean()中调用了两种类型的getSingleton()

  先调用getSingleton(beanName)从缓存中取，没有取到，再调用getSingleton(beanName, singletonFactory)。

2.还是有一个AbstractAutowireCapableBeanFactory.getSingleton(beanName,false)在    AbstractAutowireCapableBeanFactory.doCreateBean中调用。

### 三、总结

通过debug，可以追踪循环依赖，其中构造方法依赖比较容易分析，不会涉及三级缓存。而setter方法的循环依赖，因为涉及到三级缓存，所以需要细致的分析  singletonFactories, earlySingletonObjects, registeredSingletons这三个缓存在执行时候的变化，因为它们是解决setter循环依赖的工具。文章中没有帖代码出来，是希望大家能够自己动手debug，跟踪一下这两种循环依赖的方法调用和容器状态的情况，以便更加理解Spring处理循环依赖的方法。最后，如有纰漏，欢迎大家及时指正。

