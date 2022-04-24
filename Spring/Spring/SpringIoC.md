# Spring IoC

## Ioc的基本概念

### 让别人为你服务

IoC 的全称为Inversion of Control，中文通常翻译为“控制反转”，它还有一个别名叫做依赖注入（Dependency Injection）。

![image-20220420205449383](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220420205449383.png)

通常情况下，被注入对象会直接依赖于被依赖对象。但是，在IoC的场景中，二者之间通过IoC Service Provider来打交道，所有的被注入对象和依赖对象现在由IoC Service Provider统一管理。被注入对象需要 什么，直接跟IoC Service Provider招呼一声，后者就会把相应的被依赖对象注入到被注入对象中，从而 达到IoC Service Provider为被注入对象服务的目的。IoC Service Provider在这里就是通常的IoC容器所充 当的角色。从被注入对象的角度看，与之前直接寻求依赖对象相比，依赖对象的取得方式发生了反转， 控制也从被注入对象转到了IoC Service Provider那里。

### IoC的三种注入方式

#### 构造方法注入

被注入对象可以通过在其构造方法中声明依赖对象的参数列表，让外部（通常是IoC容器）知道它需要哪些依赖对象。

```java
public FXNewsProvider(IFXNewsListener newsListner,IFXNewsPersister newsPersister){
	this.newsListener = newsListner;
	this.newPersistener = newsPersister;
}
```

IoC Service Provider会检查被注入对象的构造方法，取得它所需要的依赖对象列表，进而为其注 入相应的对象。同一个对象是不可能被构造两次的，因此，被注入对象的构造乃至其整个生命周期， 应该是由IoC Service Provider来管理的。

构造方法注入方式比较直观，对象被构造完成后，即进入就绪状态，可以马上使用。

#### setter方法注入

当前对象只要为其依赖对象所对应的属性添加setter 方法，就可以通过setter方法将相应的依赖对象设置到被注入对象中。

```java
public setIFXNewsListener(IFXNewsListener newsListner){
	this.newsListener = newsListner;
}
```

setter方法注入虽不像构造方法注入那样，让对象构造完成后即可使用，但相对来说更宽松一些， 可以在对象构造完成后再注入。

#### 接口注入

相对于前两种注入方式来说，接口注入没有那么简单明了。被注入对象如果想要IoC Service Provider为其注入依赖对象，就必须实现某个接口。这个接口提供一个方法，用来为其注入依赖对象。 IoC Service Provider最终通过这些接口来了解应该为被注入对象注入什么依赖对象。

目前这种方式已经被废弃。

### 小结

IoC是一种可以帮助我们解耦各业务对象间依赖关系的对象绑定方式！。

## IoC的Service Provider

### IoC Service Provider 的职责

IoC Service Provider的职责相对来说比较简单，主要有两个：业务对象的构建管理和业务对象间 的依赖绑定。 

* 业务对象的构建管理。在IoC场景中，业务对象无需关心所依赖的对象如何构建如何取得，但这部分工作始终需要有人来做。所以，IoC Service Provider需要将对象的构建逻辑从客户端对象那里剥离出来，以免这部分逻辑污染业务对象的实现。 
* 业务对象间的依赖绑定。对于IoC Service Provider来说，这个职责是最艰巨也是最重要的，这是它的最终使命之所在。如果不能完成这个职责，那么，无论业务对象如何的“呼喊”，也不会得到依赖对象的任何响应（最常见的倒是会收到一个NullPointerException）。IoC Service Provider通过结合之前构建和管理的所有业务对象，以及各个业务对象间可以识别的依赖关系，将这些对象所依赖的对象注入绑定，从而保证每个业务对象在使用的时候，可以处于就绪状 态。

### IoC Service Provider 如何管理对象间的依赖关系

IoC Service Provider需要寻求其他方式来记录诸多对象之间的对应关系。比如：

* 它可以通过最基本的文本文件来记录被注入对象和其依赖对象之间的对应关系；

* 它也可以通过描述性较强的XML文件格式来记录对应信息； 

* 它还可以通过编写代码的方式来注册这些对应信息； 

#### 直接编码方式

通过为相应的类指定对应的具体实例，可以告知IoC容器，当我们要这种类型的对象实例时，请 将容器中注册的、对应的那个具体实例返回给我们。

```java
IoContainer container = ...;
container.register(FXNewsProvider.class,new FXNewsProvider());
container.register(IFXNewsListener.class,new DowJonesNewsListener());
...
FXNewsProvider newsProvider = (FXNewsProvider)container.get(FXNewsProvider.class);
newProvider.getAndPersistNews(); 
```

通过程序编码让最终的IoC Service Provider（也就是各个IoC框架或者容器实现）得以知晓 服务的“奥义”，是管理依赖绑定关系的最基本方式。

#### 配置文件方式

通过XML文件来管理对象注册和对象间依赖关系，比如Spring IoC容器和在PicoContainer基础上扩展的NanoContainer，都是采用XML文件来管理和保存依赖注入信息的。

```xml
<bean id="newsProvider" class="..FXNewsProvider">
	<property name="newsListener">
 		<ref bean="djNewsListener"/>
 	</property>
 	<property name="newPersistener">
 		<ref bean="djNewsPersister"/>
 	</property>
</bean>

<bean id="djNewsListener" class="..impl.DowJonesNewsListener">
</bean>

<bean id="djNewsPersister"	class="..impl.DowJonesNewsPersister">
</bean> 
```

```java
container.readConfigurationFiles(...); // 读取xml文件
FXNewsProvider newsProvider = (FXNewsProvider)container.getBean("newsProvider"); // 根据名字获取bean
newsProvider.getAndPersistNews();
```

#### 元数据方式

接在类中使用元数据信息来标注各个对象之间的依赖关系，然后由Guice框架根 据这些注解所提供的信息将这些对象组装后，交给客户端对象使用。

```java
public class FXNewsProvider{
	private IFXNewsListener newsListener;
 	private IFXNewsPersister newPersistener;
 	@Inject
	public FXNewsProvider(IFXNewsListener listener,IFXNewsPersister persister){
 		this.newsListener = listener;
 		this.newPersistener = persister;
 	} 
} 
```

通过@Inject，我们指明需要IoC Service Provider通过构造方法注入方式，为FXNewsProvider注入其所依赖的对象。

余下的依赖相关信息，在Guice中是由相应的Module来提供的

```java
public class NewsBindingModule extends AbstractModule
{
 	@Override
 	protected void configure() {
	 	bind(IFXNewsListener.class).to(DowJonesNewsListener.class).in(Scopes.SINGLETON);
    	bind(IFXNewsPersister.class).to(DowJonesNewsPersister.class).in(Scopes.SINGLETON);
 	}
}
```

通过Module指定进一步的依赖注入相关信息之后，我们就可以直接从Guice那里取得最终已经注 入完毕，并直接可用的对象了

```java
Injector injector = Guice.createInjector(new NewsBindingModule());
FXNewsProvider newsProvider = injector.getInstance(FXNewsProvider.class);
newsProvider.getAndPersistNews();
```

## BeanFactory

Spring的IoC容器是一个提供IoC支持的轻量级容器，除了基本 的IoC支持，它作为轻量级容器还提供了IoC之外的支持。如在Spring的IoC容器之上，Spring还提供了 相应的AOP框架支持、企业级服务集成等服务。

![image-20220422145627495](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220422145627495.png)

### 拥有BeanFactory之后的生活

有了BeanFactory之类的IoC容器之后，需要依赖什么让BeanFactory为我们推过来（Push）就行了。拥有BeanFactory之后，要使用IoC模式进行系统业务对象的开发。

### BeanFactory的对象注册与依赖绑定方式

#### 直接编码方式

把编码方式单独提出来称作一种方式并不十分恰当。因为不管什么方式，最终都需要编码 才能“落实”所有信息并付诸使用。不过，通过这些代码，起码可以让我们更加清楚BeanFactory在 底层是如何运作的。

