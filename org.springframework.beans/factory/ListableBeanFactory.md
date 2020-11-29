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

方法介绍：通过指定的名称检查当前的BeanFactory中是否存在对应的Bean Definition。

不考虑当前工厂可能参与的任何的层次结构，并且忽略任何已经通过除了bean definition以外方式注册的任何单例bean。

```java
/**
 * Return the number of beans defined in the factory.
 * <p>Does not consider any hierarchy this factory may participate in,
 * and ignores any singleton beans that have been registered by
 * other means than bean definitions.
 * @return the number of beans defined in the factory
 */
int getBeanDefinitionCount();
```

方法介绍：返回这个工厂中定义的bean的数量。

不考虑当前工厂可能参与的任何的层次结构，并且忽略任何已经通过除了bean definition以外方式注册的任何单例bean。

```java
/**
 * Return the names of all beans defined in this factory.
 * <p>Does not consider any hierarchy this factory may participate in,
 * and ignores any singleton beans that have been registered by
 * other means than bean definitions.
 * @return the names of all beans defined in this factory,
 * or an empty array if none defined
 */
String[] getBeanDefinitionNames();
```

返回这个工厂中定义的所有bean的名称。

不考虑当前工厂可能参与的任何的层次结构，并且忽略任何已经通过除了bean definition以外方式注册的任何单例bean。

```java
/**
 * Return the names of beans matching the given type (including subclasses),
 * judging from either bean definitions or the value of {@code getObjectType}
 * in the case of FactoryBeans.
 * <p><b>NOTE: This method introspects top-level beans only.</b> It does <i>not</i>
 * check nested beans which might match the specified type as well.
 * <p>Does consider objects created by FactoryBeans, which means that FactoryBeans
 * will get initialized. If the object created by the FactoryBean doesn't match,
 * the raw FactoryBean itself will be matched against the type.
 * <p>Does not consider any hierarchy this factory may participate in.
 * Use BeanFactoryUtils' {@code beanNamesForTypeIncludingAncestors}
 * to include beans in ancestor factories too.
 * <p>Note: Does <i>not</i> ignore singleton beans that have been registered
 * by other means than bean definitions.
 * <p>This version of {@code getBeanNamesForType} matches all kinds of beans,
 * be it singletons, prototypes, or FactoryBeans. In most implementations, the
 * result will be the same as for {@code getBeanNamesForType(type, true, true)}.
 * <p>Bean names returned by this method should always return bean names <i>in the
 * order of definition</i> in the backend configuration, as far as possible.
 * @param type the generically typed class or interface to match
 * @return the names of beans (or objects created by FactoryBeans) matching
 * the given object type (including subclasses), or an empty array if none
 * @since 4.2
 * @see #isTypeMatch(String, ResolvableType)
 * @see FactoryBean#getObjectType
 * @see BeanFactoryUtils#beanNamesForTypeIncludingAncestors(ListableBeanFactory, ResolvableType)
 */
String[] getBeanNamesForType(ResolvableType type);
be matched against：与...相配
```

方法介绍：返回匹配指定类型（包括子类型）的bean的名称，从bean definitions或者是FactoryBean的getObjectType的返回值判断。

注意：这个方法只内省（通过getter、setter去感知bean）最顶级bean。这个方法同样不会检查可能匹配上的Bean的内嵌Beans。

这个方法会考虑FactoryBeans创建的对象，这也就意味着FactoryBeans将会被初始化。如果FactoryBeans创建的对象没有匹配上，那么将会去与这个FactoryBean本身进行匹配。

不会考虑当前工厂可能参与的任何层级结构。可以使用BeanFactoryUtils的beanNamesForTypeIncludingAncestors()去检查包括祖先工厂在内的beans。

注意：不会忽略除已经通过bean definitions以外方式注册的单例bean。

当前版本的getBeanNamesForType匹配所有类型的beans，不管是单例、原型还是工厂Beans，在大多数的实现类中，这个结果将会和getBeanNamesForType(type,true,true)返回的结果一样。

这个方法返回的Bean名称数组应该尽可能的保持其在后端配置定义的名称顺序。

