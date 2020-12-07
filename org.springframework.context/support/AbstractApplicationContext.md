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

```java
//---------------------------------------------------------------------
// Implementation of ApplicationContext interface
//---------------------------------------------------------------------
```

注释翻译：ApplicationContext接口的实现方法。

```java
/**
 * Set the unique id of this application context.
 * <p>Default is the object id of the context instance, or the name
 * of the context bean if the context is itself defined as a bean.
 * @param id the unique id of the context
 */
@Override
public void setId(String id) {
   this.id = id;
}
```

方法介绍：给当前应用上下文设置唯一的id。

默认的当前上下文实例对象的id，或者是这个上下文bean的名称，如果这个上下文本身定义为一个bean的话。

```java
/**
 * Set a friendly name for this context.
 * Typically done during initialization of concrete context implementations.
 * <p>Default is the object id of the context instance.
 */
public void setDisplayName(String displayName) {
   Assert.hasLength(displayName, "Display name must not be empty");
   this.displayName = displayName;
}
```

方法介绍：为当前上下文设置一个有好的名字。

通常情况下会在具体上下文实现类的初始化期间完成。

默认的是当前上下文实例对象的id。

```java
/**
 * Return a friendly name for this context.
 * @return a display name for this context (never {@code null})
 永远都不会是null

 */
@Override
public String getDisplayName() {
   return this.displayName;
}
```

方法介绍：返回当前上下文的有好的名称

```java
/**
 * Return the parent context, or {@code null} if there is no parent
 * (that is, this context is the root of the context hierarchy).
 */
that is：也就是说
@Override
@Nullable
public ApplicationContext getParent() {
   return this.parent;
}
```

方法介绍：返回父上下文，或者null如果没有父上下文的话（也就是说，当前上下文是上下文层次结构的起源）

```java
/**
 * Set the {@code Environment} for this application context.
 * <p>Default value is determined by {@link #createEnvironment()}. Replacing the
 * default with this method is one option but configuration through {@link
 * #getEnvironment()} should also be considered. In either case, such modifications
 * should be performed <em>before</em> {@link #refresh()}.
 * @see org.springframework.context.support.AbstractApplicationContext#createEnvironment
 */
@Override
public void setEnvironment(ConfigurableEnvironment environment) {
   this.environment = environment;
}
```

方法介绍：为当前应用上下文设置Environment。

默认值由createEnvironment()确定的。通过此方法替换默认的Environment是一个可选项但是也要考虑通过getEnvironment()进行配置（意思可能是已有的环境也需要纳入考虑范围）

```java
/**
 * Return the {@code Environment} for this application context in configurable
 * form, allowing for further customization.
 * <p>If none specified, a default environment will be initialized via
 * {@link #createEnvironment()}.
 */
@Override
public ConfigurableEnvironment getEnvironment() {
   if (this.environment == null) {
      this.environment = createEnvironment();
   }
   return this.environment;
}
```

方法介绍：以可配置的形式返回当前应用上下文的Environment，允许更进一步的自定义。

如果没有指定，则将会通过createEnvironment()方法初始化一个默认的environment

```java
/**
 * Create and return a new {@link StandardEnvironment}.
 * <p>Subclasses may override this method in order to supply
 * a custom {@link ConfigurableEnvironment} implementation.
 */
protected ConfigurableEnvironment createEnvironment() {
   return new StandardEnvironment();
}
```

方法介绍：创建并返回一个新的StandardEnvironment。

子类们为了提供一个自定义的ConfigurableEnvironment实现类会重写这个方法。

```java
/**
 * Return this context's internal bean factory as AutowireCapableBeanFactory,
 * if already available.
 * @see #getBeanFactory()
 */
@Override
public AutowireCapableBeanFactory getAutowireCapableBeanFactory() throws IllegalStateException {
   return getBeanFactory();
}
```

方法介绍：返回将当前上下文的内部bean工厂作为AutowireCapableBeanFactory返回，如果已经可用的话。

