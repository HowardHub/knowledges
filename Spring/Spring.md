# Spring

## 一、Spring的架构

![image-20210725202416741](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210725202416741.png)

### 1.IoC

### 2.AOP

### 3.JDBC

### 4.tx

### 5.ORM

### 6.MVC





## 二、Spring IoC

### 1.IoC的定义

将依赖的对象的生成交给其他IoC容器负责。是一种可以帮助我们解耦各业务对象间依赖关系的对象绑定方式。

![image-20210725223208976](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210725223208976.png)

### 2.依赖注入的三种方式

#### 1.构造方法注入

被注入对象可以通过在其构造方法中声明以来对象的参数列表，让外部对象知道它需要哪些依赖对象。对象构造完后，即可被使用；但如果参数较长，维护比较麻烦，而且构造方法无法继承，无法设置默认值。

#### 2.setter方法注入

被注入对象为其所依赖的对象提供setter方法，就可以通过setter方法将相应的依赖对象设置到被注入对象中。可以被继承，允许设置默认值。

#### 3.接口注入（耦合重，已废弃）

被注入对象实现某个接口，而该接口中声明一个注入方法，该方法的参数列表即为被依赖对象列表。

### 3.IoC容器的职责

#### 1.业务对象的构建管理

#### 2.业务对象的依赖绑定

##### 2.1依赖关系的管理方式

1. 直接编码方式：在容器启动前，可以通过编码的方式将被注入对象和依赖对象注册到容器中，并明确它们相互之间的依赖注入关系。
2. 配置文件方式：最常见的是通过XML文件来管理对象注册和对象间依赖关系，Spring采用XML文件来管理和保存依赖注入的信息。
3. 元数据（注解）方式：在类中使用注解信息来标注各个对象之间的依赖关系。它是编码方式的一种特殊情况。

### 4.两种Spring容器

![image-20210725235556283](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210725235556283.png)

#### 1.BeanFactory

> 它是基础类型的IoC容器，提供完整的IoC服务支持。如果没有特殊指定，默认采用延迟初始化策略。

![image-20210726204942653](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210726204942653.png)

> BeanFactory只是定义了如何访问容器内管理的Bean的方法，各个BeanFatory的具体实现类负责具体Bean的注册以及管理工作。DefaultListabBeanFactory是BeanFactory一个比较通用的实现类，它还实现了BeanDefinitionRegistry接口，该接口是在BeanFactory的实现中担当Bean注册管理的角色。BeanDenifitionRegistry接口定义抽象了Bean的注册逻辑。通常情况下，具体的BeanFactory实现类会实现这个接口来管理Bean的注册。

##### 1.1外部加载Bean

> 采用外部配置文件时，Spring有一个统一的处理方式。一般需要针对某种类型的配置文件，给出相应的BeanDefinitionReader实现类，由它负责将相应的配置文件内容读取并映射到BeanDefinition，然后将映射后的BeanDefinition注册到一个BeanDefinitionRegistry，之后，BeanDefinitionRegistry即完成Bean的注册和加载。BeanDefinitionReader的工作包括解析文件格式、装配BeanDefinition，而BeanDefinitionRegistry只不过负责保管而已。

##### 1.2注解方式

> 使用@Autowired以及@Component对相关类进行标记，Spring就会到指定的包下扫描标注有@Component的类，然后将它们添加到容器进行管理，并根据它们所标注的@Autowired为这些类注入符合条件的依赖对象。
>
> Spring除了可以通过配置明确指定bean之间的依赖关系，还可以根据bean定义的某些特点将相互依赖的某些bean直接自动绑定，这就是bean标签的autowire属性，他有5个值no,byName,byType,constructor和autodetect。
>
> ```java
> public class User{
>     private Car car;
>     public void setCar(Car car) {
>         this.car = car;
>     }
> }
> ```
>
> 没有指定User和Car的关系，但是使用autowire="byName"可以实现自动绑定。
>
> <bean id="user" class="...User" autowire="byName"/>
>
> <bean id="car" class="...Car"/>

#### 1.3 Bean的scope

