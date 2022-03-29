### Spring如何根据BeanDefinition实例化为bean，并管理这些bean？

#### 一、实例化bean

下面是XML配置的Car，Spring是如何生成Car实例的呢？

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="car" class="**.Car" scope="prototype"/>

</beans>
```

``` JAVA
public class Car {

    private String carNum;

    public void setCarNum(String carNum) {
        this.carNum = carNum;
    }

    public String getCarNum() {
        return carNum;
    }
}

public class LoadBeanPlay {

    public static void main(String[] args){
        // 获得的容器container已经加载好BeanDefinition
        BeanFactory container = new XmlBeanFactory(new ClassPathResource("scope.xml"));
        Car car = container.getBean("car", Car.class);
    } 

}
```

bean的实例化（不包括其他的管理）的工作由以下类共同完成。

![image-20210803230535231](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210803230535231.png)

大概流程：

![image-20210806231342953](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210806231342953.png)

代码流程

```java
public abstract class AbstractAutowireCapableBeanFactory extends AbstractBeanFactory
		 implements AutowireCapableBeanFactory {
    
    // 实现的AbstractBeanFactory.createBean的抽象方法
    @Override
	protected Object createBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
			throws BeanCreationException {

		if (logger.isTraceEnabled()) {
			logger.trace("Creating instance of bean '" + beanName + "'");
		}
		RootBeanDefinition mbdToUse = mbd;

		// Make sure bean class is actually resolved at this point, and
		// clone the bean definition in case of a dynamically resolved Class
		// which cannot be stored in the shared merged bean definition.
		Class<?> resolvedClass = resolveBeanClass(mbd, beanName);
		if (resolvedClass != null && !mbd.hasBeanClass() && mbd.getBeanClassName() != null) {
			mbdToUse = new RootBeanDefinition(mbd);
			mbdToUse.setBeanClass(resolvedClass);
		}

		// Prepare method overrides.
		try {
			mbdToUse.prepareMethodOverrides();
		}
		catch (BeanDefinitionValidationException ex) {
			throw new BeanDefinitionStoreException(mbdToUse.getResourceDescription(),
					beanName, "Validation of method overrides failed", ex);
		}

		try {
			// Give BeanPostProcessors a chance to return a proxy instead of the target bean instance.
			Object bean = resolveBeforeInstantiation(beanName, mbdToUse);
			if (bean != null) {
				return bean;
			}
		}
		catch (Throwable ex) {
			throw new BeanCreationException(mbdToUse.getResourceDescription(), beanName,
					"BeanPostProcessor before instantiation of bean failed", ex);
		}

		try {
			// 实例化bean的流程在此方法内
			Object beanInstance = doCreateBean(beanName, mbdToUse, args);
			if (logger.isTraceEnabled()) {
				logger.trace("Finished creating instance of bean '" + beanName + "'");
			}
			return beanInstance;
		}
		catch (BeanCreationException | ImplicitlyAppearedSingletonException ex) {
			// A previously detected exception with proper bean creation context already,
			// or illegal singleton state to be communicated up to DefaultSingletonBeanRegistry.
			throw ex;
		}
		catch (Throwable ex) {
			throw new BeanCreationException(
					mbdToUse.getResourceDescription(), beanName, "Unexpected exception during bean creation", ex);
		}
	}
    
    
    protected Object doCreateBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
			throws BeanCreationException {

		// Instantiate the bean.
		// 1.实例化bean
		BeanWrapper instanceWrapper = null;
		if (mbd.isSingleton()) {
			instanceWrapper = this.factoryBeanInstanceCache.remove(beanName);
		}
		if (instanceWrapper == null) {
			instanceWrapper = createBeanInstance(beanName, mbd, args);
		}
		Object bean = instanceWrapper.getWrappedInstance();
		Class<?> beanType = instanceWrapper.getWrappedClass();
		if (beanType != NullBean.class) {
			mbd.resolvedTargetType = beanType;
		}

		// Allow post-processors to modify the merged bean definition.
		synchronized (mbd.postProcessingLock) {
			if (!mbd.postProcessed) { // 如果没有被处理过，则进行处理
				try {
					applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);
				}
				catch (Throwable ex) {
					throw new BeanCreationException(mbd.getResourceDescription(), beanName,
							"Post-processing of merged bean definition failed", ex);
				}
				mbd.postProcessed = true;
			}
		}

		// Eagerly cache singletons to be able to resolve circular references
		// even when triggered by lifecycle interfaces like BeanFactoryAware.
		boolean earlySingletonExposure = (mbd.isSingleton() && this.allowCircularReferences &&
				isSingletonCurrentlyInCreation(beanName));
		if (earlySingletonExposure) {
			if (logger.isTraceEnabled()) {
				logger.trace("Eagerly caching bean '" + beanName +
						"' to allow for resolving potential circular references");
			}
			// 存在循环依赖，提前把bean临时加入到singletonFactory中。
			addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));
		}

		// Initialize the bean instance.
		// 初始化bean实例：此时bean已经创建了。和instantiate实例化是有区别的。
		Object exposedObject = bean;
		try {
			// 2.设置对象属性
			populateBean(beanName, mbd, instanceWrapper);
			// 3-7
			exposedObject = initializeBean(beanName, exposedObject, mbd);
		}
		catch (Throwable ex) {
			if (ex instanceof BeanCreationException && beanName.equals(((BeanCreationException) ex).getBeanName())) {
				throw (BeanCreationException) ex;
			}
			else {
				throw new BeanCreationException(
						mbd.getResourceDescription(), beanName, "Initialization of bean failed", ex);
			}
		}

		if (earlySingletonExposure) {
			Object earlySingletonReference = getSingleton(beanName, false);
			if (earlySingletonReference != null) {
				if (exposedObject == bean) {
					exposedObject = earlySingletonReference;
				}
				else if (!this.allowRawInjectionDespiteWrapping && hasDependentBean(beanName)) {
					String[] dependentBeans = getDependentBeans(beanName);
					Set<String> actualDependentBeans = new LinkedHashSet<>(dependentBeans.length);
					for (String dependentBean : dependentBeans) {
						if (!removeSingletonIfCreatedForTypeCheckOnly(dependentBean)) {
							actualDependentBeans.add(dependentBean);
						}
					}
					if (!actualDependentBeans.isEmpty()) {
						throw new BeanCurrentlyInCreationException(beanName,
								"Bean with name '" + beanName + "' has been injected into other beans [" +
								StringUtils.collectionToCommaDelimitedString(actualDependentBeans) +
								"] in its raw version as part of a circular reference, but has eventually been " +
								"wrapped. This means that said other beans do not use the final version of the " +
								"bean. This is often the result of over-eager type matching - consider using " +
								"'getBeanNamesForType' with the 'allowEagerInit' flag turned off, for example.");
					}
				}
			}
		}

		// Register bean as disposable.
		// 8.注册必要的Destruction相关回调接口
		try {
			registerDisposableBeanIfNecessary(beanName, bean, mbd);
		}
		catch (BeanDefinitionValidationException ex) {
			throw new BeanCreationException(
					mbd.getResourceDescription(), beanName, "Invalid destruction signature", ex);
		}
		// 返回客户端使用，用完后，检查bean是否实现了Disposable接口，是否配置自定义destroy方法。
		return exposedObject;
	}
    
    
    // 3-7
    protected Object initializeBean(String beanName, Object bean, @Nullable RootBeanDefinition mbd) {
		if (System.getSecurityManager() != null) {
			AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
				invokeAwareMethods(beanName, bean);
				return null;
			}, getAccessControlContext());
		}
		else {
			// 3.检查aware相关接口，并将Aware规定的依赖注入给当前实例
			invokeAwareMethods(beanName, bean);
		}

		Object wrappedBean = bean;
		if (mbd == null || !mbd.isSynthetic()) {
			// 4.bean post前置处理
			wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
		}

		try {
			// 5-6.检查是否配置有自定义init method
			invokeInitMethods(beanName, wrappedBean, mbd);
		}
		catch (Throwable ex) {
			throw new BeanCreationException(
					(mbd != null ? mbd.getResourceDescription() : null),
					beanName, "Invocation of init method failed", ex);
		}
		if (mbd == null || !mbd.isSynthetic()) {
			// 7. bean post后置处理
			wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
		}

		return wrappedBean;
	}
    
    
    // 5-6
    protected void invokeInitMethods(String beanName, Object bean, @Nullable RootBeanDefinition mbd)
			throws Throwable {

		// 5. 检查是否实现了InitializingBean接口，已决定是否调用afterPropertiesSet方法
		boolean isInitializingBean = (bean instanceof InitializingBean);
		if (isInitializingBean && (mbd == null || !mbd.isExternallyManagedInitMethod("afterPropertiesSet"))) {
			if (logger.isTraceEnabled()) {
				logger.trace("Invoking afterPropertiesSet() on bean with name '" + beanName + "'");
			}
			if (System.getSecurityManager() != null) {
				try {
					AccessController.doPrivileged((PrivilegedExceptionAction<Object>) () -> {
						((InitializingBean) bean).afterPropertiesSet();
						return null;
					}, getAccessControlContext());
				}
				catch (PrivilegedActionException pae) {
					throw pae.getException();
				}
			}
			else {
				((InitializingBean) bean).afterPropertiesSet();
			}
		}

		if (mbd != null && bean.getClass() != NullBean.class) {
			// 6.执行自身的init method方法
			String initMethodName = mbd.getInitMethodName();
			if (StringUtils.hasLength(initMethodName) &&
					!(isInitializingBean && "afterPropertiesSet".equals(initMethodName)) &&
					!mbd.isExternallyManagedInitMethod(initMethodName)) {
				invokeCustomInitMethod(beanName, bean, mbd);
			}
		}
	}
   
    
}
```





#### 二、管理实例化后的bean

下面的类主要是对实例化后的bean进行管理，如注册、获取、销毁。

![image-20210802221530736](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210802221530736.png)

其中BeanDefinitionRegistry持有的是BeanDefinition,它是生产bean的原材料供应商。真正产生bean的是SingletonBeanRegistry。真正创建bean实例的步骤在AbstractAutowireCapableBeanFactory中，它会调用AbstractAutoProxyCreator的createProxy方法，来生成一个bean的代理，因为引入了postBeanProcessor机制。最后，AbstractAutowireCapableBeanFactory会把生成的bean保存到DefaultSingletonBeanRegistry的singletonObjects中。

1.SingletonBeanRegistry

```java
public interface SingletonBeanRegistry {