```java
/**
 * Return the timestamp (ms) when this context was first loaded.
 */
@Override
public long getStartupDate() {
   return this.startupDate;
}
```

方法介绍：当当前上下文第一次加载的时候返回一个时间戳

```java
/**
 * Publish the given event to all listeners.
 * <p>Note: Listeners get initialized after the MessageSource, to be able
 * to access it within listener implementations. Thus, MessageSource
 * implementations cannot publish events.
 * @param event the event to publish (may be application-specific or a
 * standard framework event)
 （可能是特定于应用程序或者是一个标准的框架事件）
 */
@Override
public void publishEvent(ApplicationEvent event) {
   publishEvent(event, null);
}
```

方法介绍：将给定的事件发布给所有的监听者。

注意：监听者会在MessageSource之后被初始化，这样做是为了能在监听器实现类中获得MessageSource。因此，MessageSource的实现类不能发布事件。

```java
/**
 * Publish the given event to all listeners.
 * <p>Note: Listeners get initialized after the MessageSource, to be able
 * to access it within listener implementations. Thus, MessageSource
 * implementations cannot publish events.
 * @param event the event to publish (may be an {@link ApplicationEvent}
 * or a payload object to be turned into a {@link PayloadApplicationEvent})
 */
可能是ApplicationContext或者是一个要被转换为PayloadApplicationEvent的装载对象
@Override
public void publishEvent(Object event) {
   publishEvent(event, null);
}
payload：有效载荷
```

方法介绍：将给定的事件发布给所有的监听者。

注意：监听者会在MessageSource之后进行初始化，这样就能在监听器的实现类之中获得到MessageSource。因此，MessageSource的实现类不能发布事件。

```java
/**
 * Publish the given event to all listeners.
 * @param event the event to publish (may be an {@link ApplicationEvent}
 * or a payload object to be turned into a {@link PayloadApplicationEvent})
 * @param eventType the resolved event type, if known
 * @since 4.2
 */
protected void publishEvent(Object event, @Nullable ResolvableType eventType) {
   Assert.notNull(event, "Event must not be null");

   // Decorate event as an ApplicationEvent if necessary
  //如果有必要的话将event装饰为ApplicationEvent
   ApplicationEvent applicationEvent;
   if (event instanceof ApplicationEvent) {
      applicationEvent = (ApplicationEvent) event;
   }
   else {
      applicationEvent = new PayloadApplicationEvent<>(this, event);
      if (eventType == null) {
         eventType = ((PayloadApplicationEvent<?>) applicationEvent).getResolvableType();
      }
   }

   // Multicast right now if possible - or lazily once the multicaster is initialized
  //如果可能的话立即组播-或者延迟到组播器初始化完毕立即执行
   if (this.earlyApplicationEvents != null) {
      this.earlyApplicationEvents.add(applicationEvent);
   }
   else {
      getApplicationEventMulticaster().multicastEvent(applicationEvent, eventType);
   }

   // Publish event via parent context as well...
  //通过父上下文也发布一下事件
   if (this.parent != null) {
      if (this.parent instanceof AbstractApplicationContext) {
         ((AbstractApplicationContext) this.parent).publishEvent(event, eventType);
      }
      else {
         this.parent.publishEvent(event);
      }
   }
}
```

方法介绍：将给定的事件发布到所有的监听者

```java
/**
 * Return the internal ApplicationEventMulticaster used by the context.
 * @return the internal ApplicationEventMulticaster (never {@code null})
 * @throws IllegalStateException if the context has not been initialized yet
 */
ApplicationEventMulticaster getApplicationEventMulticaster() throws IllegalStateException {
   if (this.applicationEventMulticaster == null) {
      throw new IllegalStateException("ApplicationEventMulticaster not initialized - " +
            "call 'refresh' before multicasting events via the context: " + this);
   }
   return this.applicationEventMulticaster;
}
```

方法介绍：返回一个当前上下文使用的内部ApplicationEventMulticaster