> BeanFactory还有一个职责就是对象的声明周期管理，scope用来声明容器的对象所应该处的限定场景或者说该对象的存活时间，即容器在对象进入其相应的scope之前，生成并装配这些对象，在该对象不再处于这些scope的限定之后，容器通常会销毁这些对象。在xml配置中scope为bean标签的属性，由AutowireCapableBeanFactory接口提供支持。

##### 1.3.1 scope的取值

> singleton、prototype、request、session和global session，后三种只能在web容器（ApplicationContext）中使用。
>
> singleton：容器中只存在一个实例，所有对该对象的引用将共享这个实例。该实例从容器启动，并因为第一次请求而被初始化后，将一直存活到容器退出。这是scope的默认值。
>
> prototype：容器在接到该类型的请求时，会每次都重新生成一个新的对象实例给请求方。只要对象实例返回给请求方之后，容器就不再拥有当前返回对象的引用。
>
> request：容器为每个请求都创建一个全新的RequestProcessor对象供当前请求使用，时prototype的一种特例。
>
> session：容器为每个独立的session创建一个UserPreference对象，它比request拥有更长的存活时间，其他方面没差别。
>
> global session：只用在基于portlet的Web中。

##### 1.3.2 scope的陷阱

> ```java
> public class Car {
> 
>     private String carNum;
>     
>     public void setCarNum(String carNum) {
>         this.carNum = carNum;
>     }
> 
>     public String getCarNum() {
>         return carNum;
>     }
> }
> 
> public class User {
>     
>     private Car car;
>     
>     public void setCar(Car car) {
>         this.car = car;
>     }
>     
> 
>     public Car getCar() {
>         return car;
>     }
>     
>     public void printCarAdd(){
>         System.out.println("car的地址是："+ getCar());
>     }
> 
> }
> 
> XML的配置
>     <bean id="user" class="ch4.User">
>         <property name="car" ref="car"/>
>     </bean>
>     <bean id="car" class="ch4.Car" scope="prototype"/>
> // 测试代码
> public class ScopePlay {
> 
>     public static void main(String[] args){
>         BeanFactory container = new XmlBeanFactory(new ClassPathResource("scope.xml"));
>         User user1 = container.getBean("user", User.class);
>         User user2 = container.getBean("user", User.class);
>         user1.printCarAdd();
>         user2.printCarAdd();
>     }
> }
> 输出结果：
> car的地址是：ch4.Car@6f96c77
> car的地址是：ch4.Car@6f96c77
> 
> ```
>
> 问题：XML中的Car的scope已经配置为prototype类型，为啥输出的两个car是一样的？
>
> Why：问题出在了实例取得的方式上面。虽然Car拥有prototype类型的scope，但是当容器将第一个Car的实例注入到User后，User就会一直持有这个Car实例的引用。虽然每次输出都调用了getCar()方法并返回了Car实例，但实际上每次返回的都是User持有的容器第一次注入的实例。即第一个实例注入后，User再也没有重新向容器申请新的实例，所以容器也不会重新为其注入新的Car类型的实例。
>
> Sol：两种方式，方法注入和实现BeanFactoryAware接口
>
> 1.方法注入
>
> 该方法必须能够被子类覆盖，因为容器会为我们要进行方法注入的对象(user)使用Cglib动态生成一个子类实现，从而替代当前对象，getCar()符合要求。接下里修改XML配置
>
> ```xml
> <bean id="user" class="ch4.User">
>     <lookup-method name="getCar" bean="car"/>
> </bean>
> // 输出结果
> car的地址是：ch4.Car@2ef5e5e3
> car的地址是：ch4.Car@36d4b5c
> ```
>
> 2.实现BeanFactoryAware接口
>
> 即使没有方法注入，只要在实现getCar()方法的时候，能保证每次调用BeanFactory的getBean("car")，就同样可以每次都取得新的Car对象实例，因此可以让User拥有一个BeanFactory引用。
>
> 实现BeanFactoryAware接口，容器在实现了该接口的bean定义的过程中，会自动将容器本身注入该bean，这样bean就持有了BeanFactory的引用。
>
> ```java
> public class User implements BeanFactoryAware {
> 
>     private BeanFactory beanFactory;
> 
>     private Car car;
> 
>     public void setCar(Car car) {
>         this.car = car;
>     }
> 
>     public Car getCar() {
>         return (Car) beanFactory.getBean("car");
>     }
> 
>     public void printCarAdd(){
>         System.out.println("car的地址是："+ getCar());
>     }
> 
>     @Override
>     public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
>         this.beanFactory = beanFactory;
>     }
> }
> // 输出
> car的地址是：ch4.Car@3ac3fd8b
> car的地址是：ch4.Car@5594a1b5
> ```

