#### BeanFactory

```java
/**
 * The root interface for accessing a Spring bean container.
 * This is the basic client view of a bean container;
 * further interfaces such as {@link ListableBeanFactory} and
 * {@link org.springframework.beans.factory.config.ConfigurableBeanFactory}
 * are available for specific purposes.
 *
 * <p>This interface is implemented by objects that hold a number of bean definitions,
 * each uniquely identified by a String name. Depending on the bean definition,
 * the factory will return either an independent instance of a contained object
 * (the Prototype design pattern), or a single shared instance (a superior
 * alternative to the Singleton design pattern, in which the instance is a
 * singleton in the scope of the factory). Which type of instance will be returned
 * depends on the bean factory configuration: the API is the same. Since Spring
 * 2.0, further scopes are available depending on the concrete application
 * context (e.g. "request" and "session" scopes in a web environment).
 *
 * <p>The point of this approach is that the BeanFactory is a central registry
 * of application components, and centralizes configuration of application
 * components (no more do individual objects need to read properties files,
 * for example). See chapters 4 and 11 of "Expert One-on-One J2EE Design and
 * Development" for a discussion of the benefits of this approach.
 *
 * <p>Note that it is generally better to rely on Dependency Injection
 * ("push" configuration) to configure application objects through setters
 * or constructors, rather than use any form of "pull" configuration like a
 * BeanFactory lookup. Spring's Dependency Injection functionality is
 * implemented using this BeanFactory interface and its subinterfaces.
 *
 * <p>Normally a BeanFactory will load bean definitions stored in a configuration
 * source (such as an XML document), and use the {@code org.springframework.beans}
 * package to configure the beans. However, an implementation could simply return
 * Java objects it creates as necessary directly in Java code. There are no
 * constraints on how the definitions could be stored: LDAP, RDBMS, XML,
 * properties file, etc. Implementations are encouraged to support references
 * amongst beans (Dependency Injection).
 *
 * <p>In contrast to the methods in {@link ListableBeanFactory}, all of the
 * operations in this interface will also check parent factories if this is a
 * {@link HierarchicalBeanFactory}. If a bean is not found in this factory instance,
 * the immediate parent factory will be asked. Beans in this factory instance
 * are supposed to override beans of the same name in any parent factory.
 *
 * <p>Bean factory implementations should support the standard bean lifecycle interfaces
 * as far as possible. The full set of initialization methods and their standard order is:
 * <ol>
 * <li>BeanNameAware's {@code setBeanName}
 * <li>BeanClassLoaderAware's {@code setBeanClassLoader}
 * <li>BeanFactoryAware's {@code setBeanFactory}
 * <li>EnvironmentAware's {@code setEnvironment}
 * <li>EmbeddedValueResolverAware's {@code setEmbeddedValueResolver}
 * <li>ResourceLoaderAware's {@code setResourceLoader}
 * (only applicable when running in an application context)
 * <li>ApplicationEventPublisherAware's {@code setApplicationEventPublisher}
 * (only applicable when running in an application context)
 * <li>MessageSourceAware's {@code setMessageSource}
 * (only applicable when running in an application context)
 * <li>ApplicationContextAware's {@code setApplicationContext}
 * (only applicable when running in an application context)
 * <li>ServletContextAware's {@code setServletContext}
 * (only applicable when running in a web application context)
 * <li>{@code postProcessBeforeInitialization} methods of BeanPostProcessors
 * <li>InitializingBean's {@code afterPropertiesSet}
 * <li>a custom init-method definition
 * <li>{@code postProcessAfterInitialization} methods of BeanPostProcessors
 * </ol>
 *
 * <p>On shutdown of a bean factory, the following lifecycle methods apply:
 * <ol>
 * <li>{@code postProcessBeforeDestruction} methods of DestructionAwareBeanPostProcessors
 * <li>DisposableBean's {@code destroy}
 * <li>a custom destroy-method definition
 * </ol>
 *
 * @author Rod Johnson
 * @author Juergen Hoeller
 * @author Chris Beams
 * @since 13 April 2001
 * @see BeanNameAware#setBeanName
 * @see BeanClassLoaderAware#setBeanClassLoader
 * @see BeanFactoryAware#setBeanFactory
 * @see org.springframework.context.ResourceLoaderAware#setResourceLoader
 * @see org.springframework.context.ApplicationEventPublisherAware#setApplicationEventPublisher
 * @see org.springframework.context.MessageSourceAware#setMessageSource
 * @see org.springframework.context.ApplicationContextAware#setApplicationContext
 * @see org.springframework.web.context.ServletContextAware#setServletContext
 * @see org.springframework.beans.factory.config.BeanPostProcessor#postProcessBeforeInitialization
 * @see InitializingBean#afterPropertiesSet
 * @see org.springframework.beans.factory.support.RootBeanDefinition#getInitMethodName
 * @see org.springframework.beans.factory.config.BeanPostProcessor#postProcessAfterInitialization
 * @see DisposableBean#destroy
 * @see org.springframework.beans.factory.support.RootBeanDefinition#getDestroyMethodName
 */
```