```java
    public static void main(String[] args) {
        DefaultListableBeanFactory beanRegistry = new DefaultListableBeanFactory();
        BeanFactory container = (BeanFactory) bindViaCode(beanRegistry);
        FXNewsProvider newsProvider = (FXNewsProvider) container.getBean("djNewsProvider");
        newsProvider.getAndPersistNews();
    }

    public static BeanFactory bindViaCode(BeanDefinitionRegistry registry) {
        AbstractBeanDefinition newsProvider = new RootBeanDefinition(FXNewsProvider.class, true);
        AbstractBeanDefinition newsListener = new RootBeanDefinition(DowJonesNewsListener.class, true);
        AbstractBeanDefinition newsPersister = new RootBeanDefinition(DowJonesNewsPersister.class, true);
        // 将bean定义注册到容器中
        registry.registerBeanDefinition("djNewsProvider", newsProvider);
        registry.registerBeanDefinition("djListener", newsListener);
        registry.registerBeanDefinition("djPersister", newsPersister);
        // 指定依赖关系
        // 1. 可以通过构造方法注入方式
        ConstructorArgumentValues argValues = new ConstructorArgumentValues();
        argValues.addIndexedArgumentValue(0, newsListener);
        argValues.addIndexedArgumentValue(1, newsPersister);
        newsProvider.setConstructorArgumentValues(argValues);
        // 2. 或者通过setter方法注入方式
        MutablePropertyValues propertyValues = new MutablePropertyValues();
        propertyValues.addPropertyValue(new ropertyValue("newsListener", newsListener));
        propertyValues.addPropertyValue(new PropertyValue("newPersistener", newsPersister));
        newsProvider.setPropertyValues(propertyValues);
        // 绑定完成
        return (BeanFactory) registry;
    }
```

![image-20220423223059797](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220423223059797.png)

DefaultListableBeanFactory除了间接地实现了BeanFactory接口，还实现了BeanDefinitionRegistry接口，该接口才 是在BeanFactory的实现中担当Bean注册管理的角色。基本上，BeanFactory接口只定义如何访问容 器内管理的Bean的方法，各个BeanFactory的具体实现类负责具体Bean的注册以及管理工作。 BeanDefinitionRegistry接口定义抽象了Bean的注册逻辑。

每一个受管的对象，在容器中都会有一个BeanDefinition的实例（instance）与之相对应，该 BeanDefinition的实例负责保存对象的所有必要信息，包括其对应的对象的class类型、是否是抽象 类、构造方法参数以及其他属性等。

#### 外部配置文件方式

采用外部配置文件时，Spring的IoC容器有一个统一的处理方式。通常情况下，需要根据不同的外部配置文件格式，给出相应的BeanDefinitionReader实现类，由BeanDefinitionReader的相应实现类负责将相应的配置文件内容读取并映射到BeanDefinition，然后将映射后的BeanDefinition注册到一个BeanDefinitionRegistry，之后，BeanDefinitionRegistry即完成Bean的注册和加载。 当然，大部分工作，包括解析文件格式、装配BeanDefinition之类的工作，都是由BeanDefinitionReader的相应实现类来做的，BeanDefinitionRegistry只不过负责保管而已。

整个过程类似于如下代码： 

```java
BeanDefinitionRegistry beanRegistry = <某个BeanDefinitionRegistry实现类，通常为DefaultListableBeanFactory>;
BeanDefinitionReader beanDefinitionReader = new BeanDefinitionReaderImpl(beanRegistry);
beanDefinitionReader.loadBeanDefinitions("配置文件路径");
// 现在我们就取得了一个可用的Bea nDefinitionRegistry实例
```

##### Properties配置格式的加载

Spring提供了org.springframework.beans.factory.support.PropertiesBeanDefinitionReader类用于Properties格式配置文件的加载，所以，我们不用自己去实现BeanDefinitionReader， 只要根据该类的读取规则，提供相应的配置文件即可。

加载Properties配置的BeanFactory的使用演示

```java
    public static void main(String[] args) {
        DefaultListableBeanFactory beanRegistry = new DefaultListableBeanFactory();
        BeanFactory container = (BeanFactory) bindViaPropertiesFile(beanRegistry);
        FXNewsProvider newsProvider = (FXNewsProvider) container.getBean("djNewsProvider");
        newsProvider.getAndPersistNews();
    }


    public static BeanFactory bindViaPropertiesFile(BeanDefinitionRegistry registry) {
        PropertiesBeanDefinitionReader reader =
                new PropertiesBeanDefinitionReader(registry);
        reader.loadBeanDefinitions("classpath:../../binding-config.properties");
        return (BeanFactory) registry;
    }
```

##### XML配置格式的加载

与为Properties配置文件格式提供PropertiesBeanDefinitionReader相对应，Spring同样为XML 格式的配置文件提供了现成的BeanDefinitionReader实现，即XmlBeanDefinitionReader。 XmlBeanDefinitionReader负责读取Spring指定格式的XML配置文件并解析，之后将解析后的文件内 容映射到相应的BeanDefinition，并加载到相应的BeanDefinitionRegistry中（在这里是DefaultListableBeanFactory）。这时，整个BeanFactory就可以放给客户端使用了。

Spring还在DefaultListableBeanFactory的基础上构建了简化XML格式配置加载的XmlBeanFactory实现。

```java
    public static void main(String[] args) {
        DefaultListableBeanFactory beanRegistry = new DefaultListableBeanFactory();
        BeanFactory container = (BeanFactory) bindViaXMLFile(beanRegistry);
        FXNewsProvider newsProvider = (FXNewsProvider) container.getBean("djNewsProvider");
        newsProvider.getAndPersistNews();
    }


    public static BeanFactory bindViaXMLFile(BeanDefinitionRegistry registry) {
        XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(registry);
        reader.loadBeanDefinitions("classpath:../news-config.xml");
        return (BeanFactory) registry;
        // 或者直接
        //return new XmlBeanFactory(new ClassPathResource("../news-config.xml"));
    }
```

#### 注解方式

如果要通过注解标注的方式为FXNewsProvider注入所需要的依赖，现在可以使用@Autowired以 及@Component对相关类进行标记。

@Autowired是这里的主角，它的存在将告知Spring容器需要为当前对象注入哪些依赖对象。而 @Component则是配合Spring 2.5中新的classpath-scanning功能使用的。现在我们只要再向Spring的配置 文件中增加一个“触发器”，使用@Autowired和@Component标注的类就能获得依赖对象的注入了。

```java
@Component
public class FXNewsProvider {
     @Autowired
    private IFXNewsListener newsListener;
    @Autowired
    private IFXNewsPersister newPersistener;

    public FXNewsProvider(IFXNewsListener newsListner, IFXNewsPersister newsPersister) {
        this.newsListener = newsListner;
        this.newPersistener = newsPersister;
    }
}

@Component
public class DowJonesNewsListener implements IFXNewsListener
{
 ...
}
@Component
public class DowJonesNewsPersister implements IFXNewsPersister
{
 ...
} 
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:tx="http://www.springframework.org/schema/tx"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-2.5.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx-2.5.xsd">

    <context:component-scan base-package="cn.spring21.project.base.package"/>
</beans> 
```

会到指定的包（package）下面扫描标注有@Component的类，如果 找到，则将它们添加到容器进行管理，并根据它们所标注的@Autowired为这些类注入符合条件的依赖对象。

```java
public static void main(String[] args){
    ApplicationContext ctx = new ClassPathXmlApplicationContext("配置文件路径");
    FXNewsProvider newsProvider = (FXNewsProvider)container.getBean("FXNewsProvider");
    newsProvider.getAndPersistNews();
} 
```

现在可以完全使用配置类的方式来开启取代context:component-scan标签

```java
@ComponentScan("com.ln")
public class AppConfig {

}
```

```java
public static void main(String[] args){
	AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
	UserService bean = applicationContext.getBean(UserService.class);
	bean.test();
}
```

### BeanFactory的XML之旅

#### \<beans\>和\<bean\>标签

##### \<beans\>

\<beans\>是XML配置文件中最顶层的元素，它下面可以包含0或者1个\<description\>和多个\<bean\>以及\<import\>或者\<alias\>

![image-20220423225507726](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220423225507726.png)

\<beans\>的属性

* default-lazy-init。其值可以指定为true或者false，默认值为false。用来标志是否对所 有的进行延迟初始化。 

