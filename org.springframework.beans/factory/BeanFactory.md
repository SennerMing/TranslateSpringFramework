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