#### 1.4 工厂方法与FactoryBean

> 虽然对象可以通过声明接口来避免对特定接口实现类的过度耦合，但总归需要一种方法将声明依赖接口的对象与接口实现类关联起来。否则，只依赖一个不做任何事情的接口是没有任何用处的。
>
> ```java
> public class Foo {
>     private BarInterface barInstance;
>     public Foo(){
>         // 应该避免这样做。
>         this.barInstance = new BarInterfaceImpl();
>     }
> }
> ```
>
> 如果BarInterfaceImpl是我们自己开发的，那我们可以通过依赖注入，让容器帮助我们解除接口和实现类之间的耦合性。但是如果我们需要实例化第三方库中的相关类，此时接口和实现类的耦合性需要用其他方式来避免。
>
> 通过使用工厂方法模式，提供一个工厂类来实例化具体的接口实现类，这样主体对象只需要依赖工厂类，具体使用的实现类有变更的话，只是变更工厂类，而主体对象不需要做任何改动。
>
> ```java
> public class Foo {
>     private BarInterface barInstance;
>     public Foo(){
>         this.barInstance = BarInterfaceFactory.getInstance();
>         // 或者
>         this.barInstance = new BarInterfaceFactory().getInstance();
>     }
> }
> ```
>
> 针对使用工厂模式实例化对象的方式，Spring的IoC同样提供了支持。我们需要做的，只是将工厂类返回的具体的接口实现类注入给主体对象。Spring对静态工厂方法和非静态工厂方法都提供了支持。
>
> FactoryBean：是容器提供的一种可以扩展容器对象实例化逻辑的接口。它本身是一个bean，只不过这种类型的Bean本身就是生产对象的工厂。当某些对象的实例化过程过于繁琐，通过XML配置过于复杂，使得我们宁愿用Java代码来完成实例化过程，或者，某些第三方库不能直接注册到Spring容器的时候，就可以实现FactoryBean接口，给出自己的对象实例化逻辑代码。当然，通过自定义一个工厂方法类，也可以达到目的。
>
> 实现FactoryBean接口，并配置到XML中，通过getBean(beanName)方法获取的对象就是它生产的对象，而不是工厂本身，要获取FactoryBean本身，需要getBean(&beanName)的方法。

#### 1.5容器运作流程

![image-20210728214120844](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210728214120844.png)

> 总体分为两个阶段：容器启动阶段和Bean实例化阶段。在每个阶段都加入了相应的容器扩展点，以便根据具体的场景需要加入自定义扩展逻辑。
>
> 1.容器启动阶段：
>
> 首先通过某种途径加载Configuration MetaData（通常为XLM格式的配置信息），除了代码方式比较直接，在大部分情况下，容器需要依赖某些工具类（BeanDefinitionReader）对加载的配置文件进行解析和分析，并将分析后的信息编组为相应的BeanDefinition，最后把这些保存了bean定义必要信息的BeanDefinition，注册到相应的BeanDefinitionRegistry，这样容器启动就完成了。这个阶段侧重于对象管理信息的收集。
>
> 2.Bean实例化阶段
>
> 经过第一阶段，现在所有bean定义休息都通过BeanDefinition的方式注册到了BeanfinitionRegistry中。当某个请求方通过容器的getBean方法明确地请求某个对象，或者因依赖关系容器需要隐式地调用getBean方法时，就会触发第二阶段。
>
> 该阶段，容器会首先检查所有请求的对象之前是否已经初始化。如果没有，则会根据注册的BeanDefinition所提供的信息实例化被请求对象，并为其注入依赖。如果该对象实现了某些回调接口，也会根据回调接口的要求去装配它。当该对象装配完毕后，容器会立即将其返回请求方使用。

