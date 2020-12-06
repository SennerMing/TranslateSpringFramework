#### AbstractApplicationContext

```java
/**
 * Abstract implementation of the {@link org.springframework.context.ApplicationContext}
 * interface. Doesn't mandate the type of storage used for configuration; simply
 * implements common context functionality. Uses the Template Method design pattern,
 * requiring concrete subclasses to implement abstract methods.
 *
 * <p>In contrast to a plain BeanFactory, an ApplicationContext is supposed
 * to detect special beans defined in its internal bean factory:
 * Therefore, this class automatically registers
 * {@link org.springframework.beans.factory.config.BeanFactoryPostProcessor BeanFactoryPostProcessors},
 * {@link org.springframework.beans.factory.config.BeanPostProcessor BeanPostProcessors},
 * and {@link org.springframework.context.ApplicationListener ApplicationListeners}
 * which are defined as beans in the context.
 *
 * <p>A {@link org.springframework.context.MessageSource} may also be supplied
 * as a bean in the context, with the name "messageSource"; otherwise, message
 * resolution is delegated to the parent context. Furthermore, a multicaster
 * for application events can be supplied as an "applicationEventMulticaster" bean
 * of type {@link org.springframework.context.event.ApplicationEventMulticaster}
 * in the context; otherwise, a default multicaster of type
 * {@link org.springframework.context.event.SimpleApplicationEventMulticaster} will be used.
 *
 * <p>Implements resource loading by extending
 * {@link org.springframework.core.io.DefaultResourceLoader}.
 * Consequently treats non-URL resource paths as class path resources
 * (supporting full class path resource names that include the package path,
 * e.g. "mypackage/myresource.dat"), unless the {@link #getResourceByPath}
 * method is overridden in a subclass.
 *
 * @author Rod Johnson
 * @author Juergen Hoeller
 * @author Mark Fisher
 * @author Stephane Nicoll
 * @author Sam Brannen
 * @since January 21, 2001
 * @see #refreshBeanFactory
 * @see #getBeanFactory
 * @see org.springframework.beans.factory.config.BeanFactoryPostProcessor
 * @see org.springframework.beans.factory.config.BeanPostProcessor
 * @see org.springframework.context.event.ApplicationEventMulticaster
 * @see org.springframework.context.ApplicationListener
 * @see org.springframework.context.MessageSource
 */
mandate：n 授权；任期；委托书；授权令 v 强制执行；委托办理；授权
In contrast to：与...相比
unless：除非
```

类介绍：是ApplicationContext接口的抽象实现类。不强制要求用于配置的存储类型；只是实现通用的上下文功能。想要使用模板方法设计模式需要具体子类去实现抽象方法。

与普通的bean工厂相比，ApplicationContext应该侦测定义在其内部bean factory的beans：因此，这个类自动注册[BeanFactoryPostProcessor类型的bean工厂派驻（后置）处理器]，[BeanPostProcessor类型的bean派驻(后置)处理器]和[ApplicationListener类型的应用监听者]在上下文中，这些个类将会被定义为beans。

MessageSource在当前上下文中也可被当做bean提供，他的名字就是"messageSource"；否则，消息解析就会委托给父上下文了。更进一步，在上下文中应用事件的组播器(广播器)可以被当做名为"applicationEventMulticaster"的bean提供，这个bean的类型是ApplicationEventMulticaster；否则，将使用类型为SimpleApplicationEventMulticaster的默认组播器。

通过扩展DefaultResourceLoader来实现资源加载。因此可将非URL资源路径当做类路径资源处理(支持完整类路径资源名称包括包路径，例如："mypackage/myresource.dat")，除非getResourceByPath()方法被子类重写。

```java
/**
 * Name of the MessageSource bean in the factory.
 * If none is supplied, message resolution is delegated to the parent.
 * @see MessageSource
 */
public static final String MESSAGE_SOURCE_BEAN_NAME = "messageSource";
```

属性介绍：当前工厂中MessageSource bean的名称。如果没有，消息解析将会委托给父ApplicationContext处理。

```java
/**
 * Name of the LifecycleProcessor bean in the factory.
 * If none is supplied, a DefaultLifecycleProcessor is used.
 * @see org.springframework.context.LifecycleProcessor
 * @see org.springframework.context.support.DefaultLifecycleProcessor
 */
public static final String LIFECYCLE_PROCESSOR_BEAN_NAME = "lifecycleProcessor";
```

属性介绍：当前工厂中LifecycleProcessor的名称。如果没有提供的话，使用默认的DefaultLifecycleProcessor。