* default-autowire。可以取值为no、byName、byType、constructor以及autodetect。默 认值为no，如果使用自动绑定的话，用来标志全体bean使用哪一种默认绑定方式。 
* default-dependency-check。可以取值none、objects、simple以及all，默认值为none， 即不做依赖检查。 
*  default-init-method。如果所管辖的按照某种规则，都有同样名称的初始化方法的 话，可以在这里统一指定这个初始化方法名，而不用在每一个上都重复单独指定。 
* default-destroy-method。与default-init-method相对应，如果所管辖的bean有按照某种 规则使用了相同名称的对象销毁方法，可以通过这个属性统一指定。

\<import\>标签：在 想加载主要配置文件，并将主要配置文件所依赖的配置文件同时加载时，可以在这个主要的配置文件中通过\<import\>元素对其所依赖的配置文件进行引用。比如，如果A.xml中的\<bean\>定义可能依赖 B.xml中的某些\<bean\>定义，那么就可以在A.xml中使用\<import\>将B.xml引入到A.xml，以类似于\<import resource="B.xml"/\>的形式。

\<alias\>：可以通过\<alias\>为某些\<bean\>起一些“外号”（别名），通常情况下是为了减少输入。\<alias name="dataSourceForMasterDatabase" alias="masterDataSource"/>以后通过 dataSourceForMasterDatabase或者masterDataSource来引用这个\<bean\>都可以。

##### \<bean\>

每个业务对象作为个体，在Spring的XML配置文件中是与 \<bean\>元素一一对应的。

```xml
    <bean id="a" class="circularDependency.A">
        <property name="b" ref="b"/>
    </bean>
```

* id：通过id属性来指定当前注册对 象的beanName是什么。
* class：每个注册到容器的对象都需要通过元素的class属性指定其类型，否则，容器可不知道这 个对象到底是何方神圣。

#### 相互依赖的bean

#####  构造方法注入的XML之道

要通过构造方法注入方式，为当前业务对象注入其所依赖的对象， 需要使用\<constructor-arg\>，它有type和index两个属性。

```xml
    <bean id="mockBO" class="..MockBusinessObject">
        <constructor-arg type="int">
            <value>111111</value>
        </constructor-arg>
    </bean>

	<bean id="mockBO" class="..MockBusinessObject">
        <constructor-arg index="1" value="11111"/>
        <constructor-arg index="0" value="22222"/>
    </bean>
```

* type：指定构造方法的参数类型
* index：当某个业务对象的构造方法同时传入了多个类型相同的参数时，用index表名顺序

#####  setter方法注入的XML之道

Spring为setter方法注入提供了\<property\>元素。\<property\>有一个name属性（attribute），用来指定该将会注入的对象所对应的实例变量名称。

```xml
    <bean id="b" class="circularDependency.B">
        <property name="a" ref="a"/>
    </bean>
```

##### \<property>和\<constructor-arg\>中可用的配置项

为了能够指定多种注 入类型，Spring还提供了其他的元素供我们使用，这包括bean、ref、idref、value、null、list、set、map、 props。

##### depends-on

如果某些时候，我们没有通过类似\<ref\>的元素明确指定对象A依赖于对象 B的话，如何让容器在实例化对象A之前首先实例化对象B呢？ 

```java
public class SystemConfigurationSetup
{
    static
    {
        DOMConfigurator.configure("配置文件路径");
        // 其他初始化代码
    }

} 
```

如果ClassA需要使用log4j，那么就必须在bean定义中使用depends-on来要求容器在初始化自身实例之前首先实例化SystemConfigurationSetup， 以保证日志系统的可用。

```xml
<bean id="classAInstance" class="...ClassA" depends-on="configSetup"/>
<bean id="configSetup" class="SystemConfigurationSetup"/>
```

##### autowire

除了可以通过配置明确指定bean之间的依赖关系，Spirng还提供了根据bean定义的某些特点将相互依赖的某些bean直接自动绑定的功能。通过\<bean\>的autowire属性，可以指定当前bean定义采用某种类型的自动绑定模式。

```xml
<bean id="fooBean" class="...Foo" autowire="byType">
</bean> 
```

* no：不开启启动自动绑定，容器默认的方式。
* byName：按照类中声明的实例变量的名称，与XML配置文件中声明的bean定义的beanName的值进行匹配， 相匹配的bean定义将被自动绑定到当前实例变量上。
* byType：容器会根据当前bean定义类型，分析其 相应的依赖对象类型，然后到容器所管理的所有bean定义中寻找与依赖对象类型相同的bean定义，然后将找到的符合条件的bean自动绑定到当前bean定义。byType只能保证，在容器中只存在一个符合条件的依赖对象的时候才会发挥最大的作用。
* constructor：byName和byType类型的自动绑定模式是针对property的自动绑定，而constructor类型则是针对 构造方法参数的类型而进行的自动绑定，它同样是byType类型的绑定模式。不过，constructor是匹 配构造方法的参数类型，而不是实例属性的类型。
* autodetect：这种模式是byType和constructor模式的结合体，如果对象拥有默认无参数的构造方法，容器会 优先考虑byType的自动绑定模式。否则，会使用constructor模式。当然，如果通过构造方法注入绑 定后还有其他属性没有绑定，容器也会使用byType对剩余的对象属性进行自动绑定。 

##### dependency-check

该功能主要与自动绑定结合使用，可以保证当自动绑定完成后，最终确认每个对象所依赖的对象是否按 照所预期的那样被注入。

* no：不做依赖检查，默认值。
* simple：容器会对简单属性类型以及相 关的collection进行依赖检查，对象引用类型的依赖除外。
* object：只对对象引用类型依赖进行检查。
* all：将simple和object相结合。

##### lazy-init 

主要是可以针对ApplicationContext容器的bean初始化行为施以更多控制。

```xml
<bean id="lazy-init-bean" class="..." lazy-init="true"/>
<bean id="not-lazy-init-bean" class="..."/>
```

如果我们真想保证lazy-init-bean一定会被延迟初始化的话，就需要保证依赖于该bean定义的其他bean定义也同样设置为延迟初始化。

#### bean之间的继承

让该子类与FXNewsProvider使用相同的IFXNewsPersister，即DowJonesNewsPersister，那么可以使用如代码

```xml
    <bean id="superNewsProvider" class="..FXNewsProvider">
        <property name="newsListener">
            <ref bean="djNewsListener"/>
        </property>
        <property name="newPersistener">
            <ref bean="djNewsPersister"/>
        </property>
    </bean>
    <bean id="subNewsProvider" class="..SpecificFXNewsProvider">
        <property name="newsListener">
            <ref bean="specificNewsListener"/>
        </property>
        <property name="newPersistener">
            <ref bean="djNewsPersister"/>
        </property>
    </bean>
```

我们在声明subNewsProvider的时候，使用了parent属性，将其值指定为superNewsProvider， 这样就继承了superNewsProvider定义的默认值，只需要将特定的属性进行更改，而不要全部又重新 定义一遍。

```xml
    <bean id="superNewsProvider" class="..FXNewsProvider">
        <property name="newsListener">
            <ref bean="djNewsListener"/>
        </property>
        <property name="newPersistener">
            <ref bean="djNewsPersister"/>
        </property>
    </bean>

    <bean id="subNewsProvider" parent="superNewsProvider"
          class="..SpecificFXNewsProvider">
    <property name="newsListener">
        <ref bean="specificNewsListener"/>
    </property>
    </bean>
```

#### bean 的 scope 

scope用来声明容器中的对象所应该处的限定场景或者说该对象的存活时间，即容器在对象进入其 相应的scope之前，生成并装配这些对象，在该对象不再处于这些scope的限定之后，容器通常会销毁这些对象。

##### singleton

标记为拥有singleton scope的对象定义，在Spring的IoC容器中只存在一个实例，所有对该对象的引用将共享这个实例。该实例从容器启动，并因为第一次被请求而初始化之后，将一直存活到容器退出， 也就是说，它与IoC容器“几乎”拥有相同的“寿命”。

##### prototype

针对声明为拥有prototype scope的bean定义，容器在接到该类型对象的请求的时候，会每次都重新生成一个新的对象实例给请求方。虽然这种类型的对象的实例化以及属性设置等工作都是由容器负责 的，但是只要准备完毕，并且对象实例返回给请求方之后，容器就不再拥有当前返回对象的引用，请 求方需要自己负责当前返回对象的后继生命周期的管理工作，包括该对象的销毁。

##### request、session和global session

它们只适用于Web应用程序，通常是与XmlWebApplicationContext共同使用。