类的简介：

这个根接口的定义对获取Spring框架中bean的容器进行了规范，这是一个bean容器的基本客户端视图；像是ListableBeanFactory和ConfigurableBeanFacotry虽然都有着各自特定用途但他们都是继承或实现了这个接口（规范）。

这个接口的实现类都保存着一批bean definitions，且每个bean definition都有一个唯一的特定的字符串类型的标识。遵守这个BeanFactory接口规范的Bean工厂，会根据持有的bean definitions去获取一个scope为prototype的独立的实例，或者是一个共享的单例实例（一个比单例设计模式更高级的替代品），这个共享的单例只是在当前Bean工厂的范围内（spring对单例加了一个作用域），返回的是prototype类型的实例也好还是共享的singleton也罢，全是基于bean工厂怎样去配置：这个API都是一样的。从Spring框架2.0版本开始，有着更多scope的BeanFactory实现类加入进来（例如：web环境中的"request"和"session"作用域）。

BeanFactory接口定义的规范表明BeanFactory是应用程序组件的注册中心，他还集中了应用程序组件的配置（例如：不再需要为某个单独的对象去读取配置文件）。关于这种方法的好处，请参阅“专家一对一J2EE设计和开发”的第4章和第11章。

兄弟记着，通常依靠依赖注入（推入配置）的方式通过setter方法和constructor方法配置应用对象，要比任何形式的拉取配置要好（像是BeanFactory的查找）。Spring框架的依赖注入功能的实现都是靠这个BeanFactory接口的规范和他的子接口（规范）。