	// 把单例以<beanName, bean>的形式注册起来
	void registerSingleton(String beanName, Object singletonObject);

	@Nullable
	// 通过名字获取实例
	Object getSingleton(String beanName);

	// beanName是否已经被注册
	boolean containsSingleton(String beanName);

	// 获取所有被注册的beanName
	String[] getSingletonNames();

	// 当前被注册的数量
	int getSingletonCount();

	// 返回此注册表使用的单例互斥锁（用于外部协作者）。
	Object getSingletonMutex();

}
```

2.DefaultSingletonBeanRegistry，这个类主要实现了对单例bean实例CRUD，当然了，这个C，是指别人create后，然后交给它来管理的。

```java
public class DefaultSingletonBeanRegistry extends SimpleAliasRegistry implements SingletonBeanRegistry {

	/** Maximum number of suppressed exceptions to preserve. */
	private static final int SUPPRESSED_EXCEPTIONS_LIMIT = 100;


	/** Cache of singleton objects: bean name to bean instance. */
	// 容器内部当前已注册的单例bean
	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

	/** Cache of singleton factories: bean name to ObjectFactory. */
	// 容器内部当前已注册的单例工厂
	private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);

	/** Cache of early singleton objects: bean name to bean instance. */
	//
	private final Map<String, Object> earlySingletonObjects = new ConcurrentHashMap<>(16);

	/** Set of registered singletons, containing the bean names in registration order. */
	// 按注册顺序，保存已经注册的单例（bean和bean工厂）名字
	private final Set<String> registeredSingletons = new LinkedHashSet<>(256);

	/** Names of beans that are currently in creation. */
	// 当前正在创建的bean的名称。
	private final Set<String> singletonsCurrentlyInCreation =
			Collections.newSetFromMap(new ConcurrentHashMap<>(16));

	/** Names of beans currently excluded from in creation checks. */
	// 当前从创建检查中排除的bean的名称
	private final Set<String> inCreationCheckExclusions =
			Collections.newSetFromMap(new ConcurrentHashMap<>(16));

	/** Collection of suppressed Exceptions, available for associating related causes. */
	@Nullable
	private Set<Exception> suppressedExceptions;

	/** Flag that indicates whether we're currently within destroySingletons. */
	// 指示我们当前是否在destroySingletons中的标志。
	private boolean singletonsCurrentlyInDestruction = false;

	/** Disposable bean instances: bean name to disposable instance. */
	// 一次性 bean 实例：<beanName : 一次性实例>。
	private final Map<String, Object> disposableBeans = new LinkedHashMap<>();

	/** Map between containing bean names: bean name to Set of bean names that the bean contains. */
	// 包含 bean 名称之间的映射：bean 名称到 bean 包含的 bean 名称集。
	private final Map<String, Set<String>> containedBeanMap = new ConcurrentHashMap<>(16);

	/** Map between dependent bean names: bean name to Set of dependent bean names. */
	// 依赖 bean 名称之间的映射,bean name to 一组依赖 bean 名称。
    // A依赖B和C，D依赖B，那么在dependentBeanMap中保存的是<b,{a,d}>,<c,{a}>
	// 所以它的key是被依赖主体，value是依赖主体的集合，可以看到某个对象被哪些对象依赖
	// 举个例子，可以通过debug来确定。
	private final Map<String, Set<String>> dependentBeanMap = new ConcurrentHashMap<>(64);

	/** Map between depending bean names: bean name to Set of bean names for the bean's dependencies. */
	// A依赖B和C，D依赖B，那么在dependenciesForBeanMap中保存的是<a,{b,c}>,<d,{b}>
	// 所以它的key是依赖主体，value是被依赖主体的集合，可以看到某个对象依赖哪些对象。
	// 举个例子，可以通过debug来确定。
	private final Map<String, Set<String>> dependenciesForBeanMap = new ConcurrentHashMap<>(64);

    
}