* request：为每个HTTP请求创建一个全新的RequestProcessor对象供当前请求使用，当请求结束后，该对象实例的生命周期即告结束。当同时有10个HTTP 请求进来的时候，容器会分别针对这10个请求返回10个全新的RequestProcessor对象实例，且它们 之间互不干扰。
* session：Spring容器会为每个独立的session创建属于它们自己的全新的UserPreferences对象实例。
* global session：global session只有应用在基于portlet的Web应用程序中才有意义，它映射到portlet的global范围的session。

#### 工厂方法与FactoryBean

有时，我们需要依赖第三方库，需要实例化并使用第三方库中的相 关类，这时，接口与实现类的耦合性需要其他方式来避免。

通常的做法是通过使用工厂方法（Factory Method）模式，提供一个工厂类来实例化具体的接口 实现类，这样，主体对象只需要依赖工厂类，具体使用的实现类有变更的话，只是变更工厂类，而主 体对象不需要做任何变动。

```java
public class Foo {

    private BarInterface barInterface;

    public Foo() {
        // barInterface = BarInterfaceFactory.getInstance();
        // 或者
        // barInterface = new BarInterfaceFactory().getInstance();
    }
}
```

针对使用工厂方法模式实例化对象的方式，Spring的IoC容器同样提供了对应的集成支持。我们所 要做的，只是将工厂类所返回的具体的接口实现类注入给主体对象（这里是Foo）。

##### 静态工厂方法

```java
public class StaticBarInterfaceFactory { 
	public static BarInterface getInstance() {
        return new BarInterfaceImpl();
    }
}
```

```xml
    <bean id="foo" class="...Foo">
        <property name="barInterface">
            <ref bean="bar"/>
        </property>
    </bean>
    <bean id="bar" class="...StaticBarInterfaceFactory" factory-method="getInstance"/>
```

class指定静态方法工厂类，factory-method指定工厂方法名称，然后，容器调用该静态方法工厂类的指定工厂方法（getInstance），并返回方法调用后的结果，即BarInterfaceImpl的实例。 也就是说，为foo注入的bar实际上是BarInterfaceImpl的实例，即方法调用后的结果，而不是静态工厂方法类（StaticBarInterfaceFactory）。我们可以实现自己的静态工厂方法类返回任意类型 的对象实例，但工厂方法类的类型与工厂方法返回的类型没有必然的相同关系。

##### 非静态工厂方法

```java
public class NonStaticBarInterfaceFactory { 
	public BarInterface getInstance() {
        return new BarInterfaceImpl();
    }
}
```

因为工厂方法为非静态的，我们只能通过某个NonStaticBarInterfaceFactory实例来调用该方法。

```xml
    <bean id="foo" class="...Foo">
        <property name="barInterface">
            <ref bean="bar"/>
        </property>
    </bean> 7
    <bean id="barFactory" class="...NonStaticBarInterfaceFactory"/>
    <bean id="bar" factory-bean="barFactory" factory-method="getInstance"/>
```

NonStaticBarInterfaceFactory是作为正常的bean注册到容器的，而bar的定义则与静态工厂方 法的定义有些不同。现在使用factory-bean属性来指定工厂方法所在的工厂类实例，而不是通过 class属性来指定工厂方法所在类的类型。

##### FactoryBean

FactoryBean是Spring容器提供的一种可以扩展容器对象实例化逻辑的接口。

当某些对象的实例化过程过于烦琐，通过XML配置过于复杂，使我们宁愿使用Java代码来完成这 个实例化过程的时候，或者，某些第三方库不能直接注册到Spring容器的时候，就可以实现org.springframework.beans.factory.FactoryBean接口，给出自己的对象实例化逻辑代码。

```java
public class NextDayDateFactoryBean implements FactoryBean {
    public Object getObject() throws Exception {
        return new DateTime().plusDays(1);
    }

    public Class getObjectType() {
        return DateTime.class;
    }

    public boolean isSingleton() {
        return false;
    }
} 
```

要使用NextDayDateFactoryBean，只需要如下这样将其注册到容器即可：

```xml
    <bean id="nextDayDateDisplayer" class="...NextDayDateDisplayer">
        <property name="dateOfNextDay">
            <ref bean="nextDayDate"/>
        </property>
    </bean>

    <bean id="nextDayDate" class="...NextDayDateFactoryBean">
    </bean>
```

配置上看不出与平常的bean定义有何不同，不过，只有当我们看到NextDayDateDisplayer的定 义的时候，才会知道FactoryBean的魔力到底在哪。

```java
public class NextDayDateDisplayer{
    private DateTime dateOfNextDay;
    // 相应的setter方法
    // ...
} 
```

NextDayDateDisplayer所声明的依赖dateOfNextDay的类型为DateTime，而不是 NextDayDateFactoryBean。也就是说FactoryBean类型的bean定义，通过正常的id引用，容器返回的是FactoryBean所“生产”的对象类型，而非FactoryBean实现本身。

如果一定要取得FactoryBean本身的话，可以通过在bean定义的id之前加前缀&来达到目的。

Spring容器内部许多地方了使用FactoryBean。TransactionProxyFactoryBean、ProxyFactoryBean、SqlMapClientFactoryBean等。

#### 偷梁换柱之术

```java
public class FXNewsBean {
    
}
```

```java
public class MockNewsPersister {

    private FXNewsBean newsBean;

    public void persistNews(FXNewsBean bean) {
        persistNews();
    }

    public void persistNews() {
        System.out.println("persist bean:" + getNewsBean());
    }
    public FXNewsBean getNewsBean() {
        return newsBean;
    }

    public void setNewsBean(FXNewsBean newsBean) {
        this.newsBean = newsBean;
    }

}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="newsBean" class="book.FXNewsBean" scope="prototype">
    </bean>
    <bean id="mockPersister" class="book.MockNewsPersister">
        <property name="newsBean">
            <ref bean="newsBean"/>
        </property>
    </bean>
    
</beans>
```

```java
public class BeanFactoryPlay {
    
    public static void main(String[] args){
        BeanFactory container = new XmlBeanFactory(new ClassPathResource("magic-scope.xml"));
        MockNewsPersister persister = (MockNewsPersister)container.getBean("mockPersister");
        persister.persistNews();
        persister.persistNews();
    }
    
}
```

输出结果

```txt
persist bean:book.FXNewsBean@13c78c0b
persist bean:book.FXNewsBean@13c78c0b
```

从输出看，对象实例是相同的，而这与我们的初衷是相悖的。因为每次调用persistNews都会调 用getNewsBean()方法并返回一个FXNewsBean实例，而FXNewsBean实例是prototype类型的，因此每次不是应该输出不同的对象实例嘛？ 

虽然FXNewsBean拥有prototype类型的scope，但当容器将一个FXNewsBean的实例注入MockNewsPersister之后，MockNewsPersister就会一直持有这个FXNewsBean实例的引用。虽然每 次输出都调用了getNewsBean()方法并返回了 FXNewsBean 的实例，但实际上每次返回的都是 MockNewsPersister持有的容器第一次注入的实例。

解决问题的关键在于保证getNewsBean()方法每次 从容器中取得新的FXNewsBean实例，而不是每次都返回其持有的单一实例。

##### 方法注入

Spring容器提出了一种叫做方法注入（Method Injection）的方式，可以帮助我们解决上述问题。只要让getNewsBean方法声明符合规定的格式，并在配置文件中通知容器，当 该方法被调用的时候，每次返回指定类型的对象实例即可。方法声明需要符合的规格定义如下：

```txt
<public|protected> [abstract] <return-type> theMethodName(no-arguments); 
```

该方法必须能够被子类实现或者覆写，因为容器会为我们要进行方法注入的对象使用 Cglib动态生成一个子类实现，从而替代当前对象。既然我们的getNewsBean()方法已经满足以上方法 声明格式，剩下唯一要做的就是配置该类：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="newsBean" class="book.FXNewsBean" scope="prototype">
    </bean>
    <bean id="mockPersister" class="book.MockNewsPersister">
        <lookup-method name="getNewsBean" bean="newsBean"/>
    </bean>
    
