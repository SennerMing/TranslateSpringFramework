#### SingletonBeanRegistry

```
Interface that defines a registry for shared bean instances.Can be implemented by {@link org.springframework.beans.factory.BeanFactory} implementations in order to expose their singleton management facility in a uniform manner.
@see ConfigurableBeanFactory
@see org.springframework.beans.factory.support.DefaultSingletonBeanRegistry
@see org.springframework.beans.factory.support.AbstractBeanFactory
```

是为共享实例的注册中心定义的规范，可以被BeanFactory的实现类去实现，这样这些个实现类就具备了对单例统一管理的能力。实现类可以参见@see的那几个类或接口。

```java
/**
 * Register the given existing object as singleton in the bean registry,
 * under the given bean name.
 * <p>The given instance is supposed to be fully initialized; the registry
 * will not perform any initialization callbacks (in particular, it won't
 * call InitializingBean's {@code afterPropertiesSet} method).
 * The given instance will not receive any destruction callbacks
 * (like DisposableBean's {@code destroy} method) either.
 * <p>When running within a full BeanFactory: <b>Register a bean definition
 * instead of an existing instance if your bean is supposed to receive
 * initialization and/or destruction callbacks.</b>
 * <p>Typically invoked during registry configuration, but can also be used
 * for runtime registration of singletons. As a consequence, a registry
 * implementation should synchronize singleton access; it will have to do
 * this anyway if it supports a BeanFactory's lazy initialization of singletons.
 * @param beanName the name of the bean
 * @param singletonObject the existing singleton object
 * @see org.springframework.beans.factory.InitializingBean#afterPropertiesSet
 * @see org.springframework.beans.factory.DisposableBean#destroy
 * @see org.springframework.beans.factory.support.BeanDefinitionRegistry#registerBeanDefinition
 */
void registerSingleton(String beanName, Object singletonObject);
```

方法简介：将传入singletonObject以beanName为键注册到Bean的注册中心。

传入的singletonObject应该是一个完全初始化的实例；注册中心不会对该Bean再执行任何初始化操作的回调，特别是不会调用InitializingBean的afterPropertiesSet方法。

传入的singletonObject也不会接收到任何销毁的回调，像是DisposableBean的destroy方法。

当在一个完整的BeanFactory中运行时：如果你的Bean是一个需要接收初始化操作 和/或者 销毁回调的时候，请注册一个Bean Definition而不是一个已经存在的实例。

这个registerSingleton方法通常实在注册中心配置阶段被调用，在运行时注册单例也可能会被用到。因此（As a consequence，虽说我翻译成因此，但是我并不知道什么原因，如果有大佬知道原因或者是小明翻译的不对，请不吝赐教）一个注册中心的实现类应该在单例的获取上进行（同步/加锁）；如果这个实现类中支持BeanFactory的单例懒加载（也就是我们常说的延迟加载），那么它无论如何也要这样做（同步/加锁）。



```java
/**
 * Return the (raw) singleton object registered under the given name.
 * <p>Only checks already instantiated singletons; does not return an Object
 * for singleton bean definitions which have not been instantiated yet.
 * <p>The main purpose of this method is to access manually registered singletons
 * (see {@link #registerSingleton}). Can also be used to access a singleton
 * defined by a bean definition that already been created, in a raw fashion.
 * <p><b>NOTE:</b> This lookup method is not aware of FactoryBean prefixes or aliases.
 * You need to resolve the canonical bean name first before obtaining the singleton instance.
 * @param beanName the name of the bean to look for
 * @return the registered singleton object, or {@code null} if none found
 * @see ConfigurableListableBeanFactory#getBeanDefinition
 */
@Nullable
Object getSingleton(String beanName);
```

方法简介：通过传入的beanName获得已经注册的（原始）单例对象

该方法的实现只检查已经初始化完成的单例，不会返回还没有被初始化完成的单例bean定义的对象。

这个方法的主要目的是获取手动注册的单例（可以参考registerSingleton，就上面第一个方法）。这个方法也可以用于获取由已经以一种原始方式创建的bean definition定义下的单例（真鸡儿拗口）。

这个函数的查找单例的方式它不知道FactoryBean的前缀名（什么#号啦）或者别名。需要在获得单例对象之前自己解析这些以经典方式命名的beanName；