```java
/**
 * Return the internal LifecycleProcessor used by the context.
 * @return the internal LifecycleProcessor (never {@code null})
 * @throws IllegalStateException if the context has not been initialized yet
 */
LifecycleProcessor getLifecycleProcessor() throws IllegalStateException {
   if (this.lifecycleProcessor == null) {
      throw new IllegalStateException("LifecycleProcessor not initialized - " +
            "call 'refresh' before invoking lifecycle methods via the context: " + this);
   }
   return this.lifecycleProcessor;
}
```

方法介绍：返回一个当前上下文使用的内部的LifecycleProcessor

```java
/**
 * Return the ResourcePatternResolver to use for resolving location patterns
 * into Resource instances. Default is a
 * {@link org.springframework.core.io.support.PathMatchingResourcePatternResolver},
 * supporting Ant-style location patterns.
 * <p>Can be overridden in subclasses, for extended resolution strategies,
 * for example in a web environment.
 * <p><b>Do not call this when needing to resolve a location pattern.</b>
 * Call the context's {@code getResources} method instead, which
 * will delegate to the ResourcePatternResolver.
 * @return the ResourcePatternResolver for this context
 * @see #getResources
 * @see org.springframework.core.io.support.PathMatchingResourcePatternResolver
 */
protected ResourcePatternResolver getResourcePatternResolver() {
   return new PathMatchingResourcePatternResolver(this);
}
```

方法介绍：返回ResourcePatternResolver用来解析位置模式为资源实例。默认是一个PathMatchingResourcePatternResolver，支持Ant风格的位置匹配模式。

为了扩展解析策略，可以被子类重写，例如web环境。

当需要解析一个位置模式不要调用这个方法。请调用当前上下文的getResources()方法作为替代，它将会委托给ResourcePatternResolver来处理。

```java
//---------------------------------------------------------------------
// Implementation of ConfigurableApplicationContext interface
//---------------------------------------------------------------------
```

注释翻译：下面是对ConfigurableApplicationContext接口的实现

```java
/**
 * Set the parent of this application context.
 * <p>The parent {@linkplain ApplicationContext#getEnvironment() environment} is
 * {@linkplain ConfigurableEnvironment#merge(ConfigurableEnvironment) merged} with
 * this (child) application context environment if the parent is non-{@code null} and
 * its environment is an instance of {@link ConfigurableEnvironment}.
 * @see ConfigurableEnvironment#merge(ConfigurableEnvironment)
 */
@Override
public void setParent(@Nullable ApplicationContext parent) {
   this.parent = parent;
   if (parent != null) {
      Environment parentEnvironment = parent.getEnvironment();
      if (parentEnvironment instanceof ConfigurableEnvironment) {
         getEnvironment().merge((ConfigurableEnvironment) parentEnvironment);
      }
   }
}
```

方法介绍：设置当前应用上下文的父ApplicationContext

这个父ApplicationContext的[通过ApplicationContext的getEnvironment()方法获得的环境]是[通过ConfigurableEnvironment的merge(ConfigurableEnvironment)与当前子引用上下文环境合并后的environment]如果父ApplicationContext是个非空的并且它的环境是ConfigurableEnvironment的类型实例。

```java
/**
 * Return the list of BeanFactoryPostProcessors that will get applied
 * to the internal BeanFactory.
 */
public List<BeanFactoryPostProcessor> getBeanFactoryPostProcessors() {
   return this.beanFactoryPostProcessors;
}
```

方法介绍：返回将会被应用在内部BeanFactory中的BeanFactoryPostProcessors的列表。

```java
/**
 * Return the list of statically specified ApplicationListeners.
 */
public Collection<ApplicationListener<?>> getApplicationListeners() {
   return this.applicationListeners;
}
```

方法介绍：返回静态化指定的ApplicationListeners列表。

