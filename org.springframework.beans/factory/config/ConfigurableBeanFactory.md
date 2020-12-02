#### ConfigurableBeanFactory

```java
/**
 * Configuration interface to be implemented by most bean factories. Provides
 * facilities to configure a bean factory, in addition to the bean factory
 * client methods in the {@link org.springframework.beans.factory.BeanFactory}
 * interface.
 *
 * <p>This bean factory interface is not meant to be used in normal application
 * code: Stick to {@link org.springframework.beans.factory.BeanFactory} or
 * {@link org.springframework.beans.factory.ListableBeanFactory} for typical
 * needs. This extended interface is just meant to allow for framework-internal
 * plug'n'play and for special access to bean factory configuration methods.
 *
 * @author Juergen Hoeller
 * @since 03.11.2003
 * @see org.springframework.beans.factory.BeanFactory
 * @see org.springframework.beans.factory.ListableBeanFactory
 * @see ConfigurableListableBeanFactory
 */
```

类介绍：这是一个被大多数bean工厂实现的配置接口。除了BeanFactory接口中提供的工厂客户端方法，另外还提供了配置一个bean工厂的工具。

这个bean工厂接口的本意并不是普通应用代码中使用的：在经典的需求当中，请坚持使用BeanFactory或者ListableBeanFactory。这个扩展的接口仅用于框架内部plug'n'play（即插即用）和专用于获取bean工厂配置的方法。