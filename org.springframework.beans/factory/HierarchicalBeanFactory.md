#### HierarchicalBeanFactory

```java
/**
 * Sub-interface implemented by bean factories that can be part
 * of a hierarchy.
 *
 * <p>The corresponding {@code setParentBeanFactory} method for bean
 * factories that allow setting the parent in a configurable
 * fashion can be found in the ConfigurableBeanFactory interface.
 *
 * @author Rod Johnson
 * @author Juergen Hoeller
 * @since 07.07.2003
 * @see org.springframework.beans.factory.config.ConfigurableBeanFactory#setParentBeanFactory
 */
```

类介绍：BeanFactory的子接口，bean factories实现此接口的话会成为层级结构的一部分。

实现ConfigurableBeanFactory接口中对应的setParentBeanFactory()方法可以为bean factories提供通过配置的方式设置父工厂。

```java
/**
 * Return the parent bean factory, or {@code null} if there is none.
 */
@Nullable
BeanFactory getParentBeanFactory();
```

方法介绍：返回父工厂，如果没有的话返回null。

```java
/**
 * Return whether the local bean factory contains a bean of the given name,
 * ignoring beans defined in ancestor contexts.
 * <p>This is an alternative to {@code containsBean}, ignoring a bean
 * of the given name from an ancestor bean factory.
 * @param name the name of the bean to query
 * @return whether a bean with the given name is defined in the local factory
 * @see BeanFactory#containsBean
 */
boolean containsLocalBean(String name);
```

方法介绍：返回当前bean factory是否包含指定name的bean，忽略在祖先上下文定义的beans。

这个方法是containsBean()的一个替代方法，忽略祖先bean factory中的指定名称的bean。