```java
@Override
public void refresh() throws BeansException, IllegalStateException {
   synchronized (this.startupShutdownMonitor) {
      // Prepare this context for refreshing.
       //为刷新准备当前上下文
      prepareRefresh();

      // Tell the subclass to refresh the internal bean factory.
       //告诉子类去刷新内部bean工厂
      ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

      // Prepare the bean factory for use in this context.
       //为当前上下文的使用准备bean 工厂
      prepareBeanFactory(beanFactory);

      try {
         // Allows post-processing of the bean factory in context subclasses.
          //允许在当前上下文子类中的bean工厂进行后置处理
         postProcessBeanFactory(beanFactory);

         // Invoke factory processors registered as beans in the context.
          //调用当前上下文中以beans形式注册的工厂处理器
         invokeBeanFactoryPostProcessors(beanFactory);

         // Register bean processors that intercept bean creation.
          //注册拦截bean创建的bean处理器。
         registerBeanPostProcessors(beanFactory);

         // Initialize message source for this context.
          //为当前上下文初始化消息源
         initMessageSource();

         // Initialize event multicaster for this context.
          //为当前上下文初始化事件组播器
         initApplicationEventMulticaster();

         // Initialize other special beans in specific context subclasses.
          //在指定上下文子类中初始化其他特殊的beans
         onRefresh();

         // Check for listener beans and register them.
          //检查并注册监听器beans
         registerListeners();

         // Instantiate all remaining (non-lazy-init) singletons.
         //初始化所有剩下的（非懒加载）的单例
         finishBeanFactoryInitialization(beanFactory);

         // Last step: publish corresponding event.
          //最后一步：发布相应的事件
         finishRefresh();
      }

      catch (BeansException ex) {
         if (logger.isWarnEnabled()) {
            logger.warn("Exception encountered during context initialization - " +
                  "cancelling refresh attempt: " + ex);
         }

         // Destroy already created singletons to avoid dangling（晃来晃去的，悬空的） resources.
          //销毁已经创建的单例来避免悬空的资源
         destroyBeans();

         // Reset 'active' flag.
         //重置'激活'标识
         cancelRefresh(ex);

         // Propagate（传播） exception to caller.
          //将异常传递给调用者
         throw ex;
      }

      finally {
         // Reset common introspection caches in Spring's core, since we
         // might not ever need metadata for singleton beans anymore...
          //在Spring核心重置通用内省缓存，因为我们可能不再需要singleton bean的元数据
         resetCommonCaches();
      }
   }
}
```

```java
/**
 * Prepare this context for refreshing, setting its startup date and
 * active flag as well as performing any initialization of property sources.
 */
protected void prepareRefresh() {
   // Switch to active.
  //切换为激活状态
   this.startupDate = System.currentTimeMillis();
   this.closed.set(false);
   this.active.set(true);

   if (logger.isDebugEnabled()) {
      if (logger.isTraceEnabled()) {
         logger.trace("Refreshing " + this);
      }
      else {
         logger.debug("Refreshing " + getDisplayName());
      }
   }
  // Initialize any placeholder property sources in the context environment.
  //初始化在当前上下文环境中的任何占位符属性源
		initPropertySources();

		// Validate that all properties marked as required are resolvable:
		// see ConfigurablePropertyResolver#setRequiredProperties
  //校验所有被标记为需要的属性是否为可解析的：参见ConfigurablePropertyResolver的setRequiredProperties()
		getEnvironment().validateRequiredProperties();

		// Store pre-refresh ApplicationListeners...
  //保存预刷新监听器
		if (this.earlyApplicationListeners == null) {
			this.earlyApplicationListeners = new LinkedHashSet<>(this.applicationListeners);
		}
		else {
			// Reset local application listeners to pre-refresh state.
      //重置本地应用监听器为预刷新状态
			this.applicationListeners.clear();
			this.applicationListeners.addAll(this.earlyApplicationListeners);
		}

		// Allow for the collection of early ApplicationEvents,
		// to be published once the multicaster is available...
  //允许早期的ApplicationEvents（应用程序事件）集合立即被发布，一旦组播器可用。
		this.earlyApplicationEvents = new LinkedHashSet<>();
}
```

