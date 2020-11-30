#### AutowireCapableBeanFactory

```java
/**
 * Extension of the {@link org.springframework.beans.factory.BeanFactory}
 * interface to be implemented by bean factories that are capable of
 * autowiring, provided that they want to expose this functionality for
 * existing bean instances.
 *
 * <p>This subinterface of BeanFactory is not meant to be used in normal
 * application code: stick to {@link org.springframework.beans.factory.BeanFactory}
 * or {@link org.springframework.beans.factory.ListableBeanFactory} for
 * typical use cases.
 *
 * <p>Integration code for other frameworks can leverage this interface to
 * wire and populate existing bean instances that Spring does not control
 * the lifecycle of. This is particularly useful for WebWork Actions and
 * Tapestry Page objects, for example.
 *
 * <p>Note that this interface is not implemented by
 * {@link org.springframework.context.ApplicationContext} facades,
 * as it is hardly ever used by application code. That said, it is available
 * from an application context too, accessible through ApplicationContext's
 * {@link org.springframework.context.ApplicationContext#getAutowireCapableBeanFactory()}
 * method.
 *
 * <p>You may also implement the {@link org.springframework.beans.factory.BeanFactoryAware}
 * interface, which exposes the internal BeanFactory even when running in an
 * ApplicationContext, to get access to an AutowireCapableBeanFactory:
 * simply cast the passed-in BeanFactory to AutowireCapableBeanFactory.
 *
 * @author Juergen Hoeller
 * @since 04.12.2003
 * @see org.springframework.beans.factory.BeanFactoryAware
 * @see org.springframework.beans.factory.config.ConfigurableListableBeanFactory
 * @see org.springframework.context.ApplicationContext#getAutowireCapableBeanFactory()
 */
provided that：假如，设若
meant to be：命中注定，意味着
stick to：坚持不变；遵守；坚持（原则、诺言、决定、协议）
typical：典型的、有代表性的；一贯的；平常的；特有的（MD，总是搞不清，到底是特有的还是平常的）
leverage：杠杆力；有能力；手段；优势；举债经营；为...融资
His function as a Mayor affords him the leverage to get things done through 		attending committee meetings. 
他的市长身份使他有能力通过出席委员会会议来达成一些事情。
populate：居住于，生活于；迁移；（给文件）增添数据，输入数据
as it is：实际上，事实上
hardly ever：几乎从不
```

类介绍：AutowireCapableBeanFactory是BeanFactory的一个扩展接口，实现此接口的bean factories将会获得自动装配的能力，假如他们希望将此功能暴露给已经存在的bean实例。

这个BeanFactory的子接口的本意并不是为了在普通应用程序的代码中使用：在经典用例中，请坚持使用BeanFactory或者是ListableBeanFactory。

其他框架的集成代码可以借用这个接口，去装配和填充那些生命周期不受Spring框架控制的已经存在的beans，例如，这对于WebWork操作和Tapestry页面对象特别有用。

注意这个接口不是被ApplicationContext直接实现的（可以看关系类图），这个接口几乎从不在应用代码中使用。这就是说，他可以被应用上下文获取，通过ApplicationContext的getAutowireCapableBeanFactory()方法去获取。

你也有可能去实现另一个接口，BeanFactoryAware，这个接口即使在ApplicationContext中运行时，也会把内部BeanFactory暴露出去。怎样获得一个AutowireCapableBeanFactory呢？只需将传进的BeanFactory直接强转成AutowireCapableBeanFactory就行。















