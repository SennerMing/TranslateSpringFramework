#### AbstractRefreshableApplicationContext

```java
/**
 * Base class for {@link org.springframework.context.ApplicationContext}
 * implementations which are supposed to support multiple calls to {@link #refresh()},
 * creating a new internal bean factory instance every time.
 * Typically (but not necessarily), such a context will be driven by
 * a set of config locations to load bean definitions from.
 *
 * <p>The only method to be implemented by subclasses is {@link #loadBeanDefinitions},
 * which gets invoked on each refresh. A concrete implementation is supposed to load
 * bean definitions into the given
 * {@link org.springframework.beans.factory.support.DefaultListableBeanFactory},
 * typically delegating to one or more specific bean definition readers.
 *
 * <p><b>Note that there is a similar base class for WebApplicationContexts.</b>
 * {@link org.springframework.web.context.support.AbstractRefreshableWebApplicationContext}
 * provides the same subclassing strategy, but additionally pre-implements
 * all context functionality for web environments. There is also a
 * pre-defined way to receive config locations for a web context.
 *
 * <p>Concrete standalone subclasses of this base class, reading in a
 * specific bean definition format, are {@link ClassPathXmlApplicationContext}
 * and {@link FileSystemXmlApplicationContext}, which both derive from the
 * common {@link AbstractXmlApplicationContext} base class;
 * {@link org.springframework.context.annotation.AnnotationConfigApplicationContext}
 * supports {@code @Configuration}-annotated classes as a source of bean definitions.
 *
 * @author Juergen Hoeller
 * @author Chris Beams
 * @since 1.1.3
 * @see #loadBeanDefinitions
 * @see org.springframework.beans.factory.support.DefaultListableBeanFactory
 * @see org.springframework.web.context.support.AbstractRefreshableWebApplicationContext
 * @see AbstractXmlApplicationContext
 * @see ClassPathXmlApplicationContext
 * @see FileSystemXmlApplicationContext
 * @see org.springframework.context.annotation.AnnotationConfigApplicationContext
 */
derive：获得；取得；(使)起源，(使)产生；
derive from：派生
```

类介绍：是ApplicationContext实现类的基类，这些基类应该支持refresh()的多次调用，每次都会创建一个新的内部的bean工厂实例。通常情况下（但是不一定），这样一个上下文将会被一组配置路径驱动来加载bean definitions（来自config locations）

这个唯一的被子类实现的方法且将会被每次刷新都调用的方法就是loadBeanDefinitions()。具体的实现类应该将bean definitions加载入给定的DefaultListableBeanFactory()，通常委托给一个或者更多的指定的bean definition 阅读器。

注意：有一个为WebApplicationContexts准备的和这个类相似的基类。AbstractRefreshableWebApplicationContext提供相同的子类化策略，但是额外的为web环境预实现了所有上下文功能。它还有为web上下文预定义的方式来获取配置路径。

这个基类的具体且独立的子类，以特定的bean definition格式进行读取，这些子类就是ClassPathXmlApplicationContext和FileSystemXmlApplication，他们都是派生自一个共同的基类AbstractXmlApplicationContext；AnnotationConfigApplicationContext支持将带@Configuration注解的类当做一个bean definitions的资源。

```java
/** Bean factory for this context. */
@Nullable
private DefaultListableBeanFactory beanFactory;
```

属性介绍：当前上下文的Bean工厂

```java
/** Synchronization monitor for the internal BeanFactory. */
private final Object beanFactoryMonitor = new Object();
```

属性介绍：内部Bean工厂的同步监视器

```java
/**
 * Create a new AbstractRefreshableApplicationContext with no parent.
 */
public AbstractRefreshableApplicationContext() {
}
```

方法介绍：创建一个新的没有父ApplicationContext的AbstractRefreshableApplicationContext

```java
/**
 * Create a new AbstractRefreshableApplicationContext with the given parent context.
 * @param parent the parent context
 */
public AbstractRefreshableApplicationContext(@Nullable ApplicationContext parent) {
   super(parent);
}
```

方法介绍：创建一个新的指定父ApplicationContext的AbstractRefreshableApplicationContext

```java
/**
 * Set whether it should be allowed to override bean definitions by registering
 * a different definition with the same name, automatically replacing the former.
 * If not, an exception will be thrown. Default is "true".
 * @see org.springframework.beans.factory.support.DefaultListableBeanFactory#setAllowBeanDefinitionOverriding
 */
public void setAllowBeanDefinitionOverriding(boolean allowBeanDefinitionOverriding) {
   this.allowBeanDefinitionOverriding = allowBeanDefinitionOverriding;
}
```

方法介绍：设置是否应该允许覆盖同名的但是不相同的definition，自动替换之前定义的。如果不允许则会报错，默认是允许。

```java
/**
 * Set whether to allow circular references between beans - and automatically
 * try to resolve them.
 * <p>Default is "true". Turn this off to throw an exception when encountering
 * a circular reference, disallowing them completely.
 * @see org.springframework.beans.factory.support.DefaultListableBeanFactory#setAllowCircularReferences
 */
public void setAllowCircularReferences(boolean allowCircularReferences) {
   this.allowCircularReferences = allowCircularReferences;
}
```

方法介绍：设置是否允许bean之间的循环依赖-并且自动尝试解析他们。

默认是允许状态，不允许的话当遇到循环依赖将会抛出错误，完全不允许他们循环依赖。