```java
/**
 * Name of the ApplicationEventMulticaster bean in the factory.
 * If none is supplied, a default SimpleApplicationEventMulticaster is used.
 * @see org.springframework.context.event.ApplicationEventMulticaster
 * @see org.springframework.context.event.SimpleApplicationEventMulticaster
 */
public static final String APPLICATION_EVENT_MULTICASTER_BEAN_NAME = "applicationEventMulticaster";
```

属性介绍：当前工厂中ApplicationEventMulticaster bean的名称。

如果没有可提供的ApplicationEventMulticaster bean，一个默认的SimpleApplicationEventMulticaster将会被使用。

```java
static {
   // Eagerly load the ContextClosedEvent class to avoid weird classloader issues
   // on application shutdown in WebLogic 8.1. (Reported by Dustin Woods.)
   ContextClosedEvent.class.getName();
}
```

静态代码块介绍：为了避免在程序关闭时在WebLogic8.1中会出现奇怪的类加载器问题，需要急切加载ContextClosedEvent类（这个问题由二狗上报）

```java
/** Logger used by this class. Available to subclasses. */
protected final Log logger = LogFactory.getLog(getClass());
```

属性介绍：当前类使用的日志工具。子类同样可以获取

```java
/** Unique id for this context, if any. */
private String id = ObjectUtils.identityToString(this);
```

属性介绍：当前上下文的唯一的id，如果存在的话。

```java
/** Display name. */
private String displayName = ObjectUtils.identityToString(this);
```

属性介绍：显示名称(说是什么java可视化界面的什么东西。。。嗨呀搞不懂)

```java
/** Parent context. */
@Nullable
private ApplicationContext parent;
```

属性介绍：父上下文

```java
/** Environment used by this context. */
@Nullable
private ConfigurableEnvironment environment;
```

属性介绍：当前上下文使用的Environment

```java
/** BeanFactoryPostProcessors to apply on refresh. */
private final List<BeanFactoryPostProcessor> beanFactoryPostProcessors = new ArrayList<>();
```

属性介绍：用在刷新上的BeanFactoryPostProcessors

```java
/** System time in milliseconds when this context started. */
private long startupDate;
```

属性介绍：当前上下文启动的系统时间，毫秒级

```java
/** Flag that indicates whether this context is currently active. */
private final AtomicBoolean active = new AtomicBoolean();
```

属性介绍：显示当前上下文当前是否是激活的状态标识

```java
/** Flag that indicates whether this context has been closed already. */
private final AtomicBoolean closed = new AtomicBoolean();
```

属性介绍：显示当前上下文是否已经被关闭的状态标识

```java
/** Synchronization monitor for the "refresh" and "destroy". */
private final Object startupShutdownMonitor = new Object();
```

属性介绍：refresh和destroy的同步监视器

```java
/** Reference to the JVM shutdown hook, if registered. */
@Nullable
private Thread shutdownHook;
```

属性介绍：JVM关闭钩子的引用，如果已经注册的话

```java
/** ResourcePatternResolver used by this context. */
private ResourcePatternResolver resourcePatternResolver;
```

属性介绍：当前上下文使用的ResourcePatternResolver

```java
/** LifecycleProcessor for managing the lifecycle of beans within this context. */
@Nullable
private LifecycleProcessor lifecycleProcessor;
```

属性介绍：管理当前上下文中beans生命周期的LifecycleProcessor

```java
/** MessageSource we delegate our implementation of this interface to. */
@Nullable
private MessageSource messageSource;
```

属性介绍：这个MessageSource是我们委托我们的这个接口的实现类(委托的MessageSource的实现类)

```java
/** Helper class used in event publishing. */
@Nullable
private ApplicationEventMulticaster applicationEventMulticaster;
```

属性介绍：用于事件发布的帮助类

```java
/** Statically specified listeners. */
private final Set<ApplicationListener<?>> applicationListeners = new LinkedHashSet<>();
```

属性介绍：静态化指定的监听器

```java
/** Local listeners registered before refresh. */
@Nullable
private Set<ApplicationListener<?>> earlyApplicationListeners;
```

属性介绍：在刷新之前注册的本地监听器

```java
/** ApplicationEvents published before the multicaster setup. */
@Nullable
private Set<ApplicationEvent> earlyApplicationEvents;
```

属性介绍：在广播器设置之前已经发布的ApplicationEvents

```java
/**
 * Create a new AbstractApplicationContext with no parent.
 */
public AbstractApplicationContext() {
   this.resourcePatternResolver = getResourcePatternResolver();
}
```

方法介绍：创建一个新的AbstractApplicationContext不指定父ApplicationContext

```java
/**
 * Create a new AbstractApplicationContext with the given parent context.
 * @param parent the parent context
 */
public AbstractApplicationContext(@Nullable ApplicationContext parent) {
   this();
   setParent(parent);
}
```

方法介绍：创建一个新的AbstractApplicationContext给定父上下文