</beans>
```

通过的name属性指定需要注入的方法名，bean属性指定需要注入的对象，当 getNewsBean方法被调用的时候，容器可以每次返回一个新的FXNewsBean类型的实例。再次检查执行结果，输出的实例引用应该是不同的：

```txt
persist bean:book.FXNewsBean@5f2108b5
persist bean:book.FXNewsBean@31a5c39e
```

##### 使用BeanFactoryAware接口

即使没有方法注入，只要在实现getNewsBean()方法的时候，能够保证每次调用BeanFactory的getBean("newsBean")，就同样可以每次都取得新的FXNewsBean对象实例。现在，我们唯 一需要的，就是让MockNewsPersister拥有一个BeanFactory的引用。

Spring框架提供了一个BeanFactoryAware接口，容器在实例化实现了该接口的bean定义的过程 中，会自动将容器本身注入该bean。

```java
public class MockNewsPersister implements  BeanFactoryAware {

    private BeanFactory beanFactory;

    @Override
    public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
        this.beanFactory = beanFactory;
    }
    private FXNewsBean newsBean;

    public void persistNews(FXNewsBean bean) {
        persistNews();
    }

    public void persistNews() {
        System.out.println("persist bean:" + getNewsBean());
    }
    public FXNewsBean getNewsBean() {
        return (FXNewsBean) beanFactory.getBean("newsBean");
    }

    public void setNewsBean(FXNewsBean newsBean) {
        this.newsBean = newsBean;
    }
    
}
```

配置简化为

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="newsBean" class="book.FXNewsBean" scope="prototype">
    </bean>
    <bean id="mockPersister" class="book.MockNewsPersister">
    </bean>
    
</beans>
```

##### 使用ObjectFactoryCreatingFactoryBean

与使用BeanFactoryAware接口类似

##### 方法替换

与org.springframework.beans.factory.support.MethodReplacer相关

### 容器背后的秘密

#### 战略性观望

Spring的IoC容器实现以上功能的过程，基本上可以按照类似的流程划分为两个阶段，即容器启动 8 阶段和Bean实例化阶段。

Spring的IoC容器在实现的时候，充分运用了这两个实现阶段的不同特点，在每个阶段都加入了相 应的容器扩展点，以便我们可以根据具体场景的需要加入自定义的扩展逻辑。

![image-20220424010204145](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220424010204145.png)

##### 容器启动阶段

容器启动伊始，首先会通过某种途径加载Configuration MetaData。除了代码方式比较直接，在大部分情况下，容器需要依赖某些工具类（BeanDefinitionReader）对加载的Configuration MetaData进行解析和分析，并将分析后的信息编组为相应的BeanDefinition，最后把这些保存了bean定义必 要信息的BeanDefinition，注册到相应的BeanDefinitionRegistry，这样容器启动工作就完成了。

总地来说，该阶段所做的工作可以认为是准备性的，重点更加侧重于对象管理信息的收集。当然， 一些验证性或者辅助性的工作也可以在这个阶段完成。

##### Bean实例化阶段

经过第一阶段，现在所有的bean定义信息都通过BeanDefinition的方式注册到了BeanDefinitionRegistry中。当某个请求方通过容器的getBean方法明确地请求某个对象，或者因依赖关系容器需要隐式地调用getBean方法时，就会触发第二阶段的活动。

该阶段，容器会首先检查所请求的对象之前是否已经初始化。如果没有，则会根据注册的BeanDefinition所提供的信息实例化被请求对象，并为其注入依赖。如果该对象实现了某些回调接 口，也会根据回调接口的要求来装配它。当该对象装配完毕之后，容器会立即将其返回请求方使用。 如果说第一阶段只是根据图纸装配生产线的话，那么第二阶段就是使用装配好的生产线来生产具体的 产品了。

#### 插手“容器的启动”

Spring提供了一种叫做BeanFactoryPostProcessor的容器扩展机制。该机制允许我们在容器实例化相应对象之前，对注册到容器的BeanDefinition所保存的信息做相应的修改。这就相当于在容器实现的**第一阶段**最后加入一道工序，让我们对最终的BeanDefinition做一些额外的操作，比如修改其中bean定义的某些属性，为bean定义增加其他信息等。

Spring已经提供了几个现成的BeanFactoryPostProcessor实现类，其中，org.springframework.beans. factory.config.PropertyPlaceholderConfigurer和org.springframework.beans.factory. config.PropertyOverrideConfigurer是两个比较常用的BeanFactoryPostProcessor。另外，为 了处理配置文件中的数据类型与真正的业务对象所定义的数据类型转换，Spring还允许我们通过org.springframework.beans.factory.config.CustomEditorConfigurer来注册自定义的PropertyEditor以补助容器中默认的PropertyEditor。

可以通过两种方式来应用 BeanFactoryPostProcessor，分别针对基本的 IoC 容 器 BeanFactory和较为先进的容器ApplicationContext。

对于BeanFactory来说，我们需要用手动方式应用所有的BeanFactoryPostProcessor

```java
// 声明将被后处理的BeanFactory实例
ConfigurableListableBeanFactory beanFactory = new XmlBeanFactory(new ClassPathResource("..."));
// 声明要使用的BeanFactoryPostProcessor
PropertyPlaceholderConfigurer propertyPostProcessor = new PropertyPlaceholderConfigurer();
propertyPostProcessor.setLocation(new ClassPathResource("..."));
// 执行后处理操作
propertyPostProcessor.postProcessBeanFactory(beanFactory);
```

因为ApplicationContext会自动识别配 置文件中的BeanFactoryPostProcessor并应用它，所以，相对于BeanFactory，在ApplicationContext 中加载并应用BeanFactoryPostProcessor，仅需要在XML配置文件中将这些BeanFactoryPostProcessor简单配置一下即可。

```xml
    <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="locations">
            <list>
                <value>conf/jdbc.properties</value>
                <value>conf/mail.properties</value>
            </list>
        </property>
    </bean>
```

##### PropertyPlaceholderConfigurer

PropertyPlaceholderConfigurer允许我们在XML配置文件中使用占位符（PlaceHolder）， 并将这些占位符所代表的资源单独配置到简单的properties文件中来加载。

##### PropertyOverrideConfigurer

通过PropertyOverrideConfigurer对容器中配置的任何你想处理的bean定义的property信 息进行覆盖替换。

##### CustomEditorConfigurer

其他两个BeanFactoryPostProcessor都是通过对BeanDefinition中的数据进行变更以达到某 种目的。与它们有所不同，CustomEditorConfigurer是另一种类型的BeanFactoryPostProcessor实 现，它只是辅助性地将后期会用到的信息注册到容器，对BeanDefinition没有做任何变动。

Spring内部通过JavaBean的PropertyEditor来帮助进行String类型到其他类型的转换工作。只要 为每种对象类型提供一个 PropertyEditor ，就可以根据该对象类型取得与其相对应的 PropertyEditor来做具体的类型转换。Spring框架还提供了自身实现的一些PropertyEditor，这些PropertyEditor大部分都位于org.springframework. beans.propertyeditors包下。

##### 自定义PropertyEditor

通常情况下，对于Date类型，不同的Locale、不同的系统在表现形式上存在不同的需求。直接继承java.beans.PropertyEditorSupport类以避免实现java.beans.PropertyEditor接口的所有方法。

#### 了解bean的一生

容器启动之后，并不会马上就实例化相应的bean定义。我们知道，容器现在仅仅拥有所有对象的 BeanDefinition来保存实例化阶段将要用的必要信息。只有当请求方通过BeanFactory的getBean() 方法来请求某个对象实例的时候，才有可能触发Bean实例化阶段的活动。BeanFactory的getBean()方法可以被客户端对象显式调用，也可以在容器内部隐式地被调用。隐式调用有如下两种情况。

* 对于BeanFactory来说，对象实例化默认采用延迟初始化。通常情况下，当对象A被请求而需要第一次实例化的时候，如果它所依赖的对象B之前同样没有被实例化，那么容器会先实例化对象A所依赖的对象。这时容器内部就会首先实例化对象B，以及对象A依赖的其他还没有实例化的对象。这种情况是容器内部调用getBean()，对于本次请求的请求方是隐式的。

* ApplicationContext启动之后会实例化所有的bean定义。 但ApplicationContext在实现的过程中依然遵循Spring容器实现流程的两个阶段，只不过它会在启动阶段的活动完成之后，紧接着调用注册到该容器的所有bean定义的实例化方法getBean()。这就是为什么当你得到ApplicationContext类型的容器引用时，容器内所有对 象已经被全部实例化完成。不信你查一下类org.springframework.context.support.AbstractApplicationContext的refresh()方法。