```java
/**
 * Return the names of beans matching the given type (including subclasses),
 * judging from either bean definitions or the value of {@code getObjectType}
 * in the case of FactoryBeans.
 * <p><b>NOTE: This method introspects top-level beans only.</b> It does <i>not</i>
 * check nested beans which might match the specified type as well.
 * <p>Does consider objects created by FactoryBeans if the "allowEagerInit" flag is set,
 * which means that FactoryBeans will get initialized. If the object created by the
 * FactoryBean doesn't match, the raw FactoryBean itself will be matched against the
 * type. If "allowEagerInit" is not set, only raw FactoryBeans will be checked
 * (which doesn't require initialization of each FactoryBean).
 * <p>Does not consider any hierarchy this factory may participate in.
 * Use BeanFactoryUtils' {@code beanNamesForTypeIncludingAncestors}
 * to include beans in ancestor factories too.
 * <p>Note: Does <i>not</i> ignore singleton beans that have been registered
 * by other means than bean definitions.
 * <p>Bean names returned by this method should always return bean names <i>in the
 * order of definition</i> in the backend configuration, as far as possible.
 * @param type the generically typed class or interface to match
 * @param includeNonSingletons whether to include prototype or scoped beans too
 * or just singletons (also applies to FactoryBeans)
 * @param allowEagerInit whether to initialize <i>lazy-init singletons</i> and
 * <i>objects created by FactoryBeans</i> (or by factory methods with a
 * "factory-bean" reference) for the type check. Note that FactoryBeans need to be
 * eagerly initialized to determine their type: So be aware that passing in "true"
 * for this flag will initialize FactoryBeans and "factory-bean" references.
 allowEagerInit决定了是否初始化懒加载单例并且是否对FactoryBeans创建的对象（或者是通过加了factory-bean引用的工厂方法返回的对象）进行类型检查，注意，FactoryBeans需要紧急初始化才能确定他们的类型：所以需要注意如果传进来的allowEagerInit参数为true，那么该方法将会导致初始化FactoryBeans和加了factory-bean引用的工厂方法。
 * @return the names of beans (or objects created by FactoryBeans) matching
 * the given object type (including subclasses), or an empty array if none
 * @since 5.2
 * @see FactoryBean#getObjectType
 * @see BeanFactoryUtils#beanNamesForTypeIncludingAncestors(ListableBeanFactory, ResolvableType, boolean, boolean)
 */
String[] getBeanNamesForType(ResolvableType type, boolean includeNonSingletons, boolean allowEagerInit);
generically：一般的、通用的；
```

方法介绍：返回匹配上指定类型（包括子类型）的bean的name数组，会从bean definitions或者是FactorBean的getObjectType()返回值中判断（类型）。

注意，这个函数只会内省顶层bean，它将同样不会检查已匹配上类型的内嵌beans。

该函数会根据allowEagerInit去考虑FactroyBean创建的对象，这将意味着，相应的FactoryBean会被初始化。当FactoryBean创建的对象类型与指定类型不匹配时，那么将会与FactoryBean本身的类型进行匹配。如果allowEagerInit设置为false，则将直接检查FactoryBean本身的类型（也就不需要去初始化每个FactoryBean了）。

不会去考虑当前工厂可能参与的任何层次结构。

使用BeanFactoryUtils的beanNamesForTypeIncludingAncestors()可以去获取曲线工厂的beans。

注意，不要忽略已经除了用bean definitions以外的其他方式注册的单例beans。

这个方法返回的bean名称的顺序应该尽可能的遵循后端配置文件配置的名称顺序。

```java
/**
 * Return the names of beans matching the given type (including subclasses),
 * judging from either bean definitions or the value of {@code getObjectType}
 * in the case of FactoryBeans.
 * <p><b>NOTE: This method introspects top-level beans only.</b> It does <i>not</i>
 * check nested beans which might match the specified type as well.
 * <p>Does consider objects created by FactoryBeans, which means that FactoryBeans
 * will get initialized. If the object created by the FactoryBean doesn't match,
 * the raw FactoryBean itself will be matched against the type.
 * <p>Does not consider any hierarchy this factory may participate in.
 * Use BeanFactoryUtils' {@code beanNamesForTypeIncludingAncestors}
 * to include beans in ancestor factories too.
 * <p>Note: Does <i>not</i> ignore singleton beans that have been registered
 * by other means than bean definitions.
 * <p>This version of {@code getBeanNamesForType} matches all kinds of beans,
 * be it singletons, prototypes, or FactoryBeans. In most implementations, the
 * result will be the same as for {@code getBeanNamesForType(type, true, true)}.
 * <p>Bean names returned by this method should always return bean names <i>in the
 * order of definition</i> in the backend configuration, as far as possible.
 * @param type the class or interface to match, or {@code null} for all bean names
 * @return the names of beans (or objects created by FactoryBeans) matching
 * the given object type (including subclasses), or an empty array if none
 * @see FactoryBean#getObjectType
 * @see BeanFactoryUtils#beanNamesForTypeIncludingAncestors(ListableBeanFactory, Class)
 */
String[] getBeanNamesForType(@Nullable Class<?> type);
```