方法介绍：为刷新准备当前上下文，设置他的启动时间和激活标识以及执行属性源的任何初始化。

```java
/**
 * <p>Replace any stub property sources with actual instances.
 * @see org.springframework.core.env.PropertySource.StubPropertySource
 * @see org.springframework.web.context.support.WebApplicationContextUtils#initServletPropertySources
 */
protected void initPropertySources() {
   // For subclasses: do nothing by default.
}
replace...with:将...替换为
```

方法介绍：替换任何存根属性源替换为真实实例

```java
/**
 * Tell the subclass to refresh the internal bean factory.
 * @return the fresh BeanFactory instance
 * @see #refreshBeanFactory()
 * @see #getBeanFactory()
 */
protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
   refreshBeanFactory();
   return getBeanFactory();
}
```

方法介绍：告诉子类去刷新内部bean工厂

```java
/**
 * Configure the factory's standard context characteristics,
 * such as the context's ClassLoader and post-processors.
 * @param beanFactory the BeanFactory to configure
 */
protected void prepareBeanFactory(ConfigurableListableBeanFactory beanFactory) {
   // Tell the internal bean factory to use the context's class loader etc.
  //告诉内部bean工厂去使用上下文的类加载器等
   beanFactory.setBeanClassLoader(getClassLoader());
   beanFactory.setBeanExpressionResolver(new StandardBeanExpressionResolver(beanFactory.getBeanClassLoader()));
   beanFactory.addPropertyEditorRegistrar(new ResourceEditorRegistrar(this, getEnvironment()));

   // Configure the bean factory with context callbacks.
  //通过回调配置bean工厂
   beanFactory.addBeanPostProcessor(new ApplicationContextAwareProcessor(this));
   beanFactory.ignoreDependencyInterface(EnvironmentAware.class);
   beanFactory.ignoreDependencyInterface(EmbeddedValueResolverAware.class);
   beanFactory.ignoreDependencyInterface(ResourceLoaderAware.class);
   beanFactory.ignoreDependencyInterface(ApplicationEventPublisherAware.class);
   beanFactory.ignoreDependencyInterface(MessageSourceAware.class);
   beanFactory.ignoreDependencyInterface(ApplicationContextAware.class);

   // BeanFactory interface not registered as resolvable type in a plain factory.
   // MessageSource registered (and found for autowiring) as a bean.
  //BeanFactory实例不在普通工厂中注册为可解析类型
  //MessageSource注册（并且为自动装配找到）为bean
   beanFactory.registerResolvableDependency(BeanFactory.class, beanFactory);
   beanFactory.registerResolvableDependency(ResourceLoader.class, this);
   beanFactory.registerResolvableDependency(ApplicationEventPublisher.class, this);
   beanFactory.registerResolvableDependency(ApplicationContext.class, this);

   // Register early post-processor for detecting inner beans as ApplicationListeners.
  //注册早期的派驻处理器（后置处理器）用以侦测内部beans作为ApplicationListeners
   beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(this));

   // Detect a LoadTimeWeaver and prepare for weaving, if found.
  //侦测LoadTimeWeaver(加载期织入者)并且准备织入，如果发现的话
   if (beanFactory.containsBean(LOAD_TIME_WEAVER_BEAN_NAME)) {
      beanFactory.addBeanPostProcessor(new LoadTimeWeaverAwareProcessor(beanFactory));
      // Set a temporary ClassLoader for type matching.
     //设置一个临时类加载器用来进行类型匹配
      beanFactory.setTempClassLoader(new ContextTypeMatchClassLoader(beanFactory.getBeanClassLoader()));
   }

   // Register default environment beans.
  //注册默认环境beans
   if (!beanFactory.containsLocalBean(ENVIRONMENT_BEAN_NAME)) {
      beanFactory.registerSingleton(ENVIRONMENT_BEAN_NAME, getEnvironment());
   }
   if (!beanFactory.containsLocalBean(SYSTEM_PROPERTIES_BEAN_NAME)) {
      beanFactory.registerSingleton(SYSTEM_PROPERTIES_BEAN_NAME, getEnvironment().getSystemProperties());
   }
   if (!beanFactory.containsLocalBean(SYSTEM_ENVIRONMENT_BEAN_NAME)) {
      beanFactory.registerSingleton(SYSTEM_ENVIRONMENT_BEAN_NAME, getEnvironment().getSystemEnvironment());
   }
}
```

