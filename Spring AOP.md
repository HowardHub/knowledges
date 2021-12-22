## Spring AOP

### 一、AOP的原理

#### 1.概念和功能

> 功能
>
> AOP（Aspect-OrientedProgramming，面向方面编程），它利用一种称为“横切”的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其名为“Aspect”，即方面。所谓“方面”，简单地说，就是将那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可操作性和可维护性。

![344c52da187b56949c7b2288239644b](C:\Users\GTLM\Desktop\344c52da187b56949c7b2288239644b.png)

>使用场景
>
>1. Authentication 权限
>2. Caching 缓存
>3. Context passing 内容传递
>4. Error handling 错误处理
>5. Lazy loading 懒加载
>6. Debugging 调试
>7. logging，tracing，profiling and monitoring 记录跟踪 优化 校准
>8. Performance optimization 性能优化
>9. Persistence 持久化
>10. Resource pooling 资源池
>11. Synchronization 同步
>12. Transactions 事务
>13. Logging 日志
>
>党建项目中使用到的场景有：通用参数校验拦截器（ParamValidationInterceptor），敏感词过滤(SensitiveWordInterceptor)等

#### 2.AOP中组件介绍

![8ead16918c81ac82ebf89836d1223ff](C:\Users\GTLM\Desktop\8ead16918c81ac82ebf89836d1223ff.png)

1、Joinpoint（连接点）：将要在其上进行织入操作的系统执行点。Spring AOP仅支持“方法调用执行”的

​                          Joinpoint。

> 1.方法调用：某个方法被调用时所处的程序执行点。处在调用对象上的执行点。
>
> 2.**方法调用执行：某个方法内部执行开始时点。处在被调用到的方法逻辑执行的时点。**
>
> 3.构造方法执行：某个对象构造方法内部执行的开始时点。
>
> 4.字段设置：对象的某个属性通过setter方法被设置或者直接被设置的时点。
>
> 5.字段获取：某个对象相应属性被访问的时点。可通过getter访问，也可以直接访问。
>
> 6.异常处理执行：在异常抛出后，对应的异常处理逻辑执行的时点。
>
> 7.类初始化：类中某些静态类型或静态代码块的初始化时点。



2、Pointcut（切点）：代表Joinpoint的表述方式。将横切逻辑织入当前系统的过程中，需要参照Pointcut规定的

Joinpoint信息，才可以知道应该往系统的哪些Joinpoint上织入横切逻辑。

> a.Pointcut的三种表述方式：
>
> * 直接指定Joinpoint所在的方法名称
>
> * 正则表达式：通过正则，来归纳表述需要符合某种条件的多组Joinpoint。
>
> * 使用特定的Pointcut表述语言：功能最强大，AspectJ使用这种方式来指定Pointcut。
>
> b.Pointcut运算：Pointcut之间可以进行逻辑运算。它们就相当于一个集合。
>
>    如Pointcut.Males || Pointcut.Females 代表全体学生



3、Advice（通知）：单一横切关注点逻辑的载体，它代表将会织入到Joinpoint的横切逻辑。把Aspect比作OOP

的Class，那么Advice就相当于Class中的Method。

> * Before Advice：在Joinpoint指定位置之前执行的Advice类型。通常不会中断程序执行流程。
>
> 通过它做一些初始化工作。
>
> * After Advice：在相应Joinpoint之后执行的Advice类型，可以分为以下三种
>
> ​       a.After returning Advice：只有当前Joinpoint处执行流程正常完成后，该Advice才会执行。
>
> ​       b.After throwing Advice：只有当期Joinpoint执行过程中抛出异常的情况下，该Advice才会
>
> ​         											执行。
>
> ​       c.After (Finally) Advice：不管Joinpoint处执行流程是正常结束还是抛出异常，该Advice都会
>
> ​		 										执行。
>
> * Around Advice：对附加其上的Joinpoint进行包裹，可以在Joinpont之前和之后都指定相应
>
>   ​                  			的逻辑，甚至中断或者忽略Joinpoint处原来程序流程的执行。