通常情况下，一个BeanFactory会将保存在配置资源（像是一个XML文档）的bean definitions加载起来，并使用org.springframework.beans包去配置这些beans。但是，一个实现类直接返回一个根据需要直接创建的Java对象，没有对怎样存储这些定义进行约束，可以存在[LDAP](https://baike.baidu.com/item/轻型目录访问协议/10493115?fromtitle=LDAP&fromid=2875565&fr=aladdin)、RDBMS（应该是关系型数据库管理系统）、XML或者是配置文件等等。BeanFactory的实现类鼓励去支持bean之间的引用（依赖注入，灵魂喔）。

对比ListableBeanFactory中的一些方法，如果一个Bean工厂是HierarchiacalBeanFactory类型的话，那么该Bean工厂中的所有该接口（也就是BeanFactory）定义的操作也都包含了对父工厂的检查。就是说，当一个bean在当前工厂实例中没有找到，那么就会立马去检查父的bean工厂。子的bean工厂会覆盖同名的父工厂存在的bean。

Bean工厂的实现类应该尽可能的遵循标准的bean的生命周期规范，这个一系列的完整的初始化方法及他们调用的顺序是：就是上面写的那些类及类中的方法。

```java
/**
 * Used to dereference a {@link FactoryBean} instance and distinguish it from
 * beans <i>created</i> by the FactoryBean. For example, if the bean named
 * {@code myJndiObject} is a FactoryBean, getting {@code &myJndiObject}
 * will return the factory, not the instance returned by the factory.
 */
String FACTORY_BEAN_PREFIX = "&";
```

变量简介：用于间接引用FactoryBean实例并将它区别于FactoryBean（工厂bean）创建的bean，举个栗子，如果一个bean被命名为myJndiObject，并且这个bean是一个FactoryBean（工厂bean），通过&myJndiObject获得的是这个工厂bean，不是这个工厂创建的实例。

```java
/**
 * Return an instance, which may be shared or independent, of the specified bean.
 * <p>This method allows a Spring BeanFactory to be used as a replacement for the
 * Singleton or Prototype design pattern. Callers may retain references to
 * returned objects in the case of Singleton beans.
 * <p>Translates aliases back to the corresponding canonical bean name.
 * Will ask the parent factory if the bean cannot be found in this factory instance.
 * @param name the name of the bean to retrieve
 * @return an instance of the bean
 * @throws NoSuchBeanDefinitionException if there is no bean with the specified name
 * @throws BeansException if the bean could not be obtained
 */
Object getBean(String name) throws BeansException;
```

方法介绍：返回一个给定名称的bean，可能是共享的也有可能是独立的。这个方法允许Spring的Bean工厂可以作为单例或者原型设计模式的替代。在单例bean情况下，调用者可以保留对返回对象的引用。将别名翻译回对应bean的经典名称（标准名称），在当前工厂无法找到对应bean的情况下，这个函数将会调用父工厂

```java
/**
 * Return an instance, which may be shared or independent, of the specified bean.
 * <p>Behaves the same as {@link #getBean(String)}, but provides a measure of type
 * safety by throwing a BeanNotOfRequiredTypeException if the bean is not of the
 * required type. This means that ClassCastException can't be thrown on casting
 * the result correctly, as can happen with {@link #getBean(String)}.
 * <p>Translates aliases back to the corresponding canonical bean name.
 * Will ask the parent factory if the bean cannot be found in this factory instance.
 * @param name the name of the bean to retrieve
 * @param requiredType type the bean must match; can be an interface or superclass（可以是一个接口或者超类）
 * @return an instance of the bean
 * @throws NoSuchBeanDefinitionException if there is no such bean definition
 * @throws BeanNotOfRequiredTypeException if the bean is not of the required type
 * @throws BeansException if the bean could not be created
 */
<T> T getBean(String name, Class<T> requiredType) throws BeansException;
```

方法介绍：和上面的方法一样根据指定名称和类型，会返回一个特定的共享或者是独立的bean。这个函数的行为和上面的getBean(String)很像，但是提供了一个如果这个bean并不是指定类型会抛出BeanNotOfRequiredTypeException异常的类型安全的方式。这就意味着ClassCastException不会在返回结果正确的情况下抛出，但是有可能会发生在使用getBean(String)的方式上（其实就是你通过名称得到了一个Bean，你想将这个Object类转换成对应的类型去调用里面特定的某些方法，由于这个函数指定了返回bean的类型，所以不会发生ClassCastException）。

```java
/**
 * Return an instance, which may be shared or independent, of the specified bean.
 * <p>Allows for specifying explicit constructor arguments / factory method arguments,
 * overriding the specified default arguments (if any) in the bean definition.
 * @param name the name of the bean to retrieve
 * @param args arguments to use when creating a bean instance using explicit arguments
 （explicit arguments 显示参数）
 * (only applied when creating a new instance as opposed to（as opposed to） retrieving an existing one)
 * @return an instance of the bean
 * @throws NoSuchBeanDefinitionException if there is no such bean definition
 * @throws BeanDefinitionStoreException if arguments have been given but
 * the affected bean isn't a prototype
 * @throws BeansException if the bean could not be created
 * @since 2.5
 */
Object getBean(String name, Object... args) throws BeansException;
```

方法介绍：和上面的方法一样，这个函数会返回一个特定的共享或者是独立的bean。允许指定显示构造函数的参数或者是工厂方法的参数，覆盖bean definition中指定的默认参数（在存在的情况下）。args参数是指使用显示构造器创建bean实例的参数（只有是在创建一个新的实例的时候使用，而不是获得一个已经存在的实例）

```java
/**
 * Return the bean instance that uniquely matches the given object type, if any.
 * <p>This method goes into（goes into 被用来或者是深入了解，这个地方感觉被用来这个翻译比较合适） {@link ListableBeanFactory} by-type lookup territory（领域）
 * but may also be translated into a conventional by-name lookup based on the name
 * of the given type. For more extensive retrieval（检索） operations across sets of beans,
 * use {@link ListableBeanFactory} and/or {@link BeanFactoryUtils}.
 * @param requiredType type the bean must match; can be an interface or superclass
 * @return an instance of the single bean matching the required type
 * @throws NoSuchBeanDefinitionException if no bean of the given type was found
 * @throws NoUniqueBeanDefinitionException if more than one bean of the given type was found
 * @throws BeansException if the bean could not be created
 * @since 3.0
 * @see ListableBeanFactory
 */
<T> T getBean(Class<T> requiredType) throws BeansException;
```

方法介绍：获得一个指定类型的唯一匹配的bean实例，如果存在的话。这个函数可以用在ListableBeanFactory的根据类型查找的领域，但是也能被转换成一个传统的按给定类型名称查找的实现方法。更广范围内的跨bean集的检索操作，要使用ListableBeanFactory和（或者）BeanFactoryUtils了。

```java
/**
 * Return an instance, which may be shared or independent, of the specified bean.
 * <p>Allows for specifying explicit constructor（explicit constructor 显示构造器） arguments / factory method arguments,
 * overriding the specified default arguments (if any) in the bean definition.
 * <p>This method goes into（go into 用于或者是深入了解） {@link ListableBeanFactory} by-type lookup territory（territory领域）
 * but may also be translated into a conventional by-name lookup based on the name
 * of the given type. For more extensive retrieval operations across sets of beans,
 * use {@link ListableBeanFactory} and/or {@link BeanFactoryUtils}.
 * @param requiredType type the bean must match; can be an interface or superclass
 * @param args arguments to use when creating a bean instance using explicit arguments
 * (only applied when creating a new instance as opposed to（as opposed to 而不是） retrieving an existing one)
 * @return an instance of the bean
 * @throws NoSuchBeanDefinitionException if there is no such bean definition
 * @throws BeanDefinitionStoreException if arguments have been given but
 * the affected bean isn't a prototype
 * @throws BeansException if the bean could not be created
 * @since 4.1
 */
<T> T getBean(Class<T> requiredType, Object... args) throws BeansException;
```

方法介绍：返回一个特定的共享的或者是独立的实例。允许指定显示构造器的或者是工厂方法的参数。覆盖bean definition中指定的默认参数（如果存在的话），这个函数可以用于ListableBeanFactory中按类型查找的方法中。也能被转换成一个基于给定类型名称的传统的按名称查找的实现方法。广范围内的跨bean集的检索操作，要使用ListableBeanFactory和（或者）BeanFactoryUtils了。

```java
/**
 * Return a provider for the specified bean, allowing for lazy on-demand retrieval
 * of instances, including availability and uniqueness options.
 * @param requiredType type the bean must match; can be an interface or superclass
 * @return a corresponding provider handle 相应的提供程序句柄（引用）
 * @since 5.1
 * @see #getBeanProvider(ResolvableType)
 */
<T> ObjectProvider<T> getBeanProvider(Class<T> requiredType);
```

方法介绍：返回一个指定bean的提供者，允许延迟按需检索的实例，包括可用性和唯一性选项（不懂）。

```java
/**
 * Return a provider for the specified bean, allowing for lazy on-demand retrieval
 * of instances, including availability and uniqueness options.
 * @param requiredType type the bean must match; can be a generic type declaration.（可以使一个泛型类型声明）
 * Note that collection types are not supported here（不支持集合类型）, in contrast to 	     reflective injection points（请注意，这里不支持集合类型，与反射注入点不同。）.
 * For programmatically retrieving a list of beans matching a
 * specific type, specify the actual bean type as an argument here and subsequently
 * use {@link ObjectProvider#orderedStream()} or its lazy streaming/iteration options.
 （通过编程的方式去检索匹配指定类型的bean的列表，请指定真正的bean类型作为参数并且接着使用ObjectProvider的orderedStream（）或者他的延迟 流/迭代 选项）
 * @return a corresponding provider handle
 * @since 5.1
 * @see ObjectProvider#iterator()
 * @see ObjectProvider#stream()
 * @see ObjectProvider#orderedStream()
 */
<T> ObjectProvider<T> getBeanProvider(ResolvableType requiredType);
```

方法介绍：返回一个指定bean的提供者，允许延迟按需检索的实例，包括可用性和唯一性选项（不懂）

```java
/**
 * Does this bean factory contain a bean definition or externally（外部） registered（注册） singleton
 * instance with the given name?
 * <p>If the given name is an alias, it will be translated back to the corresponding
 * canonical bean name.
 * <p>If this factory is hierarchical, will ask any parent factory if the bean cannot
 * be found in this factory instance.
 * <p>If a bean definition or singleton instance matching the given name is found,
 * this method will return {@code true} whether the named bean definition is concrete
 * or abstract, lazy or eager, in scope or not. Therefore, note that a {@code true}
 * return value from this method does not necessarily indicate that {@link #getBean}
 * will be able to obtain an instance for the same name.
 * @param name the name of the bean to query
 * @return whether a bean with the given name is present
 */
boolean containsBean(String name);
```

方法介绍：通过给定的名称，判断这个bean factory是否包含一个相应的bean definition或者是一个外部已经注册的单例。如果给定的名称是一个别名，那么它将被转换回对应的经典bean名称。如果这个工厂是一个分层的组织结构，那么如果当前工厂没有找到的话，会去询问父工厂是否存在。如果存在一个bean definition或者一个单例的实例匹配上了给定的名称的话，那么这个函数将会返回true，不论这个bean definition是一个具体类还是一个抽象类，亦或是延迟类或提前加载类，又有可能是在或者不在范围内（request，session，singleton，prototype），因此，请记住，这个方法即便返回true了，也不代表getBean函数能够获得到指定name的实例。

```java
/**
 * Is this bean a shared singleton? That is, will {@link #getBean} always
 * return the same instance?
 * <p>Note: This method returning {@code false} does not clearly indicate
 * independent instances. It indicates non-singleton instances, which may correspond
 * to a scoped bean as well. Use the {@link #isPrototype} operation to explicitly
 * check for independent instances.
 * <p>Translates aliases back to the corresponding canonical bean name.
 * Will ask the parent factory if the bean cannot be found in this factory instance.
 * @param name the name of the bean to query
 * @return whether this bean corresponds to a singleton instance
 * @throws NoSuchBeanDefinitionException if there is no bean with the given name
 * @see #getBean
 * @see #isPrototype
 */
boolean isSingleton(String name) throws NoSuchBeanDefinitionException;
```

方法介绍：判断给定名称的这个bean是不是一个单例bean，也就是说，getBean返回的实例会不会是同一个bean。注意，这个方法返回false时，并不明确的显示为独立实例，他表示也有可能对应一个作用域（request、session、prototype）的非单例实例，使用isPrototype()操作去显示检查独立实例。将别名转换回对应的经典的bean名称。如果当前factory没找到会去询问父工厂。

```java
/**
 * Is this bean a prototype? That is, will {@link #getBean} always return
 * independent instances?
 * <p>Note: This method returning {@code false} does not clearly indicate
 * a singleton object. It indicates non-independent instances, which may correspond
 * to a scoped bean as well. Use the {@link #isSingleton} operation to explicitly
 * check for a shared singleton instance.
 * <p>Translates aliases back to the corresponding canonical bean name.
 * Will ask the parent factory if the bean cannot be found in this factory instance.
 * @param name the name of the bean to query
 * @return whether this bean will always deliver independent instances
 * @throws NoSuchBeanDefinitionException if there is no bean with the given name
 * @since 2.0.3
 * @see #getBean
 * @see #isSingleton
 */
boolean isPrototype(String name) throws NoSuchBeanDefinitionException;
```

方法介绍：判断是否为原型bean，判断getBean是否每次都返回一个独立的实例。注意，这个方法返回false时并不明确的表明这个bean就是一个单例，他只表明这个是一个非独立的实例，这个实例有可能对应一个作用域。使用isSingleton操作显示检查一个共享单例实例。将别名转换回对应的经典的bean名称。如果当前factory没找到会去询问父工厂。

```java
/**
 * Check whether the bean with the given name matches the specified type.
 * More specifically, check whether a {@link #getBean} call for the given name
 * would return an object that is assignable to（assignable to 可认定的） the specified target type.
 * <p>Translates aliases back to the corresponding canonical bean name.
 * Will ask the parent factory if the bean cannot be found in this factory instance.
 * @param name the name of the bean to query
 * @param typeToMatch the type to match against (as a {@code ResolvableType})
 * @return {@code true} if the bean type matches,
 * {@code false} if it doesn't match or cannot be determined yet
 * @throws NoSuchBeanDefinitionException if there is no bean with the given name
 * @since 4.2
 * @see #getBean
 * @see #getType
 */
boolean isTypeMatch(String name, ResolvableType typeToMatch) throws NoSuchBeanDefinitionException;
```

方法介绍：检查一个给定名称的的bean是否匹配指定的类型。描述的更精确一些就是，检查通过指定名称调用的getBean返回的对象是否为指定的类型。将别名转换回对应的经典的bean名称。如果当前factory没找到会去询问父工厂。

```java
/**
 * Check whether the bean with the given name matches the specified type.
 * More specifically, check whether a {@link #getBean} call for the given name
 * would return an object that is assignable to the specified target type.
 * <p>Translates aliases back to the corresponding canonical bean name.
 * Will ask the parent factory if the bean cannot be found in this factory instance.
 * @param name the name of the bean to query
 * @param typeToMatch the type to match against (as a {@code Class})
 * @return {@code true} if the bean type matches,
 * {@code false} if it doesn't match or cannot be determined yet
 * @throws NoSuchBeanDefinitionException if there is no bean with the given name
 * @since 2.0.1
 * @see #getBean
 * @see #getType
 */
boolean isTypeMatch(String name, Class<?> typeToMatch) throws NoSuchBeanDefinitionException;
```

方法介绍：检查这个指定名称的bean是否与指定的类型相匹配。更具体的描述就是，检查一个通过名称调用getBean获得的对象是不是指定类型。将别名转换回对应的经典的bean名称。如果当前factory没找到会去询问父工厂。

```java
/**
 * Determine the type of the bean with the given name. More specifically,
 * determine the type of object that {@link #getBean} would return for the given name.
 * <p>For a {@link FactoryBean}, return the type of object that the FactoryBean creates,
 * as exposed by {@link FactoryBean#getObjectType()}. This may lead to the initialization
 * of a previously uninitialized {@code FactoryBean} (see {@link #getType(String, boolean)}).
 * <p>Translates aliases back to the corresponding canonical bean name.
 * Will ask the parent factory if the bean cannot be found in this factory instance.
 * @param name the name of the bean to query
 * @return the type of the bean, or {@code null} if not determinable
 * @throws NoSuchBeanDefinitionException if there is no bean with the given name
 * @since 1.1.2
 * @see #getBean
 * @see #isTypeMatch
 */
@Nullable
Class<?> getType(String name) throws NoSuchBeanDefinitionException;
```

方法介绍：确定给定名称的bean的类型。更具体一些的描述就是，确定通过指定名称调用getBean方法返回的对象的类型。对于一个工厂bean，返回的对象类型是工厂bean创建的对象类型，和FactoryBean的getObjectType()的返回值一样（as exposed by不明白啥意思，暂时是这样理解的，请大佬指教）。这会导致前一个未初始化的FactoryBean的初始化操作。将别名转换回对应的经典的bean名称。如果当前factory没找到会去询问父工厂。

```java
/**
 * Determine the type of the bean with the given name. More specifically,
 * determine the type of object that {@link #getBean} would return for the given name.
 * <p>For a {@link FactoryBean}, return the type of object that the FactoryBean creates,
 * as exposed by {@link FactoryBean#getObjectType()}. Depending on the
 * {@code allowFactoryBeanInit} flag, this may lead to the initialization of a previously
 * uninitialized {@code FactoryBean} if no early type information is available.
 * <p>Translates aliases back to the corresponding canonical bean name.
 * Will ask the parent factory if the bean cannot be found in this factory instance.
 * @param name the name of the bean to query
 * @param allowFactoryBeanInit whether a {@code FactoryBean} may get initialized
 * just for the purpose of determining its object type
 * @return the type of the bean, or {@code null} if not determinable
 * @throws NoSuchBeanDefinitionException if there is no bean with the given name
 * @since 5.2
 * @see #getBean
 * @see #isTypeMatch
 */
@Nullable
Class<?> getType(String name, boolean allowFactoryBeanInit) throws NoSuchBeanDefinitionException;
```

方法介绍：确定指定名称的bean的类型，更准确一点的描述是，确定通过指定名称调用的getBean返回的对象类型，对于一个FactoryBean来说，返回的对象类型是其创建的对象类型，和FactoryBean的getObjectType()的返回值一样（as exposed by不明白啥意思，暂时是这样理解的，请大佬指教）。如果没有可获取的早期的类型信息，要不要初始化先前的FactoryBean，这取决于allowFactoryBeanInit标志。将别名转换回对应的经典的bean名称。如果当前factory没找到会去询问父工厂。

```java
/**
 * Return the aliases for the given bean name, if any.
 * All of those aliases point to the same bean when used in a {@link #getBean} call.
 * <p>If the given name is an alias, the corresponding original bean name
 * and other aliases (if any) will be returned, with the original bean name
 * being the first element in the array.
 * <p>Will ask the parent factory if the bean cannot be found in this factory instance.
 * @param name the bean name to check for aliases
 * @return the aliases, or an empty array if none
 * @see #getBean
 */
String[] getAliases(String name);
```

方法介绍：返回指定名称bean的别名，如果存在的话，当使用getBean获得的对象obj，那么这个函数所有返回的别名都指向同一个bean也就是这个obj。如果入参就是一个别名，那么对应的原始的bean名称和其他所有存在的别名都会一并返回，并且原始名称会放在数组的第一位，如果当前factory没找到会去询问父工厂。