当getBean()方法内部发现该bean定义之前还没有被实例化之后，会通过createBean()方法来进行具体的对象实例化，实例化过程如图所示

![image-20220424094127685](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220424094127685.png)

Spring容器将对其所管理的对象全部给予统一的生命周期管理，这些被管理的对象完全摆脱了原来那种“new完后被使用，脱离作用域后即被回收”的命运。每个bean在容器中是如何走过其一生的。

```
可以在org.springframework.beans.factory.support.AbstractBeanFactory类的代码中查看到getBean()方法的完整实现逻辑，可以在其子类org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory的代码中一窥createBean()方法的全貌。
```

##### Bean的实例化与BeanWrapper

容器在内部实现的时候，采用“策略模式（Strategy Pattern）”来决定采用何种方式初始化bean实例。 通常，可以通过反射或者CGLIB动态字节码生成来初始化相应的bean实例或者动态生成其子类。

org.springframework.beans.factory.support.InstantiationStrategy定义是实例化策略 的抽象接口，其直接子类SimpleInstantiationStrategy实现了简单的对象实例化功能，可以通过反射来实例化对象实例，但不支持方法注入方式的对象实例化。CglibSubclassingInstantiationStrategy继承了SimpleInstantiationStrategy的以反射方式实例化对象的功能，并且通过CGLIB 的动态字节码生成功能，该策略实现类可以动态生成某个类的子类，进而满足了方法注入所需的对象实例化需求。默认情况下，容器内部采用的是CglibSubclassingInstantiationStrategy。

容器只要根据相应bean定义的BeanDefintion取得实例化信息，结合CglibSubclassingInstantiationStrategy以及不同的bean定义类型，就可以返回实例化完成的对象实例。但是，返回方式上有些“点缀”。不是直接返回构造完成的对象实例，而是以BeanWrapper对构造完成的对象实例进行包裹，返回相应的BeanWrapper实例。

至此，第一步结束。 BeanWrapper接口通常在Spring框架内部使用，它有一个实现类org.springframework.beans. BeanWrapperImpl。其作用是对某个bean进行“包裹”，然后对这个“包裹”的bean进行操作，比如设置或者获取bean的相应属性值。而在第一步结束后返回BeanWrapper实例而不是原先的对象实例， 就是为了第二步“设置对象属性”。

BeanWrapper定义继承了org.springframework.beans.PropertyAccessor接口，可以以统一的方式对对象属性进行访问；BeanWrapper定义同时又直接或者间接继承了PropertyEditorRegistry 和TypeConverter接口。不知你是否还记得CustomEditorConfigurer？当把各种PropertyEditor注册给容器时，知道后面谁用到这些PropertyEditor吗？对，就是BeanWrapper！在第一步构造完成对象之后，Spring会根据对象实例构造一个BeanWrapperImpl实例，然后将之前CustomEditorConfigurer注册的PropertyEditor复制一份给BeanWrapperImpl实例（这就是BeanWrapper同时又是PropertyEditorRegistry的原因）。这样，当BeanWrapper转换类型、设置对象属性值时，就不会无从下手了。

使用BeanWrapper对bean实例操作很方便，可以免去直接使用Java反射API（Java Reflection API）5 操作对象实例的烦琐。来看一段代码，之后我们就会更加清楚Spring容器内部是如何设置对象属性的了！

```java
@Data
public class User {

    private String name;

    private Integer age;
	
    public static void main(String[] args) throws Exception {
        Object user = Class.forName("beanwrapper.User").newInstance();
        BeanWrapper wrapper = new BeanWrapperImpl(user);
        // 使用BeanWrapper免去了直接使用反射API操作的繁琐
        wrapper.setPropertyValue("name",  "jack");
        wrapper.setPropertyValue("age", 19);
        System.out.println(user);
        // 使用Java反射API操作对象
        // 如果你觉得没有太大差别，那是因为没有看到紧随其后的那些异常（exception）还有待处理！
        Object reflectUser = Class.forName("beanwrapper.User").newInstance();
        Class<?> userClass = reflectUser.getClass();
        Field nameFiled = userClass.getDeclaredField("name");
        nameFiled.set(reflectUser, "mark");
        Field ageField = userClass.getDeclaredField("age");
        ageField.set(reflectUser, 28);
        System.out.println(reflectUser);
    }
}
```

##### 各色的Aware接口

当对象实例化完成并且相关属性以及依赖设置完成之后，Spring容器会检查当前对象实例是否实现了一系列的以Aware命名结尾的接口定义。如果是，则将这些Aware接口定义中规定的依赖注入给当前对象实例。

* org.springframework.beans.factory.BeanNameAware。如果Spring容器检测到当前对象实例实现了该接口，会将该对象实例的bean定义对应的beanName设置到当前对象实例。
* org.springframework.beans.factory.BeanClassLoaderAware。如果容器检测到当前对象实例实现了该接口，会将对应加载当前bean的Classloader注入当前对象实例。默认会使用加载org.springframework.util.ClassUtils类的Classloader。
* org.springframework.beans.factory.BeanFactoryAware。如果对象声明实现了 BeanFactoryAware接口，BeanFactory容器会将自身设置到当前对象实例。这样，当前对象实例就拥有了一个BeanFactory容器的引用，并且可以对这个容器内允许访问的对象按照需要进行访问。

以上几个Aware接口只是针对BeanFactory类型的容器而言，对于ApplicationContext类型的容 器，也存在几个Aware相关接口。不过在检测这些接口并设置相关依赖的实现机理上，与以上几个接 口处理方式有所不同，使用的是下面将要说到的BeanPostProcessor方式。

* org.springframework.context.ResourceLoaderAware 。 ApplicationContext 实现了Spring的ResourceLoader接口。当容器检测到当前对象实例实现了 ResourceLoaderAware接口之后，会将当前ApplicationContext自身设置到对象实例，这样当前对象实例就拥有了其所在ApplicationContext容器的一个引用。

```java
ResourceLoaderPlay resourceLoaderPlay = container.getBean(ResourceLoaderPlay.class);
ApplicationContext applicationContext = (ApplicationContext) resourceLoaderPlay.getResourceLoader();
A myA = applicationContext.getBean(A.class);
```

* org.springframework.context.ApplicationEventPublisherAware。ApplicationContext 作为一个容器，同时还实现了ApplicationEventPublisher接口，这样，它就可以作为ApplicationEventPublisher来使用。所以，当前ApplicationContext容器如果检测到当前实例 化的对象实例声明了ApplicationEventPublisherAware接口，则会将自身注入当前对象。
* org.springframework.context.MessageSourceAware。ApplicationContext通过MessageSource接口提供国际化的信息支持，即I18n（Internationalization）。它自身就实现了MessageSource接口，所以当检测到当前对象实例实现了MessageSourceAware接口，则会将自身注入当前对象实例。
* org.springframework.context.ApplicationContextAware。 如果ApplicationContext 容器检测到当前对象实现了ApplicationContextAware接口，则会将自身注入当前对象实例。

##### BeanPostProcessor

与BeanFactoryPostProcessor通常会处理容器内所有符合条件的BeanDefinition类似，BeanPostProcessor会处理容器内所有符合条件的实例化后的对象实例。

BeanPostProcessor的两个方法中都传入了原来的对象实例的引用，这为我们扩展容器的对象实例化过程中的行为提供了极大的便利，我们几乎可以对传入的对象实例执行任何的操作。

通常比较常见的使用BeanPostProcessor的场景，是处理标记接口实现类，或者为当前对象提供代理实现。当ApplicationContext中每个对象的实例化过程走到BeanPostProcessor前置处理这一步时，ApplicationContext容器会检测到之前注册到容器的ApplicationContextAwareProcessor这个BeanPostProcessor的实现类，然后就会调用其postProcessBeforeInitialization()方法，检查并设置Aware相关依赖。ApplicationContextAwareProcessor的postProcessBeforeInitialization()代码如下