```java
/**
 * This implementation performs an actual refresh of this context's underlying
 * bean factory, shutting down the previous bean factory (if any) and
 * initializing a fresh bean factory for the next phase of the context's lifecycle.
 */
underlying：表面下的；根本的；潜在的；隐含的；
@Override
protected final void refreshBeanFactory() throws BeansException {
   if (hasBeanFactory()) {
      destroyBeans();
      closeBeanFactory();
   }
   try {
      DefaultListableBeanFactory beanFactory = createBeanFactory();
      beanFactory.setSerializationId(getId());
      customizeBeanFactory(beanFactory);
      loadBeanDefinitions(beanFactory);
      synchronized (this.beanFactoryMonitor) {
         this.beanFactory = beanFactory;
      }
   }
   catch (IOException ex) {
      throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);
   }
}
```

方法介绍：这个实现方法执行当前上下文底层bean工厂的实际的刷新，关闭之前的bean factory（如果存在的话）并且为当前上下文生命周期的下一个阶段初始化一个新的bean factory。

```java
/**
 * Determine whether this context currently holds a bean factory,
 * i.e. has been refreshed at least once and not been closed yet.
 */
protected final boolean hasBeanFactory() {
   synchronized (this.beanFactoryMonitor) {
      return (this.beanFactory != null);
   }
}
```

方法介绍：确定当前上下文此时是否持有一个bean factory，即是否已经被刷新至少一次并且还未关闭。

```java
/**
 * Overridden to turn it into a no-op: With AbstractRefreshableApplicationContext,
 * {@link #getBeanFactory()} serves a strong assertion for an active context anyway.
 */
@Override
protected void assertBeanFactoryActive() {
}
```

方法介绍：重写用以将其转换为空指令：与AbstractRefreshableApplicationContext，getBeanFactory()方法无论如何都会为激活的上下文提供强断言。

```java
/**
 * Create an internal bean factory for this context.
 * Called for each {@link #refresh()} attempt.
 * <p>The default implementation creates a
 * {@link org.springframework.beans.factory.support.DefaultListableBeanFactory}
 * with the {@linkplain #getInternalParentBeanFactory() internal bean factory} of this
 * context's parent as parent bean factory. Can be overridden in subclasses,
 * for example to customize DefaultListableBeanFactory's settings.
 * @return the bean factory for this context
 * @see org.springframework.beans.factory.support.DefaultListableBeanFactory#setAllowBeanDefinitionOverriding
 * @see org.springframework.beans.factory.support.DefaultListableBeanFactory#setAllowEagerClassLoading
 * @see org.springframework.beans.factory.support.DefaultListableBeanFactory#setAllowCircularReferences
 * @see org.springframework.beans.factory.support.DefaultListableBeanFactory#setAllowRawInjectionDespiteWrapping
 */
protected DefaultListableBeanFactory createBeanFactory() {
   return new DefaultListableBeanFactory(getInternalParentBeanFactory());
}
```

方法介绍：为当前上下文创建一个内部bean factory。

这个方法在每次试图调用refresh()方法时候都会被调用。默认的实现是创建一个把[通过调用此上下文父类的getInternalParentBeanFactory()方法返回的内部bean factory]当做父bean factory的DefaultListableBeanFactory。可以被子类重写，例如用来自定义DefaultListableBeanFactory的配置。

```java
/**
 * Customize the internal bean factory used by this context.
 * Called for each {@link #refresh()} attempt.
 * <p>The default implementation applies this context's
 * {@linkplain #setAllowBeanDefinitionOverriding "allowBeanDefinitionOverriding"}
 * and {@linkplain #setAllowCircularReferences "allowCircularReferences"} settings,
 * if specified. Can be overridden in subclasses to customize any of
 * {@link DefaultListableBeanFactory}'s settings.
 * @param beanFactory the newly created bean factory for this context
 * @see DefaultListableBeanFactory#setAllowBeanDefinitionOverriding
 * @see DefaultListableBeanFactory#setAllowCircularReferences
 * @see DefaultListableBeanFactory#setAllowRawInjectionDespiteWrapping
 * @see DefaultListableBeanFactory#setAllowEagerClassLoading
 */
protected void customizeBeanFactory(DefaultListableBeanFactory beanFactory) {
   if (this.allowBeanDefinitionOverriding != null) {
      beanFactory.setAllowBeanDefinitionOverriding(this.allowBeanDefinitionOverriding);
   }
   if (this.allowCircularReferences != null) {
      beanFactory.setAllowCircularReferences(this.allowCircularReferences);
   }
}
```

方法介绍：自定义当前上下文使用的内部bean factory。这个方法在每次尝试调用refresh()方法的时候调用。

默认的实现应用了当前上下文的setAllowBeanDefinitionOverriding的允许bean定义重写模式还有setAllowCircularReferences的允许循环依赖模式配置，如果显示指定的话。可以被子类重写来自定义任何DefaultListableBeanFactory的配置。

```java
/**
 * Load bean definitions into the given bean factory, typically through
 * delegating to one or more bean definition readers.
 * @param beanFactory the bean factory to load bean definitions into
 * @throws BeansException if parsing of the bean definitions failed
 * @throws IOException if loading of bean definition files failed
 * @see org.springframework.beans.factory.support.PropertiesBeanDefinitionReader
 * @see org.springframework.beans.factory.xml.XmlBeanDefinitionReader
 */
protected abstract void loadBeanDefinitions(DefaultListableBeanFactory beanFactory)
      throws BeansException, IOException;
```

方法介绍：将bean defintions加载到给定的bean factory中，通常通过委托给一个或者多个bean definitions读取器。



















































