方法介绍：配置工厂的标准上下文特征，像是上下文的ClassLoader类加载器和派驻处理器(post-processors)。

```java
/**
 * Modify the application context's internal bean factory after its standard
 * initialization. All bean definitions will have been loaded, but no beans
 * will have been instantiated yet. This allows for registering special
 * BeanPostProcessors etc in certain ApplicationContext implementations.
 * @param beanFactory the bean factory used by the application context
 */
protected void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) {
}
certain:某些；确实；肯定；
```

方法介绍：在应用上下文的标准初始化后修改应用上下文的内部bean工厂，所有的bean definitions将会被加载完毕，但是还没有beans会被初始化。这允许在某些ApplicationContext实现中注册特殊的BeanPostProcessors

```java
/**
 * Instantiate and invoke all registered BeanFactoryPostProcessor beans,
 * respecting explicit order if given.
 * <p>Must be called before singleton instantiation.
 */
protected void invokeBeanFactoryPostProcessors(ConfigurableListableBeanFactory beanFactory) {
   PostProcessorRegistrationDelegate.invokeBeanFactoryPostProcessors(beanFactory, getBeanFactoryPostProcessors());

   // Detect a LoadTimeWeaver and prepare for weaving, if found in the meantime
   // (e.g. through an @Bean method registered by ConfigurationClassPostProcessor)
  //侦测加载器织入者并且准备织入操作，如果在此期间发现的话。
  //（例如通过ConfigurationClassPostProcessor@Bean方法注册）
   if (beanFactory.getTempClassLoader() == null && beanFactory.containsBean(LOAD_TIME_WEAVER_BEAN_NAME)) {
      beanFactory.addBeanPostProcessor(new LoadTimeWeaverAwareProcessor(beanFactory));
      beanFactory.setTempClassLoader(new ContextTypeMatchClassLoader(beanFactory.getBeanClassLoader()));
   }
}
```

方法介绍：实例化并且调用所有已经注册的BeanFactoryPostProcessor（Bean工厂派驻处理器）beans，

遵守显示指定的顺序如果给定的话。

必须在单例初始化之前调用。

```java
/**
 * Instantiate and register all BeanPostProcessor beans,
 * respecting explicit order if given.
 * <p>Must be called before any instantiation of application beans.
 */
protected void registerBeanPostProcessors(ConfigurableListableBeanFactory beanFactory) {
   PostProcessorRegistrationDelegate.registerBeanPostProcessors(beanFactory, this);
}
```

方法介绍：实例化并且注册所有的BeanPostProcessor（bean后置处理器）beans，遵守显示指定的顺序如果给定的话。

必须在任何应用beans初始化之前调用。

```java
/**
 * Initialize the MessageSource.
 * Use parent's if none defined in this context.
 */
protected void initMessageSource() {
   ConfigurableListableBeanFactory beanFactory = getBeanFactory();
   if (beanFactory.containsLocalBean(MESSAGE_SOURCE_BEAN_NAME)) {
      this.messageSource = beanFactory.getBean(MESSAGE_SOURCE_BEAN_NAME, MessageSource.class);
      // Make MessageSource aware of parent MessageSource.
     //使MessageSource知道(了解)父MessageSource
      if (this.parent != null && this.messageSource instanceof HierarchicalMessageSource) {
         HierarchicalMessageSource hms = (HierarchicalMessageSource) this.messageSource;
         if (hms.getParentMessageSource() == null) {
            // Only set parent context as parent MessageSource if no parent MessageSource
            // registered already.
          // 仅当还没有父MessageSource注册的话时，将父上下文遏制为父MessageSource
            hms.setParentMessageSource(getInternalParentMessageSource());
         }
      }
      if (logger.isTraceEnabled()) {
         logger.trace("Using MessageSource [" + this.messageSource + "]");
      }
   }
   else {
      // Use empty MessageSource to be able to accept getMessage calls.
     //使用空的MessageSource可以接受getMessage()调用
      DelegatingMessageSource dms = new DelegatingMessageSource();
      dms.setParentMessageSource(getInternalParentMessageSource());
      this.messageSource = dms;
      beanFactory.registerSingleton(MESSAGE_SOURCE_BEAN_NAME, this.messageSource);
      if (logger.isTraceEnabled()) {
         logger.trace("No '" + MESSAGE_SOURCE_BEAN_NAME + "' bean, using [" + this.messageSource + "]");
      }
   }
}
```

