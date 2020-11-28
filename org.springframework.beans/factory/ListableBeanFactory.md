#### ListableBeanFactory

```java
/**
 * Extension of the {@link BeanFactory} interface to be implemented by bean factories
 * that can enumerate all their bean instances, rather than attempting bean lookup
 * by name one by one as requested by clients. BeanFactory implementations that
 * preload all their bean definitions (such as XML-based factories) may implement
 * this interface.
 *
 * <p>If this is a {@link HierarchicalBeanFactory}, the return values will <i>not</i>
 * take any BeanFactory hierarchy into account, but will relate only to the beans
 * defined in the current factory. Use the {@link BeanFactoryUtils} helper class
 * to consider beans in ancestor factories too.
 *
 * <p>The methods in this interface will just respect bean definitions of this factory.
 * They will ignore any singleton beans that have been registered by other means like
 * {@link org.springframework.beans.factory.config.ConfigurableBeanFactory}'s
 * {@code registerSingleton} method, with the exception of
 * {@code getBeanNamesOfType} and {@code getBeansOfType} which will check
 * such manually registered singletons too. Of course, BeanFactory's {@code getBean}
 * does allow transparent access to such special beans as well. However, in typical
 * scenarios, all beans will be defined by external bean definitions anyway, so most
 * applications don't need to worry about this differentiation.
 *
 * <p><b>NOTE:</b> With the exception of {@code getBeanDefinitionCount}
 * and {@code containsBeanDefinition}, the methods in this interface
 * are not designed for frequent invocation. Implementations may be slow.
 *
 * @author Rod Johnson
 * @author Juergen Hoeller
 * @since 16 April 2001
 * @see HierarchicalBeanFactory
 * @see BeanFactoryUtils
 */
enumerate:枚举、列举，大家都不陌生
take xxx into account：将xxx考虑在内
respect：尊重、维护、重视
with the exception of:除了...以外
scenarios：设想、方案、后果、情况
```

ListableBeanFactory是对BeanFactory规范的一个扩展，会被很多bean工厂实现。实现这个规范的bean factory就具备了枚举他们持有的bean实例的能力，比客户端一个接一个发送请求去尝试使用指定名称查找bean的方式要好的多（可能指的就是BeanFctory接口中的getBean()吧😀）。BeanFactory的实现类们会提前加载好他们所有的bean definition（像是通过基于XML方式bean definition的工厂们），这些BeanFactory会实现这个规范（接口）。

如果当前BeanFactory实现了HierarchicalBeanFactory，那么返回值将不会考虑任何的BeanFactory的层次结构，而会之关联当前工厂内定义的bean。使用BeanFactoryUtils工具类可以将祖先工厂的bean也考虑在内。

这个规范中的方法将只会维护（重视）当前工厂的bean definition。这些方法将会忽略任何已经通过其他方式注册的单例bean，就比如通过ConfigurableBeanFactory的registerSingleton()注册的单例bean，除了getBeanNamesOfType()和getBeansOfType()方法以外，因为他们也会去检查这些手动注册的单例。当然，BeanFactory的getBean()也确实允许透明的去获取这些特殊的bean。但是吧，在一般情况下，所有的bean将被定义由外部的bean definitions来定义。因此大多数的应用程序不必担心这种区别。（意思就是不管是单例还是原型还是什么类型的bean，都是外部bean definition事先定义好的，像是使用到ConfigurableBeanFactory的registerSingleton()方法的情况是很少的。暂时是这样理解的）。

注意：除了getBeanDefinitionCount()和ContainsBeanDefinition()以外，这个接口中的其他方法并不是为频繁调用而设计的，实现类会变得很慢。

```java
/**
 * Check if this bean factory contains a bean definition with the given name.
 * <p>Does not consider any hierarchy this factory may participate in,
 * and ignores any singleton beans that have been registered by
 * other means than bean definitions.
 * @param beanName the name of the bean to look for
 * @return if this bean factory contains a bean definition with the given name
 * @see #containsBean
 */
boolean containsBeanDefinition(String beanName);
```