```java
/**
 * Check if this registry contains a singleton instance with the given name.
 * <p>Only checks already instantiated singletons; does not return {@code true}
 * for singleton bean definitions which have not been instantiated yet.
 * <p>The main purpose of this method is to check manually registered singletons
 * (see {@link #registerSingleton}). Can also be used to check whether a
 * singleton defined by a bean definition has already been created.
 * <p>To check whether a bean factory contains a bean definition with a given name,
 * use ListableBeanFactory's {@code containsBeanDefinition}. Calling both
 * {@code containsBeanDefinition} and {@code containsSingleton} answers
 * whether a specific bean factory contains a local bean instance with the given name.
 * <p>Use BeanFactory's {@code containsBean} for general checks whether the
 * factory knows about a bean with a given name (whether manually registered singleton
 * instance or created by bean definition), also checking ancestor factories.
 * <p><b>NOTE:</b> This lookup method is not aware of FactoryBean prefixes or aliases.
 * You need to resolve the canonical bean name first before checking the singleton status.
 * @param beanName the name of the bean to look for
 * @return if this bean factory contains a singleton instance with the given name
 * @see #registerSingleton
 * @see org.springframework.beans.factory.ListableBeanFactory#containsBeanDefinition
 * @see org.springframework.beans.factory.BeanFactory#containsBean
 */
boolean containsSingleton(String beanName);
```

方法简介：这个containsSingleton函数被用来检查这个注册中心是否包含传入的beanName命名的单例实例。

跟上面一样，该方法的实现只检查已经初始化完成的单例，单例bean定义还没初始化完成是不会返回true的。

这个函数的主要目的是检查哪些手动注册的单例（registerSingleton，没错，还是它，就上面第一个函数）。也可以被用来检查一个由bean definition定义的单例的状态是否是已经被创建。

检验一个bean工厂是否包含名称为传入的beanName的bean definition，需要使用ListableBeanDefinition的containsBeanDefinition

检验一个bean工厂是否包含名称为传入的beanName的一个本地bean实例，就需要同时使用到ListableBeanDefinition的containsBeanDefinition和这个containsSingleton两个方法了。

通过使用BeanFactory的containsBean进行一个全体的检查，看看这个BeanFactory是否知道这个名为beanName的bean（确认一下这个到底是已经注册完成的单例实例还是一个创建好的单例的bean definition），同时也检查了其祖先工厂。

这个containsSingleton方法不知道FactoryBean的前缀（比如#号啦）或者bean的别名。在检查给定beanName的单例状态前，需要你自己去解析经典的原始的bean名称。



```java
/**
 * Return the names of singleton beans registered in this registry.
 * <p>Only checks already instantiated singletons; does not return names
 * for singleton bean definitions which have not been instantiated yet.
 * <p>The main purpose of this method is to check manually registered singletons
 * (see {@link #registerSingleton}). Can also be used to check which singletons
 * defined by a bean definition have already been created.
 * @return the list of names as a String array (never {@code null})
 * @see #registerSingleton
 * @see org.springframework.beans.factory.support.BeanDefinitionRegistry#getBeanDefinitionNames
 * @see org.springframework.beans.factory.ListableBeanFactory#getBeanDefinitionNames
 */
String[] getSingletonNames();
```

方法简介：返回注册中心被注册单例bean的名称

跟上面一样，只检查已经初始化完成的单例，不会返回还未通过bean definitions初始化完成的单例名称。

这个getSingletonNames方法的主要目的是检查手动注册的单例（可以参照registerSingleton，没错还是最上面那个方法）。也可以被用来检查由bean definition定义的单例是否已经被创建完成。



```java
/**
 * Return the number of singleton beans registered in this registry.
 * <p>Only checks already instantiated singletons; does not count
 * singleton bean definitions which have not been instantiated yet.
 * <p>The main purpose of this method is to check manually registered singletons
 * (see {@link #registerSingleton}). Can also be used to count the number of
 * singletons defined by a bean definition that have already been created.
 * @return the number of singleton beans
 * @see #registerSingleton
 * @see org.springframework.beans.factory.support.BeanDefinitionRegistry#getBeanDefinitionCount
 * @see org.springframework.beans.factory.ListableBeanFactory#getBeanDefinitionCount
 */
int getSingletonCount();
```

方法简介：用于获取已经被注册完成的单例bean的数量

跟上面一样，只检查已经初始化完成的单例，不会将还未通过bean definitions初始化完成的单例计入。

这个getSingletonCount方法的主要目的是检查手动注册的单例（可以参照registerSingleton，没错还是最上面那个方法）。也可以被用来由bean definition定义的单例是否已经被创建完成的计数工作。



```java
/**
 * Return the singleton mutex used by this registry (for external collaborators).
 * @return the mutex object (never {@code null})
 * @since 4.2
 */
Object getSingletonMutex();
```

方法简介：返回此注册表使用的单例互斥体（对于外部协作者）。