方法介绍：返回匹配上指定类型的bean的名称。会根据bean definitions或者FactoryBeans中的getObejctType()的返回值进行类型判断。

注意：这个方法只会内省最顶层级的bean，它同样将不会检查可能匹配上的Bean的内嵌bean。

考虑到FactoryBeans创建的对象，这将意味着FactoryBeans将会进行初始化。如果FactoryBean创建的对象不匹配，则将会与这个原FactoryBean本身类型进行匹配。

不会去考虑当前工厂参与的任何层级结构。

使用BeanFactoryUtils的beanNamesForTypeIncludingAncestors()去将祖先工厂的beans也曾参与进来。

注意：不要忽略除已经通过bean definitions以外方式注册的单例beans。

当前版本的getBeanNamesForType匹配所有类型的beans，不管是单例、原型还是工厂Beans。在大多数的实现中，这个方法的返回结果将会和getBeanNamesForType(type,true,true)的返回结果相同。Bean names返回的顺序应该尽可能的和后端配置中definitions中定义的顺序相同。

```java
/**
 * Return the names of beans matching the given type (including subclasses),
 * judging from either bean definitions or the value of {@code getObjectType}
 * in the case of FactoryBeans.
 * <p><b>NOTE: This method introspects top-level beans only.</b> It does <i>not</i>
 * check nested beans which might match the specified type as well.
 * <p>Does consider objects created by FactoryBeans if the "allowEagerInit" flag is set,
 * which means that FactoryBeans will get initialized. If the object created by the
 * FactoryBean doesn't match, the raw FactoryBean itself will be matched against the
 * type. If "allowEagerInit" is not set, only raw FactoryBeans will be checked
 * (which doesn't require initialization of each FactoryBean).
 * <p>Does not consider any hierarchy this factory may participate in.
 * Use BeanFactoryUtils' {@code beanNamesForTypeIncludingAncestors}
 * to include beans in ancestor factories too.
 * <p>Note: Does <i>not</i> ignore singleton beans that have been registered
 * by other means than bean definitions.
 * <p>Bean names returned by this method should always return bean names <i>in the
 * order of definition</i> in the backend configuration, as far as possible.
 * @param type the class or interface to match, or {@code null} for all bean names
 * @param includeNonSingletons whether to include prototype or scoped beans too
 * or just singletons (also applies to FactoryBeans)
 * @param allowEagerInit whether to initialize <i>lazy-init singletons</i> and
 * <i>objects created by FactoryBeans</i> (or by factory methods with a
 * "factory-bean" reference) for the type check. Note that FactoryBeans need to be
 * eagerly initialized to determine their type: So be aware that passing in "true"
 * for this flag will initialize FactoryBeans and "factory-bean" references.
 * @return the names of beans (or objects created by FactoryBeans) matching
 * the given object type (including subclasses), or an empty array if none
 * @see FactoryBean#getObjectType
 * @see BeanFactoryUtils#beanNamesForTypeIncludingAncestors(ListableBeanFactory, Class, boolean, boolean)
 */
String[] getBeanNamesForType(@Nullable Class<?> type, boolean includeNonSingletons, boolean allowEagerInit);
```

方法介绍：返回根据类型（包括子类）匹配上的bean的名称，会从bean definitions或者是FactoryBeans的getObjectType()的返回值进行类型判断。

注意，这个方法只会内省最顶层的beans，他将同样不会检查匹配类型的嵌套beans。

会去考虑FactoryBeans创建的对象，如果allowEagerInit设置为true，这意味着FactoryBeans将会被初始化。如果FactoryBean创建的对象不匹配，那么将会去与FactoryBean的原类型去匹配。如果allowEagerInit设置为false，则仅匹配FactoryBeans的原本类型（这就意味着不会对FactoryBean进行初始化操作）。

不会考虑任何当前工厂参与的层级结构。

使用BeanFactoryUtils的beanNamesForTypeIncludingAncestors()可将祖先工厂中的beans纳入考虑范围。