```java
public Object postProcessBeforeInitialization(final Object bean, String beanName) throws BeansException {
		AccessControlContext acc = null;

		if (System.getSecurityManager() != null &&
				(bean instanceof EnvironmentAware || bean instanceof EmbeddedValueResolverAware ||
						bean instanceof ResourceLoaderAware || bean instanceof ApplicationEventPublisherAware ||
						bean instanceof MessageSourceAware || bean instanceof ApplicationContextAware)) {
			acc = this.applicationContext.getBeanFactory().getAccessControlContext();
		}

		if (acc != null) {
			AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
				invokeAwareInterfaces(bean);
				return null;
			}, acc);
		}
		else {
			invokeAwareInterfaces(bean);
		}

		return bean;
	}

	private void invokeAwareInterfaces(Object bean) {
		if (bean instanceof Aware) {
			if (bean instanceof EnvironmentAware) {
				((EnvironmentAware) bean).setEnvironment(this.applicationContext.getEnvironment());
			}
			if (bean instanceof EmbeddedValueResolverAware) {
				((EmbeddedValueResolverAware) bean).setEmbeddedValueResolver(this.embeddedValueResolver);
			}
			if (bean instanceof ResourceLoaderAware) {
				((ResourceLoaderAware) bean).setResourceLoader(this.applicationContext);
			}
			if (bean instanceof ApplicationEventPublisherAware) {
				((ApplicationEventPublisherAware) bean).setApplicationEventPublisher(this.applicationContext);
			}
			if (bean instanceof MessageSourceAware) {
				((MessageSourceAware) bean).setMessageSource(this.applicationContext);
			}
			if (bean instanceof ApplicationContextAware) {
				((ApplicationContextAware) bean).setApplicationContext(this.applicationContext);
			}
		}
	}
```

Spring的AOP则更多地使用BeanPostProcessor来为对象生成相应的代理对象，如org.springframework.aop.framework. autoproxy.BeanNameAutoProxyCreator。

```txt
实际上，并非所有注册到Spring容器内的bean定义都是按照上述流程图实例化的。在所有的步骤之前，也就是实例化bean对象步骤之前，容器会首先检查容器中是否注册有InstantiationAwareBeanPostProcessor类型的BeanPostProcessor。如果有，首先使用相应的InstantiationAwareBeanPostProcessor来构造对象实例。构造成功后直接返回构造完成的对象实例，而不会按照“正规的流程”继续执行。这就是它可能造成“短路”的原因。通常情况下都是Spring容器内部使用这种特殊类型的BeanPostProcessor做一些动态对象代理等工作。
```

##### InitializingBean和init-method

org.springframework.beans.factory.InitializingBean是容器内部广泛使用的一个对象生命周期标识接口。

```java
public interface InitializingBean {
	void afterPropertiesSet() throws Exception;
}
```

其作用在于，在对象实例化过程调用过“BeanPostProcessor的前置处理” 之后，会接着检测当前对象是否实现了InitializingBean接口，如果是，则会调用其afterPropertiesSet()方法进一步调整对象实例的状态。

该接口在Spring容器内部广泛使用，但如果真的让我们的业务对象实现这个接口，则显得Spring容器比较具有侵入性。所以，Spring还提供了另一种方式来指定自定义的对象初始化操作，那就 是在XML配置的时候，使用的init-method属性。一般，我们是在集成第三方库，或者其他特殊的情况下，才会需要使用该特性。

##### DisposableBean与destroy-method

当所有的一切，该设置的设置，该注入的注入，该调用的调用完成之后，容器将检查singleton类 型的bean实例，看其是否实现了org.springframework.beans.factory.DisposableBean接口。或者其对应的bean定义是否通过的destroy-method属性指定了自定义的对象销毁方法。如果是， 就会为该实例注册一个用于对象销毁的回调（Callback），以便在这些singleton类型的对象实例销毁之前，执行销毁逻辑。

与InitializingBean和init-method用于对象的自定义初始化相对应，DisposableBean和 destroy-method为对象提供了执行自定义销毁逻辑的机会。

最常见到的该功能的使用场景就是在Spring容器中注册数据库连接池，在系统退出后，连接池应该关闭，以释放相应资源。

Spring容器在关闭之前， 不会聪明到自动调用这些回调方法。所以，需要我们告知容器，在哪个时间点来执行对象的自定义销 毁方法。

* **对于BeanFactory容器来说**。我们需要在独立应用程序的主程序退出之前，或者其他被认为是合 适的情况下（依照应用场景而定），调用ConfigurableBeanFactory提供的 destroySingletons()方法销毁容器中管理的所有singleton类型的对象实例。
* **对于ApplicationContext容器来说**。AbstractApplicationContext为我们提供了registerShutdownHook()方法，该方法底层使用标准的Runtime类的addShutdownHook()方式来调用相应bean对象的销毁逻辑，从而保证在Java虚拟机退出之前，这些singtleton类型的bean对象实例的自定义销毁逻辑会被执行。

至此，bean走完了它在容器中“光荣”的一生。 

## ApplicationContext

ApplicationContext除了拥有 BeanFactory支持的所有功能之外，还进一步扩展了基本容器的功能，包括BeanFactoryPostProcessor、BeanPostProcessor以及其他特殊类型bean的自动识别、容器启动后bean实例的自动初始化、 国际化的信息支持、容器内事件发布等。

### 统一资源加载策略

要搞清楚Spring为什么提供这么一个功能，还是从Java SE提供的标准类java.net.URL说起比较好。URL全名是Uniform Resource Locator（统一资源定位器），但多少有些名不副实的味道。

首先，说是统一资源定位，但基本实现却只限于网络形式发布的资源的查找和定位工作，基本上只提供了基于HTTP、FTP、File等协议（sun.net.www.protocol包下所支持的协议）的资源定位功能。

其次，从某些程度上来说，该类的功能职责划分不清，资源的查找和资源的表示没有一个清晰的界限。当前情况是，资源查找后返回的形式多种多样，没有一个统一的抽象。理想情况下，资源查找完成后，返回给客户端的应该是一个统一的资源抽象接口，客户端要对资源进行什么样的处理，应该由资源抽象接口来界定，而不应该成为资源的定位者和查找者同时要关心的事情。

Spring提出了一套基于org.springframework.core.io.Resource和 org.springframework.core.io.ResourceLoader接口的资源抽象和加载策略。

#### Spring中的Resource

Spring框架内部使用org.springframework.core.io.Resource接口作为所有资源的抽象和访问接口。该接口定义了7个方法，可以帮助我们查询资源状态、访问资源内容，甚至根据当前资源创建新的相对资源。

Resource接口可以根据资源的不同类型，或者资源所处的不同场合，给出相应的具体实现。

* ClassPathResource：该实现从Java应用程序的ClassPath中加载具体资源并进行封装，可以使用指定的类加载器（ClassLoader）或者给定的类进行资源加载。
* FileSystemResource：对java.io.File类型的封装，所以，我们可以以文件或者URL的形式对该类型资源进行访问，只要能跟File打的交道，基本上跟FileSystemResource也可以。
* UrlResource：通过java.net.URL进行的具体资源查找定位的实现类，内部委派URL进行具体的资源操作。
* ByteArrayResource：将字节（byte）数组提供的数据作为一种资源进行封装，如果通过InputStream形式访问该类型的资源，该实现会根据字节数组的数据，构造相应的ByteArrayInputStream并返回。

#### ResourceLoader，“更广义的URL” 

资源是有了，但如何去查找和定位这些资源，则应该是ResourceLoader的职责所在了。org.springframework.core.io.ResourceLoader接口是资源查找定位策略的统一抽象，具体的资源查找定位策略则由相应的ResourceLoader实现类给出。把ResourceLoader称作统一资源定位器或许才更恰当。其中最主要的就是Resource getResource(String location);方法，通过它，我们就可以根据指定的资源位置，定位到具体的资源实例。

##### 可用的ResourceLoader

* DefaultResourceLoader：
* FileSystemResourceLoader

##### 回顾与展望

![image-20220424145256331](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220424145256331.png)

#### ApplicationContext与ResourceLoader 

ApplicationContext继承了ResourcePatternResolver，当然就间接实现了ResourceLoader接口。所以，任何的ApplicationContext实现都可以看作是一个 ResourceLoader甚至ResourcePatternResolver。

![image-20220424145703244](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220424145703244.png)

