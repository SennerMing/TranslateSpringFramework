#### ConfigurableApplicationContext

```java
/**
 * SPI interface to be implemented by most if not all application contexts.
 * Provides facilities to configure an application context in addition
 * to the application context client methods in the
 * {@link org.springframework.context.ApplicationContext} interface.
 *
 * <p>Configuration and lifecycle methods are encapsulated here to avoid
 * making them obvious to ApplicationContext client code. The present
 * methods should only be used by startup and shutdown code.
 *
 * @author Juergen Hoeller
 * @author Chris Beams
 * @author Sam Brannen
 * @since 03.11.2003
 */
if not：即使不；要是不
facilities：工具；特别装置  
in addition to：另外；除...之外；加之
encapsulated：简述；概括；压缩；封装
```

类介绍：[SPI](https://www.cnblogs.com/jy107600/p/11464985.html)接口将会被绝大多数即使不是全部的应用上下文。除了在ApplicationContext接口中应用上下文客户端方法外，还提供用来配置应用上下文的工具。

配置和生命周期方法在此封装，以避免他们对ApplicationContext客户端代码显得显而易见。当前方法应该仅被启动和关闭代码使用。

```java
/**
 * Any number of these characters are considered delimiters between
 * multiple context config paths in a single String value.
 * @see org.springframework.context.support.AbstractXmlApplicationContext#setConfigLocation
 * @see org.springframework.web.context.ContextLoader#CONFIG_LOCATION_PARAM
 * @see org.springframework.web.servlet.FrameworkServlet#setContextConfigLocation
 */
String CONFIG_LOCATION_DELIMITERS = ",; \t\n";
```

属性介绍：在一段独立的字符串值中，任意数量的这些字符被认作在是多上下文配置路径之间的分隔符。

```java
/**
 * Name of the ConversionService bean in the factory.
 * If none is supplied, default conversion rules apply.
 * @since 3.0
 * @see org.springframework.core.convert.ConversionService
 */
String CONVERSION_SERVICE_BEAN_NAME = "conversionService";
```

属性介绍：工厂中转换服务bean的名称。如果没有提供，默认转换规则将被使用

```java
/**
 * Name of the LoadTimeWeaver bean in the factory. If such a bean is supplied,
 * the context will use a temporary ClassLoader for type matching, in order
 * to allow the LoadTimeWeaver to process all actual bean classes.
 * @since 2.5
 * @see org.springframework.instrument.classloading.LoadTimeWeaver
 */
String LOAD_TIME_WEAVER_BEAN_NAME = "loadTimeWeaver";
```

属性介绍：工厂中LoadTimeWeaver（加载期织入器）的名称。如果提供了这样的bean，上下文将会使用一个临时类加载器用以类型匹配，为了允许LoadTimeWeaver去处理所有实际bean类。

```java
/**
 * Name of the {@link Environment} bean in the factory.
 * @since 3.1
 */
String ENVIRONMENT_BEAN_NAME = "environment";
```

属性介绍：工厂中Environment bean的名称

```java
/**
 * Name of the System properties bean in the factory.
 * @see java.lang.System#getProperties()
 */
String SYSTEM_PROPERTIES_BEAN_NAME = "systemProperties";
```

属性介绍：工厂中系统属性bean的名称

```java
/**
 * Name of the System environment bean in the factory.
 * @see java.lang.System#getenv()
 */
String SYSTEM_ENVIRONMENT_BEAN_NAME = "systemEnvironment";
```

属性介绍：工厂中系统环境bean的名称

```java
/**
 * {@link Thread#getName() Name} of the {@linkplain #registerShutdownHook()
 * shutdown hook} thread: {@value}.
 * @since 5.2
 * @see #registerShutdownHook()
 */
String SHUTDOWN_HOOK_THREAD_NAME = "SpringContextShutdownHook";
```

属性介绍：{Thread的getName()}{registerShutdownHook()关闭钩子}线程：@value的名称（喵喵喵？）

```java
/**
 * Set the unique id of this application context.
 * @since 3.0
 */
void setId(String id);
```

方法介绍：设置当前应用上下文的唯一id

```java
/**
 * Set the parent of this application context.
 * <p>Note that the parent shouldn't be changed: It should only be set outside
 * a constructor if it isn't available when an object of this class is created,
 * for example in case of WebApplicationContext setup.
 * @param parent the parent context
 * @see org.springframework.web.context.ConfigurableWebApplicationContext
 */
void setParent(@Nullable ApplicationContext parent);
```

方法介绍：设置当前应用上下文的父类。

注意父类不应该被改变：如果当一个当前类创建的对象不可用时，他应该只在构造器之外被设置，例如在WebApplicationContext设置的情况下

```java
/**
 * Set the {@code Environment} for this application context.
 * @param environment the new environment
 * @since 3.1
 */
void setEnvironment(ConfigurableEnvironment environment);
```

方法介绍：为当前应用上下文设置Environment（环境）

```java
/**
 * Return the {@code Environment} for this application context in configurable
 * form, allowing for further customization.
 * @since 3.1
 */
@Override
ConfigurableEnvironment getEnvironment();
```

方法介绍：以可配置的形式返回当前上下文的Environment，允许更进一步的自定义

```java
/**
 * Add a new BeanFactoryPostProcessor that will get applied to the internal
 * bean factory of this application context on refresh, before any of the
 * bean definitions get evaluated. To be invoked during context configuration.
 * @param postProcessor the factory processor to register
 */
void addBeanFactoryPostProcessor(BeanFactoryPostProcessor postProcessor);
```

方法介绍：添加一个新的BeanFactoryPostProcessor将在当前向下文的内部bean工厂时刷新时被应用，在任何bean定义被评估之前。会在上下文配置期间被调用。

```java
/**
 * Add a new ApplicationListener that will be notified on context events
 * such as context refresh and context shutdown.
 * <p>Note that any ApplicationListener registered here will be applied
 * on refresh if the context is not active yet, or on the fly with the
 * current event multicaster in case of a context that is already active.
 * @param listener the ApplicationListener to register
 * @see org.springframework.context.event.ContextRefreshedEvent
 * @see org.springframework.context.event.ContextClosedEvent
 */
void addApplicationListener(ApplicationListener<?> listener);
on the fly：匆忙地；赶紧地
on the fly with：匆忙的使用
```

方法介绍：添加一个新的将会被上下文事件通知的ApplicationListener像是上下文刷新和上下文关闭。

注意如果上下文还没有被激活，任何在此注册的ApplicationListener将会被应用在刷新；如果上下文已经处于活动状态，则使用当前事件广播器 

```java
/**
 * Register the given protocol resolver with this application context,
 * allowing for additional resource protocols to be handled.
 * <p>Any such resolver will be invoked ahead of this context's standard
 * resolution rules. It may therefore also override any default rules.
 * @since 4.3
 */
void addProtocolResolver(ProtocolResolver resolver);
```

方法介绍：通过当前应用上下文注册给定的协议解析器，允许处理其他资源协议。任何这样的解析器将会在当前上下文的标准解析规则之前被调用。它可能因此也会被默认的规则覆盖。

```java
/**
 * Load or refresh the persistent representation of the configuration,
 * which might an XML file, properties file, or relational database schema.
 * <p>As this is a startup method, it should destroy already created singletons
 * if it fails, to avoid dangling resources. In other words, after invocation
 * of that method, either all or no singletons at all should be instantiated.
 * @throws BeansException if the bean factory could not be initialized
 * @throws IllegalStateException if already initialized and multiple refresh
 * attempts are not supported
 */
void refresh() throws BeansException, IllegalStateException;
representation：表现；描述；描绘；代理人
schema：架构；图表；概要
```

方法介绍：加载或者刷新配置中的持久描述文件，可能是一个XML文件，配置文件或者是关系型数据库表。

因为这是一个启动方法，如果启动失败的话他应该销毁已经创建的单例，来避免悬而未决的资源。换句话说，在调用这个方法之后，不是所有就是根本没有单例应该不是被初始化的