注意：不要忽略除了通过bean definitions方式的其他方式已经注册的单例bean。

Bean名称的返回顺序应该尽可能的遵循后端配置定义的顺序。

```java
/**
 * Return the bean instances that match the given object type (including
 * subclasses), judging from either bean definitions or the value of
 * {@code getObjectType} in the case of FactoryBeans.
 * <p><b>NOTE: This method introspects top-level beans only.</b> It does <i>not</i>
 * check nested beans which might match the specified type as well.
 * <p>Does consider objects created by FactoryBeans, which means that FactoryBeans
 * will get initialized. If the object created by the FactoryBean doesn't match,
 * the raw FactoryBean itself will be matched against the type.
 * <p>Does not consider any hierarchy this factory may participate in.
 * Use BeanFactoryUtils' {@code beansOfTypeIncludingAncestors}
 * to include beans in ancestor factories too.
 * <p>Note: Does <i>not</i> ignore singleton beans that have been registered
 * by other means than bean definitions.
 * <p>This version of getBeansOfType matches all kinds of beans, be it
 * singletons, prototypes, or FactoryBeans. In most implementations, the
 * result will be the same as for {@code getBeansOfType(type, true, true)}.
 * <p>The Map returned by this method should always return bean names and
 * corresponding bean instances <i>in the order of definition</i> in the
 * backend configuration, as far as possible.
 * @param type the class or interface to match, or {@code null} for all concrete beans
 * @return a Map with the matching beans, containing the bean names as
 * keys and the corresponding bean instances as values
 * @throws BeansException if a bean could not be created
 * @since 1.1.2
 * @see FactoryBean#getObjectType
 * @see BeanFactoryUtils#beansOfTypeIncludingAncestors(ListableBeanFactory, Class)
 */
<T> Map<String, T> getBeansOfType(@Nullable Class<T> type) throws BeansException;
```

方法介绍：返回指定类型匹配上的beans实例，会从bean definitions或者是FactoryBeans的getObjectType()的返回值中判断。

注意：这个方法只会内省最顶层级的beans。他同样不会去检查匹配上bean中的内嵌bean。

考虑到FactoryBeans创建的Beans，这意味着FactoryBeans会被初始化。如果FactoryBeans创建的对象没有匹配上，那么将会与FactoryBean本身的类型进行匹配。

不会考虑所有这个工厂可能参与的层级关系。

可以使用BeanFactoryUtils的beansOfTypeIncludingAncestors()方法将祖先工厂中的beans纳入考虑范围。

不要忽略已经注册的除bean definitions以外其他方式的单例beans。

当前版本的getBeansOfType会匹配所有beans类型，包括单例、原型或者FactoryBeans。在大多数实现类型中，这个方法的返回结果会和getBeansOfType(type,true,true)相同。

返回的这个Map中对应bean实例的名称尽可能和后端配置定义的顺序相同。

```java
/**
 * Return the bean instances that match the given object type (including
 * subclasses), judging from either bean definitions or the value of
 * {@code getObjectType} in the case of FactoryBeans.
 * <p><b>NOTE: This method introspects top-level beans only.</b> It does <i>not</i>
 * check nested beans which might match the specified type as well.
 * <p>Does consider objects created by FactoryBeans if the "allowEagerInit" flag is set,
 * which means that FactoryBeans will get initialized. If the object created by the
 * FactoryBean doesn't match, the raw FactoryBean itself will be matched against the
 * type. If "allowEagerInit" is not set, only raw FactoryBeans will be checked
 * (which doesn't require initialization of each FactoryBean).
 * <p>Does not consider any hierarchy this factory may participate in.
 * Use BeanFactoryUtils' {@code beansOfTypeIncludingAncestors}
 * to include beans in ancestor factories too.
 * <p>Note: Does <i>not</i> ignore singleton beans that have been registered
 * by other means than bean definitions.
 * <p>The Map returned by this method should always return bean names and
 * corresponding bean instances <i>in the order of definition</i> in the
 * backend configuration, as far as possible.
 * @param type the class or interface to match, or {@code null} for all concrete beans
 * @param includeNonSingletons whether to include prototype or scoped beans too
 * or just singletons (also applies to FactoryBeans)
 * @param allowEagerInit whether to initialize <i>lazy-init singletons</i> and
 * <i>objects created by FactoryBeans</i> (or by factory methods with a
 * "factory-bean" reference) for the type check. Note that FactoryBeans need to be
 * eagerly initialized to determine their type: So be aware that passing in "true"
 * for this flag will initialize FactoryBeans and "factory-bean" references.
 * @return a Map with the matching beans, containing the bean names as
 * keys and the corresponding bean instances as values
 * @throws BeansException if a bean could not be created
 * @see FactoryBean#getObjectType
 * @see BeanFactoryUtils#beansOfTypeIncludingAncestors(ListableBeanFactory, Class, boolean, boolean)
 */
<T> Map<String, T> getBeansOfType(@Nullable Class<T> type, boolean includeNonSingletons, boolean allowEagerInit)
      throws BeansException;
```