方法介绍：初始化MessageSource。使用父ApplicationContext的如果当前上下文中没有定义的话。

```java
/**
 * Initialize the ApplicationEventMulticaster.
 * Uses SimpleApplicationEventMulticaster if none defined in the context.
 * @see org.springframework.context.event.SimpleApplicationEventMulticaster
 */
protected void initApplicationEventMulticaster() {
   ConfigurableListableBeanFactory beanFactory = getBeanFactory();
   if (beanFactory.containsLocalBean(APPLICATION_EVENT_MULTICASTER_BEAN_NAME)) {
      this.applicationEventMulticaster =
            beanFactory.getBean(APPLICATION_EVENT_MULTICASTER_BEAN_NAME, ApplicationEventMulticaster.class);
      if (logger.isTraceEnabled()) {
         logger.trace("Using ApplicationEventMulticaster [" + this.applicationEventMulticaster + "]");
      }
   }
   else {
      this.applicationEventMulticaster = new SimpleApplicationEventMulticaster(beanFactory);
      beanFactory.registerSingleton(APPLICATION_EVENT_MULTICASTER_BEAN_NAME, this.applicationEventMulticaster);
      if (logger.isTraceEnabled()) {
         logger.trace("No '" + APPLICATION_EVENT_MULTICASTER_BEAN_NAME + "' bean, using " +
               "[" + this.applicationEventMulticaster.getClass().getSimpleName() + "]");
      }
   }
}
```

方法介绍：初始化ApplicationEventMulticaster。使用SimpleApplicationEventMulticaster如果上下文中没有定义的话。

```java
/**
 * Initialize the LifecycleProcessor.
 * Uses DefaultLifecycleProcessor if none defined in the context.
 * @see org.springframework.context.support.DefaultLifecycleProcessor
 */
protected void initLifecycleProcessor() {
   ConfigurableListableBeanFactory beanFactory = getBeanFactory();
   if (beanFactory.containsLocalBean(LIFECYCLE_PROCESSOR_BEAN_NAME)) {
      this.lifecycleProcessor =
            beanFactory.getBean(LIFECYCLE_PROCESSOR_BEAN_NAME, LifecycleProcessor.class);
      if (logger.isTraceEnabled()) {
         logger.trace("Using LifecycleProcessor [" + this.lifecycleProcessor + "]");
      }
   }
   else {
      DefaultLifecycleProcessor defaultProcessor = new DefaultLifecycleProcessor();
      defaultProcessor.setBeanFactory(beanFactory);
      this.lifecycleProcessor = defaultProcessor;
      beanFactory.registerSingleton(LIFECYCLE_PROCESSOR_BEAN_NAME, this.lifecycleProcessor);
      if (logger.isTraceEnabled()) {
         logger.trace("No '" + LIFECYCLE_PROCESSOR_BEAN_NAME + "' bean, using " +
               "[" + this.lifecycleProcessor.getClass().getSimpleName() + "]");
      }
   }
}
```

方法介绍：初始化LifecycleProcessor。使用DefaultLifecycleProcessor如果上下文中没有定义的话。