##### 1.5.1插手容器的启动

> Spring提供了一种叫做BeanFactoryPostProcessor的容器扩展机制。该机制允许我们在容器实例化相应对象之前，对注册到容器的BeanDefiniton所保存的信息做相应的修改。相当于在容器实现的第一阶段最后加入一道工序，让我们对最终的BeanDefinition做一些额外的操作，比如修改其中bean定义的某些属性或为bean定义增加其他信息等。
>
> 因为一个容器可能拥有多个BeanFactoryPostProcessor，这时可能需要实现类同时实现org.springframework.core.Ordered接口，以保证各个BeanFactoryPostProcessor可以按照预先设定的顺序执行。
>
> 1.PropertyPlaceholderConfigurer
>
> 它允许我们在配置文件中使用占位符并将这些占位符所代表的资源单独配置到简单的properties文件中来加载。这样就可以把数据库连接信息等单独配置到一个properteis文件中，如果资源变动的化，只需要关注这些简单的properties配置文件即可。避免因改动繁杂的XML配置文件而出现问题。
>
> 当BeanFactory在第一阶段加载完所有配置时，BeanFactory中保存的对象的属性信息还只是以占位符的形式存在，如${jdbc.url},${jdbc.driver}。当PropertyPlaceholderConfigure作为BeanFactoryPostProcessor被应用时，它会使用properties配置文件中的配置信息来替换相应BeanDefinition中占位符所表示的属性值。
>
> Spring3之后，使用PropertySourcesPlaceholderConfigurer代替PropertyPlaceholderConfigurer
>
> 2.PropertyOverrideConfigure
>
> 通过它对容器中配置的任何bean的定义的property信息进行覆盖替换。即如果系统提供的默认值不合适（server.port默认是8080），就可以通过PropertyOverrideConfigure在相应的properties文件中做出相应的配置，修改这个默认值。
>
> 3.CustomEditorConfigure
>
> 它将后期会用到的信息注册到容器，对BeanDefinition没有任何变动。因为XML配置的都是String，要完成String到具体对象的转换，都需要提供相应的转换规则，CustomEditorConfigure就用来配置这个规则。Spring内部通过PropertyEditor来进行String类型到其他类型的转换工作。只要为每种类型提供一个PropertyEditor，就可以达到转换的目的。
>
> Spring内置的PropertyEditor
>
> StringArrayPropertyEditor：将CVS格式的字符串转为String[]
>
> ClassEditor：根据String类型的class名称，直接将其转为相应的Class对象，类似Class.forName(string)
>
> FileEditor：和InputStreamEditor、URLEditor，都是对资源进行定位
>
> 自定义PropertyEditor：需要继承PropertyEditorSupport类，里面的方法setAsText表示从String到某个类型的转换，getAsText表示从这个类型到String的转换。然后在自定义一个类，实现PropertyEditorRegistrar接口，这个自定义的PropertyEditorRegistrar是为了把自定义PropertyEditor注册到容器中去。
>
> 

##### 1.5.2bean的实例化阶段

> getBean()方法可以在容器内部被隐式地调用。
>
> 对于BeanFactory来讲，对象默认采用延迟初始化。当A依赖B，getBean(A)时，就会首先在容器内部隐式调用getBean(B)。
>
> 对于ApplicationContext，它启动之后就会实例化所有的bean定义。它在容器启动阶段完成后，就会调用注册到该容器的所有bean定义的实例化方法getBean()。实现在它的refresh()方法内。

第二次调用getBean()则会直接返回容器的第一次实例化完的对象实例（prototype类型的bean除外）。当getBean()方法内部发现该bean定义之前没被实例化后，会通过createBean()方法来进行具体的对象实例化。该方法在org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory中实现。

![image-20210729083558882](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210729083558882.png)