方法介绍：返回指定类型(包括子类)匹配的beans实例，会从bean definitions或者FactoryBeans的getObjectType的返回值进行判断。

注意：这个方法只会内省最顶层的bean，将同样不会检查匹配类型beans的嵌套beans。

如果allowEagerInit设置为true，那么将会检查FactoryBean创建的对象，这意味着FatoryBeans将会被初始化。如果FactoryBean创建的对象没有匹配上，则会与FactoryBean本身的类型进行匹配。如果allowEagerInit设置为false，则只检查FactoryBeans原本的类型。（这意味着不需要初始化每个FactoryBean）。

不会考虑当前工厂的参与的所有层级关系。

使用BeanFactoryUtils中的beansOfTypeIncludingAncestors()将所有祖先的beans也纳入考虑范围。

注意：不要忽略已经除了使用bean definition方式注册的单例beans

这个方法返回的Map对应的bean实例的名称顺序尽可能的和后端定义的顺序相同。

```java
/**
 * Find all names of beans which are annotated with the supplied {@link Annotation}
 * type, without creating corresponding bean instances yet.
 * <p>Note that this method considers objects created by FactoryBeans, which means
 * that FactoryBeans will get initialized in order to determine their object type.
 * @param annotationType the type of annotation to look for
 * (at class, interface or factory method level of the specified bean)
 * @return the names of all matching beans
 * @since 4.0
 * @see #findAnnotationOnBean
 */
String[] getBeanNamesForAnnotation(Class<? extends Annotation> annotationType);
yet：只知道是至此还未，也可以用于否定句，表示但是，然鹅。
```

方法介绍：找到有指定注解所有beans的名称，而不创建对应的bean实例。

注意这个方法会考虑到FactoryBean创建的对象，这就意味着为了确定他们的对象类型FactoryBeans将会被初始化。

```java
/**
 * Find all beans which are annotated with the supplied {@link Annotation} type,
 * returning a Map of bean names with corresponding bean instances.
 * <p>Note that this method considers objects created by FactoryBeans, which means
 * that FactoryBeans will get initialized in order to determine their object type.
 * @param annotationType the type of annotation to look for
 * (at class, interface or factory method level of the specified bean)
 * @return a Map with the matching beans, containing the bean names as
 * keys and the corresponding bean instances as values
 * @throws BeansException if a bean could not be created
 * @since 3.0
 * @see #findAnnotationOnBean
 */
Map<String, Object> getBeansWithAnnotation(Class<? extends Annotation> annotationType) throws BeansException;
```

方法介绍：找到指定注解类型的所有beans，返回一个bean names及其对应的bean实例的Map。

注意这个方法将会考虑FactoryBeans创建的对象，这将意味着为了确定FactoryBeans的类型他们将会被初始化。

```java
/**
 * Find an {@link Annotation} of {@code annotationType} on the specified bean,
 * traversing its interfaces and super classes if no annotation can be found on
 * the given class itself, as well as checking the bean's factory method (if any).
 * @param beanName the name of the bean to look for annotations on
 * @param annotationType the type of annotation to look for
 * (at class, interface or factory method level of the specified bean)
 * @return the annotation of the given type if found, or {@code null} otherwise
 * @throws NoSuchBeanDefinitionException if there is no bean with the given name
 * @since 3.0
 * @see #getBeanNamesForAnnotation
 * @see #getBeansWithAnnotation
 */
@Nullable
<A extends Annotation> A findAnnotationOnBean(String beanName, Class<A> annotationType)
      throws NoSuchBeanDefinitionException;
```

方法介绍：通过annotationType条件找到指定的Annotation，如果在Bean的自身没找到，那么将会遍历他实现的接口及其父类，同样也会检查bean的工厂方法（如果存在的话）。