AbstractApplicationContext类的内部声明有一个resourcePatternResolver，类型是ResourcePatternResolver，对应的实例类型为PathMatchingResourcePatternResolver 。ApplicationContext的实现类在作为ResourceLoader或者ResourcePatternResolver时候的行为，完全就是委派给了PathMatchingResourcePatternResolver和DefaultResourceLoader来做。

![image-20220424145941680](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220424145941680.png)

看作为ResourceLoader或者ResourcePatternResolver的ApplicationContext，可以做什么：

##### 扮演ResourceLoader的角色

通过ApplicationContext来加载任何Spring支持的Resource类型。

```java
ResourceLoader resourceLoader = new ClassPathXmlApplicationContext("magic-scope.xml");
// 或者
// ResourceLoader resourceLoader = new FileSystemXmlApplicationContext("配置文件路径");
Resource fileResource = resourceLoader.getResource("D:/spring21site/README");
System.out.println(fileResource instanceof ClassPathResource);
System.out.println(fileResource.exists());
Resource urlResource2 = resourceLoader.getResource("http://www.spring21.cn");
System.out.println(urlResource2 instanceof UrlResource);
```

##### ResourceLoader类型的注入

如果某个bean需要依赖于ResourceLoader来查找定位资源，我们可以为其注入容器中声明的某个具体的ResourceLoader实现。只需要让该bean实现ResourceLoaderAware或者ApplicationContextAware（本身是个resourceLoader）接口。

##### Resource类型的注入

如果一个bean需要依赖一个Resource，那么xml配置这个Resource，然后value为路径，Spring会自动识别它为Resource。

```java
@Data
public class ResourceBean {
    
    private Resource resource;
    
}
```

```xml
<bean id="resourceBean" class="book.ResourceBean">
    <property name="resource" value="book.txt"/>
</bean>
```

```java
ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("magic-scope.xml");
ResourceBean bean = applicationContext.getBean(ResourceBean.class);
System.out.println(bean.getResource().getFilename()); // 输出book.txt
```

ApplicationContext启动伊始，会通过一个org.springframework.beans.support.ResourceEditorRegistrar来注册Spring提供的针对Resource类型的PropertyEditor实现到容器中，这个PropertyEditor叫做 org.springframework.core.io.ResourceEditor。这样，ApplicationContext就可以正确地识别Resource类型的依赖了。

##### 在特定情况下，ApplicationContext的Resource加载行为

对于URL所接受的资源路径来说，通常开始都会有一个协议前缀，比如file:、http:、ftp: 等。Spring同样也支持这样类型的资源路径。ResourceLoader中增加了一种新 的资源路径协议——classpath:，ResourcePatternResolver又增加了一种——classpath*:。区别在于如果能够在classpath中找到多个指定的资源，则 返回多个。

ClassPathXmlApplicationContext在实例化的时候，即使没有指明 classpath:或者classpath*:等前缀，它会默认从classpath中加载bean定义配置文件。

FileSystemXmlApplicationContext则不同，如果我们像如下代码那样指定conf/ appContext.xml，它会尝试从文件系统中加载bean定义文件。

```java
ApplicationContext ctx = new FileSystemXmlApplicationContext("conf/appContext.xml");
```

### 国际化信息支持

对于Java中的国际化信息处理，主要涉及两个类，即java.util.Locale和java.util.ResourceBundle

#### Java SE 提供的国际化支持

##### Locale

不同的Locale代表不同的国家和地区，每个国家和地区在Locale这里都有相应的简写代码表示， 包括语言代码以及国家代码，这些代码是ISO标准代码。

##### ResourceBundle

ResourceBundle用来保存特定于某个Locale的信息。

#### MessageSource与ApplicationContext

Spring提供了三种MessageSource的实现，即StaticMessageSource、ResourceBundleMessageSource和ReloadableResourceBundleMessageSource。

### 容器内部事件发布

Spring的ApplicationContext容器提供的容器内事件发布功能，是通过提供一套基于Java SE标准自定义事件类而实现的。

### 多配置模块加载的简化

如果启动需要加载多个配置文件，通过ApplicationContext，我们只要以String[]形式传入这些配置文件所在的路径，即可构造并启动容器。

```java
String[] locations = new String[]{ "conf/magic-scope.xml", "conf/scope.xml", "conf/autowired.xml"};
ApplicationContext container = new FileSystemXmlApplicationContext(locations);
// 或者
//ApplicationContext container = new ClassPathXmlApplicationContext(locations);
```

如果通过在某一个主配置文件中使用分别加载其余的配置文件，然后容器就可以通过加载这个主配置文件，来加载其他的配置文件了。但使用的问题，在于需要时刻关注主配置文件与其他配置文件的一致性。

## IoC容器的扩展

### Spring 2.5的基于注解的依赖注入

#### 注解版的自动绑定（@Autowired）

##### 从自动绑定（autowire）到@Autowired

通过default-autowire来指定默认的自动绑定方式，也可以通过每个bean定义上的 autowire来指定每个bean定义各自的自动绑定方式，它们都是触发容器对相应对象给予依赖注入的标 志。

```xml
<beans default-autowire="byType">
    <bean id="newsProvider" class="..FXNewsProvider" autowire="byType"/>
    <bean id="djNewsListener" class="..DowJonesNewsListener"/>
    <bean id="djNewsPersister" class="..DowJonesNewsPersister"/>
</beans>
```

@Autowired是基于注解的依赖注入的核心注解，它的存在可以让容器知道需要为当前类注入哪些 依赖。

与原有的byType类型的自动绑定方式类似，@Autowired也是按照类型匹配进行依赖注入的，只不过，它要比byType更加灵活，也更加强大。@Autowired可以标注于类定义的多个位置，包括如下 几个。

* 域（Filed）或者说属性（Property）：不管它们声明的访问限制符是private、protected还是 public，只要标注了@Autowired，它们所需要的依赖注入需求就都能够被满足

```java
public class ResourceBean {

    @Autowired
    private Resource resource;

}
```

* 构造方法定义（Constructor）：标注于类的构造方法之上的@Autowired
* 方法定义（Method）：@Autowired不仅可以标注于传统的setter方法之上，而且还可以标注于任 意名称的方法定义之上，只要该方法定义了需要被注入的参数。

```java
public class ResourceBean {
    
    private Resource resource;

    @Autowired
    public  void initResource(Resource resource) {
        this.resource = resource;
    }

}
```

Spring在实例化bean定义的过程中，通过org.springframework.beans. factory.annotation.AutowiredAnnotationBeanPostProcessor来检查当前对象是否有@Autowired标注的依赖需要注入。

##### @Qualifier的陪伴

如果当前@Autowired标注的依赖在容器中找到两个或者多个同一类型的对象实例，就需要使用@Qualifier对依赖注入的条件做进一步限定，使得容器不再迷茫。

@Qualifier实际上是byName自动绑定的注解版

```java
public class ResourceBean {

    @Autowired
    @Qualifier("myResource")
    private Resource resource;

}
```

#### @Autowired之外的选择——使用JSR250 标注依赖注入关系

除了可以使用Spring提供的@Autowired和@Qualifier来 标注相应类定义之外，还可以使用JSR250的@Resource和@PostConstruct以及@PreDestroy对相应 类进行标注，这同样可以达到依赖注入的目的。

@Resource与@Autowired不同，它遵循的是byName自动绑定形式的行为准则。

```java
public class ResourceBean {
    @Resource
    private Resource resource;
}
```

@PostConstruct和@PreDestroy不是服务于依赖注入的，它们主要用于标注对象生命周期管理相关方法，这与Spring的InitializingBean和DisposableBean接口，以及配置项中的 init-method和destroy-method起到类似的作用。

如果想某个方法在对象实例化之后被调用，以做某些准备工作，或者想在对象销毁之前调用某个方法清理某些资源，那么就可以像我们这样，使用@PostConstruct和@PreDestroy来标注这些方法。

```java
public class LifecycleEnabledClass {

    @PostConstruct
    public void setUp() {

    }
    @PreDestroy
    public void destroy() {

    }
}
```

就像@Autowired需要AutowiredAnnotationBeanPostProcessor为它与IoC容器牵线搭桥一样，JSR250的这些注解也同样需要一个BeanPostProcessor帮助它们实现自身的价值。这个BeanPostProcessor就是org.springframework.context. annotation.CommonAnnotationBeanPostProcessor。