```java
/**
 * Template method which can be overridden to add context-specific refresh work.
 * Called on initialization of special beans, before instantiation of singletons.
 * <p>This implementation is empty.
 * @throws BeansException in case of errors
 * @see #refresh()
 */
protected void onRefresh() throws BeansException {
   // For subclasses: do nothing by default.
}
```

方法介绍：模板方法，可以被重写用来添加特定上下文刷新工作。在特殊beans初始化时候被调用，在单例初始化之前。

这个实现是空的。

```java
/**
 * Add beans that implement ApplicationListener as listeners.
 * Doesn't affect other listeners, which can be added without being beans.
 */
protected void registerListeners() {
   // Register statically specified listeners first.
  //首先注册静态化指定的监听器
   for (ApplicationListener<?> listener : getApplicationListeners()) {
      getApplicationEventMulticaster().addApplicationListener(listener);
   }

   // Do not initialize FactoryBeans here: We need to leave all regular beans
   // uninitialized to let post-processors apply to them!
  //这里不要初始化FactoryBeans：我们需要让所有常规beans保持未初始化状态，让派驻处理器去应用他们
   String[] listenerBeanNames = getBeanNamesForType(ApplicationListener.class, true, false);
   for (String listenerBeanName : listenerBeanNames) {
      getApplicationEventMulticaster().addApplicationListenerBean(listenerBeanName);
   }

   // Publish early application events now that we finally have a multicaster...
  //发布早期应用事件，现在我们终于有了广播器
   Set<ApplicationEvent> earlyEventsToProcess = this.earlyApplicationEvents;
   this.earlyApplicationEvents = null;
   if (earlyEventsToProcess != null) {
      for (ApplicationEvent earlyEvent : earlyEventsToProcess) {
         getApplicationEventMulticaster().multicastEvent(earlyEvent);
      }
   }
}
```

方法介绍：添加实现ApplicationListener的beans当做监听器。

不要影响其他的监听器，这些监听器可以在不作为beans的情况下被添加

```java
/**
 * Finish the initialization of this context's bean factory,
 * initializing all remaining singleton beans.
 */
protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {
   // Initialize conversion service for this context.
  //为当前上下文初始化转换服务
   if (beanFactory.containsBean(CONVERSION_SERVICE_BEAN_NAME) &&
         beanFactory.isTypeMatch(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class)) {
      beanFactory.setConversionService(
            beanFactory.getBean(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class));
   }

   // Register a default embedded value resolver if no bean post-processor
   // (such as a PropertyPlaceholderConfigurer bean) registered any before:
   // at this point, primarily for resolution in annotation attribute values.
  //注册一个默认的内嵌的值解析器如果没有注册备案后置处理器（例如PropertyPlaceholderConfigurer bean）：此时主要为了解析注解属性值
   if (!beanFactory.hasEmbeddedValueResolver()) {
      beanFactory.addEmbeddedValueResolver(strVal -> getEnvironment().resolvePlaceholders(strVal));
   }

   // Initialize LoadTimeWeaverAware beans early to allow for registering their transformers early.
  //尽早初始化LoadTimeWeaverAware beans，以便尽早的注册他们的转换器
   String[] weaverAwareNames = beanFactory.getBeanNamesForType(LoadTimeWeaverAware.class, false, false);
   for (String weaverAwareName : weaverAwareNames) {
      getBean(weaverAwareName);
   }

   // Stop using the temporary ClassLoader for type matching.
  //停止使用临时ClassLoader进行类型匹配
   beanFactory.setTempClassLoader(null);

   // Allow for caching all bean definition metadata, not expecting further changes.
  //允许缓存所有bean definition元数据，不需要进一步更改
   beanFactory.freezeConfiguration();

   // Instantiate all remaining (non-lazy-init) singletons.
  //实例化所有剩下的（非延迟初始化）单例
   beanFactory.preInstantiateSingletons();
}
```

方法介绍：完成当前上下文的bean工厂的初始化，初始化所有遗留的单例beans

















