4、Aspect（切面）：是对系统中的横切关注点逻辑进行模块化封装的AOP概念实体。通常，Aspect可以包含多

个Pointcut以及相关Advice定义。Spring AOP最初实体和Aspect相对应，直到引入AspectJ后，可以使

用@AspectJ的注解并结合普通的POJO来声明Aspect。 



5、织入和织入器：只有经过织入过程后，以Aspect模块化的横切关注点才会集成到OOP的系统中，完

成织入过程的东西被称为织入器。

> AspectJ的ajc编译器就是它的织入器；Spring AOP使用一组类来完成织入操作，ProxyFactory是最
>
> 通用的织入器。





### 二、Spring AOP的实现方式

1、动态代理：

在运行期间，为相应的接口动态生成对应的代理对象。可以将横切关注点逻辑封装到动态代理的

InvocationHandler中，然后在系统运行期间，根据横切关注点需要织入的模块位置，将横切逻辑织入到

相应的代理类中。以动态代理类为载体的横切逻辑的优点是所需要织入横切关注点逻辑的模块类都得是

实现相应的接口，因为**动态代理只针对接口开放**。缺点是，在运行期间使用反射，性能较差。Spring

AOP默认采用动态代理。

2、动态字节码增强:

只要class文件符合Java规范，就可以使用CGLIB工具，在程序运行期间，动态构建字节码的class文

件。这样就可以为需要织入横切逻辑的模块类在运行期间，通过该技术，为这些系统模块类生成相应的

子类，而将横切逻辑加到这些子类中，**让程序在执行期间使用的是这些动态生成的子类**，从而达到将横

切逻辑织入系统的目的。使用该技术，即使模块类没有实现相应的接口，也可以对其进行扩展。final类

型的类除外。当动态代理无法使用时，Spring采用CGLIB库动态字节码增强支持来实现AOP。

**采用动态代理和字节码生成技术，它们都是在运行期间为目标对象生成一个代理对象，而将横切逻辑织**

**入到这个代理对象中，系统最终使用的是织入了横切逻辑的代理对象，而不是真正的目标对象。**





### 三、Spring AOP执行步骤

#### 1.创建代理对象

 1）、  @EnableAspectJAutoProxy 开启AOP功能
 2）、  @EnableAspectJAutoProxy 会给容器中注册一个组件 AnnotationAwareAspectJAutoProxyCreator
 3）、  AnnotationAwareAspectJAutoProxyCreator是一个InstantiationAwareBeanPostProcessor类型后置处理器；
 4）、  容器的创建流程：
         1）、registerBeanPostProcessors（）注册后置处理器；创建AnnotationAwareAspectJAutoProxyCreator对象
         2）、finishBeanFactoryInitialization（）初始化剩下的单实例bean
                   1）、创建业务逻辑组件和切面组件
                   2）、AnnotationAwareAspectJAutoProxyCreator拦截组件的创建过程
                   3）、组件创建完之后，判断组件是否需要增强
                            是：切面的通知方法，包装成增强器（Advisor）;给业务逻辑组件创建一个代理对象（cglib）；

关键点代码：exposedObject = initializeBean(beanName, exposedObject, mbd);//  AbstractAutowireCapableBeanFactory :595

#### 2.执行目标方法：

​	1）.创建和注册AnnotationAwareAspectJAutoProxyCreator

​	2）.创建目标对象

​	3）.创建代理对象

​          1）、代理对象执行目标方法
​          2）、CglibAopProxy.intercept()；
​                      1）、得到目标方法的拦截器链（增强器包装成拦截器MethodInterceptor）
​                      2）、利用拦截器的链式机制，依次进入每一个拦截器进行执行；
​                     3）、效果：
​                              正常执行：前置通知-》目标方法-》后置通知-》返回通知
​                              出现异常：前置通知-》目标方法-》后置通知-》异常通知









### 