> 1.Bean的实例化与BeanWrapper
>
> 容器在内部实现的时候，采用策略模式来决定采用何种方式初始化bean实例。可以通过反射或者CGLIB动态字节码来初始化相应的bean实例或动态生成其子类。
>
> SimpleInstantiationStrategy实现了简单的对象实例化功能，可以通过反射来实例化对象，但不支持方法注入的方式实例化对象。CglibSubclassingInstantiationStrategy可以通过反射实例化对象，还可以通过CGLIB的动态字节码生成功能，该策略实现类可以动态生成某个类的子类，进而满足了方法注入所需的对象实例化需求，它也是Spring默认采用的。
>
> 容器从BeanDefinition中取得实例化信息，然后结合CglibSubclassingInstantiationStrategy以及不用的bean定义类型，就可以返回实例化完成的对象实例。但它返回的实际上是以BeanWrapper对构造完成的对象实例进行包裹的相应的BeanWrapper实例。
>
> BeanWrapper对bean进行包裹，然后就可以对bean进行操作，比如设置或获取bean的相应的属性值。
>
> BeanWrapper间接继承了PropertyEditorRegistry和TypeConverter接口。Spring在构造BeanWrapperImpl实例时，将之前CustomEditorConfigure注册的PropertyEditor复制一份给这个实例。从而让BeanWrapper有了类型转换、设置对象属性的能力。
>
> 2.Aware接口
>
> 当对象实例化完成并且相关属性以及依赖设置完后，容器会检查当前对象实例是否实现了以Aware命名结尾的接口定义。如果是，则将这些Aware接口定义中规定的依赖注入给当前实例。
>
> - BeanNameAware：如果bean实现了该接口，容器会将该bean定义对应的beanName设置到当前对象实例。
> - BeanClassLoaderAware：会将对应加载当前bean的Classloader注入当前bean。
> - BeanFactoryAware：会将容器自身设置到当前bean。
>
> 对于ApplicationContext类型的容器，有以下几个Aware
>
> - ResourceLoaderAware：ApplicationContext实现了ResourceLoader接口。当bean实现该接口时，容器会将自身设置到当前bean。
> - ApplicationEventPublisherAware：同理，ApplicationContext还实现了ApplicationEventPublisherAware接口。
> - MessageSourceAware：同上。
>
> 3.BeanPostProcessor
>
> 相对于BeanFactoryPostProcessor存在于容器启动阶段而言，BeanPostProcessor存在于对象实例化阶段。它提供了两个方法，分别在bean的实例化前后调用。设置2中的Aware相关依赖就是通过ApplicationContextAwareProcessor.postProcessBeforeInitialization方法实现的。
>
> 除了检查标记接口以便应用自定义逻辑，BeanPostProcessor还可以替换当前对象实例或字节码增强当前对象实例等。AOP使用它类为bean生成代理对象。如BeanNameAutoProxyCreator。
>
> 4.InitializingBean和init-method
>
> 它是容器内部使用的一个对象生命周期标识接口，目的是在调用BeanPostProcessor的前置处理之后，会检查对象是否实现了该接口，如果是，则会调用其afterPropertiesSet方法进一步调整对象的状态。业务对象直接实现该接口比较有侵略性，所有Spring提供了在XML的bean中设置init-method的替代方式。
>
> 5.DisposableBean和detroy-method
>
> 实例化的最后一步是，容器检查singleton类型的对象，看起是否实现了DisposableBean接口或者配置了destroy-method属性指定的自定义对象销毁方法。如果是，就会为该实例注册一个用于对象销毁的回调。以便在singleton对象销毁前，执行销毁逻辑。对于BeanFactory容器而言，使用ConfigurableBeanFactory.destroySingletons触发销毁容器内所有singleton的对象。对ApplicationContext而言使用AbstractApplicationContext.registerShutdownHook。
>
> 



#### 2.ApplicationContext

> 除了拥有BeanFactory的所有支持，还提供了事件发布，国际化信息支持以及统一资源管理的功能。它启动的时候就完成了所有初始化。

![image-20210726000808737](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210726000808737.png)

> MessageSource接口为国际化支持接口，ResouceLoader为统一资源管理接口，ApplicationEventPublisher为事件发布接口。