```

小疑惑：ConfigurableBeanFactory接口的destroySingletons()方法，被DefaultSingletonBeanRegistry实现，但为什么DefaultSingletonBeanRegistry的代码中没有implements ConfigurableBeanFactory？解答：因为AbstractBeanFactory实现了ConfigurableBeanFactory的destroySingletons方法，并且间接继承了DefaultSingletonBeanRegistry类，而AbstractBeanFactory的destroySingletons方法是调用了super.destroySingletons()的，所以destroySingletons的真正实现还是在DefaultSingletonBeanRegistry中，因此可以说DefaultSingletonBeanRegistry实现了ConfigurableBeanFactory接口的某些方法。子类实现一个接口，但是这个接口的真正实现可以在父类中。

FactoryBeanRegistrySupport：这个类在继承DefaultSingletonBeanRegistry基础上，还增加一个功能，就是对那些由FactoryBean产生的单例对象的管理

```java
public abstract class FactoryBeanRegistrySupport extends DefaultSingletonBeanRegistry {
    // FactoryBeans 创建的单例对象的缓存：FactoryBean名称到对象。
	private final Map<String, Object> factoryBeanObjectCache = new ConcurrentHashMap<>(16);
    
    
    @Override
	protected void removeSingleton(String beanName) {
		synchronized (getSingletonMutex()) {
            // 把父容器的单例bean移除
			super.removeSingleton(beanName);
            // 把自身携带的由FactoryBean产生的单例移除
			this.factoryBeanObjectCache.remove(beanName);
		}
	}
    
    
    @Override
	protected void clearSingletonCache() {
		synchronized (getSingletonMutex()) {
			super.clearSingletonCache();
			this.factoryBeanObjectCache.clear();
		}
	}
}

```

AbstractBeanFactory：这个类管理着容器产生的bean，它里面有个抽象方法createBean，当查找父容器和自身后，都没有找到合适的bean实例，此时，就会调用createBean，这个方法由它的子类AbstractAutowireCapableBeanFactory来实现，这一步会和步骤一进行接轨(AbstractBeanFactory的doGetBean调用了createBean)。它们俩共同完成bean的管理。







