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

```java
/**
 * Constant that indicates no externally defined autowiring. Note that
 * BeanFactoryAware etc and annotation-driven injection will still be applied.
 * @see #createBean
 * @see #autowire
 * @see #autowireBeanProperties
 */
int AUTOWIRE_NO = 0;
```

属性介绍：标识没有外部定义的自动装配常量，注意BeanFactoryAware等和注解驱动的注入仍将会应用。

```java
/**
 * Constant that indicates autowiring bean properties by name
 * (applying to all bean property setters).
 * @see #createBean
 * @see #autowire
 * @see #autowireBeanProperties
 */
int AUTOWIRE_BY_NAME = 1;
```

属性介绍：标识根据名称装配bean属性的常量（适用于所有bean属性的setters方法）

```java
/**
 * Constant that indicates autowiring bean properties by type
 * (applying to all bean property setters).
 * @see #createBean
 * @see #autowire
 * @see #autowireBeanProperties
 */
int AUTOWIRE_BY_TYPE = 2;
```

属性介绍：标识根据类型装配bean属性的常量（适用于所有bean属性的setters方法）

```java
/**
 * Constant that indicates autowiring the greediest constructor that
 * can be satisfied (involves resolving the appropriate constructor).
 * @see #createBean
 * @see #autowire
 */
int AUTOWIRE_CONSTRUCTOR = 3;
greediest：最贪婪的（莫非是参数最多的那个构造器？greedy，嗯嗯）
involves：使参与；加入；包含；需要；
appropriate：适当的；合适的；
```

属性介绍：标识装配满足条件的最贪婪地构造器（需要解析合适的构造器）

```java
/**
 * Constant that indicates determining an appropriate autowire strategy
 * through introspection of the bean class.
 * @see #createBean
 * @see #autowire
 * @deprecated as of Spring 3.0: If you are using mixed autowiring strategies,
 * prefer annotation-based autowiring for clearer demarcation of autowiring needs.
 */
@Deprecated（已经废弃掉了）
int AUTOWIRE_AUTODETECT = 4;
introspection：内省
```

属性介绍：标识通过对bean类的内省机制，确定一种适当的装配策略

```java
/**
 * Suffix for the "original instance" convention when initializing an existing
 * bean instance: to be appended to the fully-qualified bean class name,
 * e.g. "com.mypackage.MyClass.ORIGINAL", in order to enforce the given instance
 * to be returned, i.e. no proxies etc.
 * @since 5.1
 * @see #initializeBean(Object, String)
 * @see #applyBeanPostProcessorsBeforeInitialization(Object, String)
 * @see #applyBeanPostProcessorsAfterInitialization(Object, String)
 */
String ORIGINAL_INSTANCE_SUFFIX = ".ORIGINAL";
convention：习惯；惯例；常规；公约，协定，协议；
qualified：具备...资历；具备...能力；符合资格；有限度的；有条件的
```

属性介绍：是”原始实例“约定的后缀，当初始化一个已经存在的bean实例时：会被添加到一个全限定的bean的类名称后面，例如："com.mypackage.MyClass.ORIGINAL"，这样做是为了强制返回指定实例，即没有代理等

```java
//-------------------------------------------------------------------------
// Typical methods for creating and populating external bean instances
//-------------------------------------------------------------------------
```

注释翻译：一些创建或者填充外部bean实例的经典方法

```java
/**
 * Fully create a new bean instance of the given class.
 * <p>Performs full initialization of the bean, including all applicable
 * {@link BeanPostProcessor BeanPostProcessors}.
 * <p>Note: This is intended for creating a fresh instance, populating annotated
 * fields and methods as well as applying all standard bean initialization callbacks.
 * It does <i>not</i> imply traditional by-name or by-type autowiring of properties;
 * use {@link #createBean(Class, int, boolean)} for those purposes.
 * @param beanClass the class of the bean to create
 * @return the new bean instance
 * @throws BeansException if instantiation or wiring failed
 */
<T> T createBean(Class<T> beanClass) throws BeansException;
```

方法介绍：通过指定的类，完全创建一个bean实例

执行bean的完全初始化操作，包括所有可应用的BeanPostProcessors（Bean的派驻处理器，人们常说的Bean后置处理器吧）

注意：这个方法的意图是创建一个全新的实例，填充带注解的属性和方法同样也会应用于所有bean的标准初始化回调

```java
/**
 * Populate the given bean instance through applying after-instantiation callbacks
 * and bean property post-processing (e.g. for annotation-driven injection).
 * <p>Note: This is essentially intended for (re-)populating annotated fields and
 * methods, either for new instances or for deserialized instances. It does
 * <i>not</i> imply traditional by-name or by-type autowiring of properties;
 * use {@link #autowireBeanProperties} for those purposes.
 * @param existingBean the existing bean instance
 * @throws BeansException if wiring failed
 */
void autowireBean(Object existingBean) throws BeansException;
essentially：本质上，基本上
imply：含有...的意思；暗示；暗指；说明；
```

方法介绍：通过应用after-instantiation（后实例化）回调和bean属性的post-processing（派驻数据处理）（例如：用于注解驱动注入）来填充给定的bean实例。

注意：这个方法本质的意图是为了（重新）填充带注解的属性和方法，无论是新的实例还是反序列化的实例。这不意味着传统的按名称或者按类型进行属性的装配，如果需要这样装配，请使用autowireBeanProperties()

```java
/**
 * Configure the given raw bean: autowiring bean properties, applying
 * bean property values, applying factory callbacks such as {@code setBeanName}
 * and {@code setBeanFactory}, and also applying all bean post processors
 * (including ones which might wrap the given raw bean).
 * <p>This is effectively a superset of what {@link #initializeBean} provides,
 * fully applying the configuration specified by the corresponding bean definition.
 * <b>Note: This method requires a bean definition for the given name!</b>
 * @param existingBean the existing bean instance
 * @param beanName the name of the bean, to be passed to it if necessary
 * (a bean definition of that name has to be available)
 * @return the bean instance to use, either the original or a wrapped one
 * @throws org.springframework.beans.factory.NoSuchBeanDefinitionException
 * if there is no bean definition with the given name
 * @throws BeansException if the initialization failed
 * @see #initializeBean
 */
Object configureBean(Object existingBean, String beanName) throws BeansException;
effectively：实际上
```

方法介绍：配置给定的原始bean：装配bean的属性，应用（暂时找不到好的词汇代替，相当于一个获取的操作吧）bean属性的值，调用工厂回调像是setBeanName()和setBeanFactory()，并且还会应用所有的bean派驻（后置）处理器（包括那些可能已经包装在bean上的）

这实际上是initializeBean提供的超集，完全应用了相应bean定义指定的配置。

注意：这个方法需要指定beanName的bean definition

```java
//-------------------------------------------------------------------------
// Specialized methods for fine-grained control over the bean lifecycle
//-------------------------------------------------------------------------
fine-grained：细粒度的
```

注释介绍：对bean的生命周期进行细粒度的控制的专业方法

```java
/**
 * Fully create a new bean instance of the given class with the specified
 * autowire strategy. All constants defined in this interface are supported here.
 * <p>Performs full initialization of the bean, including all applicable
 * {@link BeanPostProcessor BeanPostProcessors}. This is effectively a superset
 * of what {@link #autowire} provides, adding {@link #initializeBean} behavior.
 * @param beanClass the class of the bean to create
 * @param autowireMode by name or type, using the constants in this interface
 * @param dependencyCheck whether to perform a dependency check for objects
 * (not applicable to autowiring a constructor, thus ignored there)
 dependencyCheck 是否对对象执行依赖检查
 （不会应用于自动装配一个构造器，因此会在忽略那里）
 * @return the new bean instance
 * @throws BeansException if instantiation or wiring failed
 * @see #AUTOWIRE_NO
 * @see #AUTOWIRE_BY_NAME
 * @see #AUTOWIRE_BY_TYPE
 * @see #AUTOWIRE_CONSTRUCTOR
 */
Object createBean(Class<?> beanClass, int autowireMode, boolean dependencyCheck) throws BeansException;
effectively：有效地；实际上；事实上
```

方法介绍：通过指定的自动装配策略，根据指定类型完全创建一个新的bean实例。所有在本接口中定义的常量，这个函数都支持（上面的AUTOWIRE_开头的）。

执行bean的完全初始化，包括所有适用的BeanpostProcessors。这实际上是autowire(自动装配)的提供的一个超集，添加了initializeBean(初始化Bean)的行为。

```java
/**
 * Instantiate a new bean instance of the given class with the specified autowire
 * strategy. All constants defined in this interface are supported here.
 * Can also be invoked with {@code AUTOWIRE_NO} in order to just apply
 * before-instantiation callbacks (e.g. for annotation-driven injection).
 * <p>Does <i>not</i> apply standard {@link BeanPostProcessor BeanPostProcessors}
 * callbacks or perform any further initialization of the bean. This interface
 * offers distinct, fine-grained operations for those purposes, for example
 * {@link #initializeBean}. However, {@link InstantiationAwareBeanPostProcessor}
 * callbacks are applied, if applicable to the construction of the instance.
 * @param beanClass the class of the bean to instantiate
 * @param autowireMode by name or type, using the constants in this interface
 * @param dependencyCheck whether to perform a dependency check for object
 * references in the bean instance (not applicable to autowiring a constructor,
 * thus ignored there)
 * @return the new bean instance
 * @throws BeansException if instantiation or wiring failed
 * @see #AUTOWIRE_NO
 * @see #AUTOWIRE_BY_NAME
 * @see #AUTOWIRE_BY_TYPE
 * @see #AUTOWIRE_CONSTRUCTOR
 * @see #AUTOWIRE_AUTODETECT
 * @see #initializeBean
 * @see #applyBeanPostProcessorsBeforeInitialization
 * @see #applyBeanPostProcessorsAfterInitialization
 */
Object autowire(Class<?> beanClass, int autowireMode, boolean dependencyCheck) throws BeansException;
distinct：清晰的；清楚的；明白的；明显的；截然不同的；有区别的；
```

方法介绍：通过给定的class与指定的自动装配的策略实例化一个新的bean实例。当前接口定义的所有的常量，这个方法都支持。也可以为了仅应用before-instantiation（实例化前）的回调，那么就需要通过传入AUTOWIRE_NO（一种装配策略）调用这个方法（例如：用于注解驱动注入）。

不会对这个bean应用标准的BeanPostProcessors回调或者执行任何更进一步的初始化操作。对于这些自动装配操作（指定模式啦，按照类型实例化啦，要不要检查依赖啦），这个接口提供清晰的、细粒化的操作，initializeBean就是例证。但是呢，如果适用于实例的构造，InstantiationAwareBeanPostProcessor的回调函数将会被应用。

```java
/**
 * Autowire the bean properties of the given bean instance by name or type.
 * Can also be invoked with {@code AUTOWIRE_NO} in order to just apply
 * after-instantiation callbacks (e.g. for annotation-driven injection).
 * <p>Does <i>not</i> apply standard {@link BeanPostProcessor BeanPostProcessors}
 * callbacks or perform any further initialization of the bean. This interface
 * offers distinct, fine-grained operations for those purposes, for example
 * {@link #initializeBean}. However, {@link InstantiationAwareBeanPostProcessor}
 * callbacks are applied, if applicable to the configuration of the instance.
 * @param existingBean the existing bean instance
 * @param autowireMode by name or type, using the constants in this interface
 * @param dependencyCheck whether to perform a dependency check for object
 * references in the bean instance
 * @throws BeansException if wiring failed
 * @see #AUTOWIRE_BY_NAME
 * @see #AUTOWIRE_BY_TYPE
 * @see #AUTOWIRE_NO
 */
void autowireBeanProperties(Object existingBean, int autowireMode, boolean dependencyCheck)
      throws BeansException;
```

方法介绍：根据指定的bean实例，通过名称或者类型对bean的属性进行自动装配。为了仅应用after-instantiation（后实例化）的回调，也可以通过传入AUTOWIRE_NO进行调用（例如：用于注解驱动注入）。

对于指定的bean不会应用标准BeanPostProcessors的回调或者执行任何进一步的初始化操作。对于这些自动装配属性的目的，这个接口提供清晰的，细粒化的操作，举个例子：initializeBean()。但是，如果适用于实例的构造，InstantiationAwareBeanPostProcessor的回调将会被应用。

```java
/**
 * Apply the property values of the bean definition with the given name to
 * the given bean instance. The bean definition can either define a fully
 * self-contained bean, reusing its property values, or just property values
 * meant to be used for existing bean instances.
 * <p>This method does <i>not</i> autowire bean properties; it just applies
 * explicitly defined property values. Use the {@link #autowireBeanProperties}
 * method to autowire an existing bean instance.
 * <b>Note: This method requires a bean definition for the given name!</b>
 * <p>Does <i>not</i> apply standard {@link BeanPostProcessor BeanPostProcessors}
 * callbacks or perform any further initialization of the bean. This interface
 * offers distinct, fine-grained operations for those purposes, for example
 * {@link #initializeBean}. However, {@link InstantiationAwareBeanPostProcessor}
 * callbacks are applied, if applicable to the configuration of the instance.
 * @param existingBean the existing bean instance
 * @param beanName the name of the bean definition in the bean factory
 * (a bean definition of that name has to be available)
 * @throws org.springframework.beans.factory.NoSuchBeanDefinitionException
 * if there is no bean definition with the given name
 * @throws BeansException if applying the property values failed
 * @see #autowireBeanProperties
 */
void applyBeanPropertyValues(Object existingBean, String beanName) throws BeansException;
self-contained：设备齐全的；独立的
either：两者中的任何一个；每个；（否定）要么...要么，不是...就是
```

方法介绍：将bean definition中定义的属性值应用于给定的bean实例。bean defition能，不是通过复用他的属性值得方式定义一个独立的bean，就是只是属性值纯属被用在存在的bean实例上（翻译的不好）。

这个方法不会自动装配bean属性；它只是应用显式定义的属性值。使用autowireBeanProperties()方法去自动装配一个存在的bean实例。

注意：这个方法需要一个给定名称的bean definition。

不会应用标准的BeanPostProcessors回调或者执行bean的任何进一步的初始化操作。对于应用Bean属性值的行为，这个接口提供清晰的，细粒化的操作，举个例子：initializeBean()。但是如果适用于实例的构造，那么将会应用InstantiatioAwareBeanPostProcessor的回调。

```java
/**
 * Initialize the given raw bean, applying factory callbacks
 * such as {@code setBeanName} and {@code setBeanFactory},
 * also applying all bean post processors (including ones which
 * might wrap the given raw bean).
 * <p>Note that no bean definition of the given name has to exist
 * in the bean factory. The passed-in bean name will simply be used
 * for callbacks but not checked against the registered bean definitions.
 * @param existingBean the existing bean instance
 * @param beanName the name of the bean, to be passed to it if necessary
 * (only passed to {@link BeanPostProcessor BeanPostProcessors};
 * can follow the {@link #ORIGINAL_INSTANCE_SUFFIX} convention in order to
 * enforce the given instance to be returned, i.e. no proxies etc)
 * @return the bean instance to use, either the original or a wrapped one
 * @throws BeansException if the initialization failed
 * @see #ORIGINAL_INSTANCE_SUFFIX
 */
Object initializeBean(Object existingBean, String beanName) throws BeansException;
```

方法介绍：初始化给定的原始bean，通过应用工厂回调例如setBeanName()和setBeanFactory()，也会应用所有bean的派驻（后置）处理器（包括那些可能用给定的原始bean包裹的bean），嗨呀好难啊。

注意：bean工厂中没有必须存在指定名称的bean defintion，传进来的bean名称将会被仅用于回调，但是不会与已经注册的bean definitions进行检查。

```java
/**
 * Apply {@link BeanPostProcessor BeanPostProcessors} to the given existing bean
 * instance, invoking their {@code postProcessBeforeInitialization} methods.
 * The returned bean instance may be a wrapper around the original.
 * @param existingBean the existing bean instance
 * @param beanName the name of the bean, to be passed to it if necessary
 * (only passed to {@link BeanPostProcessor BeanPostProcessors};
 * can follow the {@link #ORIGINAL_INSTANCE_SUFFIX} convention in order to
 * enforce the given instance to be returned, i.e. no proxies etc)
 * @return the bean instance to use, either the original or a wrapped one
 * @throws BeansException if any post-processing failed
 * @see BeanPostProcessor#postProcessBeforeInitialization
 * @see #ORIGINAL_INSTANCE_SUFFIX
 */
Object applyBeanPostProcessorsBeforeInitialization(Object existingBean, String beanName)
      throws BeansException;
```

方法介绍：为给定的存在的bean实例应用BeanPostProcessors，调用他们的postProcessBeforeInitialization()方法。这个返回的bean实例可能是这个原始bean的一个包装器。（warpper可能多是指代理吧，代理的实现很像是warp包裹住了一个bean）。

```java
/**
 * Apply {@link BeanPostProcessor BeanPostProcessors} to the given existing bean
 * instance, invoking their {@code postProcessAfterInitialization} methods.
 * The returned bean instance may be a wrapper around the original.
 * @param existingBean the existing bean instance
 * @param beanName the name of the bean, to be passed to it if necessary
 * (only passed to {@link BeanPostProcessor BeanPostProcessors};
 * can follow the {@link #ORIGINAL_INSTANCE_SUFFIX} convention in order to
 * enforce the given instance to be returned, i.e. no proxies etc)
 * @return the bean instance to use, either the original or a wrapped one
 * @throws BeansException if any post-processing failed
 * @see BeanPostProcessor#postProcessAfterInitialization
 * @see #ORIGINAL_INSTANCE_SUFFIX
 */
Object applyBeanPostProcessorsAfterInitialization(Object existingBean, String beanName)
      throws BeansException;
```

方法介绍：为给定的存在的bean实例应用BeanPostProcessors,调用他们的postProcessAfterInitialization()方法。返回的bean实例可能是原始bean的一个包装器。

```java
/**
 * Destroy the given bean instance (typically coming from {@link #createBean}),
 * applying the {@link org.springframework.beans.factory.DisposableBean} contract as well as
 * registered {@link DestructionAwareBeanPostProcessor DestructionAwareBeanPostProcessors}.
 * <p>Any exception that arises during destruction should be caught
 * and logged instead of propagated to the caller of this method.
 * @param existingBean the bean instance to destroy
 */
void destroyBean(Object existingBean);
```

方法介绍：销毁给定的bean实例（这个bean实例，一般都来自于createBean()），履行DisposabelBean契约，也会同时应用注册的DestructionAwareBeanPostProcessors。

```java
//-------------------------------------------------------------------------
// Delegate methods for resolving injection points
//-------------------------------------------------------------------------
```

注释翻译：解析注入点的委托方法

```java
/**
 * Resolve the bean instance that uniquely matches the given object type, if any,
 * including its bean name.
 * <p>This is effectively a variant of {@link #getBean(Class)} which preserves the
 * bean name of the matching instance.
 * @param requiredType type the bean must match; can be an interface or superclass
 * @return the bean name plus bean instance
 * @throws NoSuchBeanDefinitionException if no matching bean was found
 * @throws NoUniqueBeanDefinitionException if more than one matching bean was found
 * @throws BeansException if the bean could not be created
 * @since 4.3.3
 * @see #getBean(Class)
 */
<T> NamedBeanHolder<T> resolveNamedBean(Class<T> requiredType) throws BeansException;
effectively：有效地;实际上;事实上
```

方法介绍：根据给定对象类型唯一匹配的bean实例，如果存在的话，包括他的bean名称。

这实际上是getBean(Class)方法的一个变体，这个方法将会保留匹配实例的bean名称。

```java
/**
 * Resolve a bean instance for the given bean name, providing a dependency descriptor
 * for exposure to target factory methods.
 * <p>This is effectively a variant of {@link #getBean(String, Class)} which supports
 * factory methods with an {@link org.springframework.beans.factory.InjectionPoint}
 * argument.
 * @param name the name of the bean to look up
 * @param descriptor the dependency descriptor for the requesting injection point
 * @return the corresponding bean instance
 * @throws NoSuchBeanDefinitionException if there is no bean with the specified name
 * @throws BeansException if the bean could not be created
 * @since 5.1.5
 * @see #getBean(String, Class)
 */
Object resolveBeanByName(String name, DependencyDescriptor descriptor) throws BeansException;
exposure：面临；遭受；揭露；亮相，被报道；
```

方法介绍：通过给定的bean名称解析一个bean的实例，为目标工厂方法的暴露提供一个依赖描述符。

这是实际上是一个getBean(String,Class)方法的一个变种，支持带有InjectionPoint参数的工厂方法。

```java
/**
 * Resolve the specified dependency against the beans defined in this factory.
 * @param descriptor the descriptor for the dependency (field/method/constructor)
 依赖的描述符是（field/method/constructor）
 * @param requestingBeanName the name of the bean which declares the given dependency
 * @return the resolved object, or {@code null} if none found
 * @throws NoSuchBeanDefinitionException if no matching bean was found
 * @throws NoUniqueBeanDefinitionException if more than one matching bean was found
 * @throws BeansException if dependency resolution failed for any other reason
 * @since 2.5
 * @see #resolveDependency(DependencyDescriptor, String, Set, TypeConverter)
 */
@Nullable
Object resolveDependency(DependencyDescriptor descriptor, @Nullable String requestingBeanName) throws BeansException;
```

方法介绍：解析定义在当前工厂中的bean与之对应的指定的依赖关系

```java
/**
 * Resolve the specified dependency against the beans defined in this factory.
 * @param descriptor the descriptor for the dependency (field/method/constructor)
 * @param requestingBeanName the name of the bean which declares the given dependency
 * @param autowiredBeanNames a Set that all names of autowired beans (used for
 * resolving the given dependency) are supposed to be added to
 一个Set集合，所有需要自动装配的beande名称都在里面
 * @param typeConverter the TypeConverter to use for populating arrays and collections
 类型转换器，被用来填充数组和集合
 * @return the resolved object, or {@code null} if none found
 * @throws NoSuchBeanDefinitionException if no matching bean was found
 * @throws NoUniqueBeanDefinitionException if more than one matching bean was found
 * @throws BeansException if dependency resolution failed for any other reason
 * @since 2.5
 * @see DependencyDescriptor
 */
@Nullable
Object resolveDependency(DependencyDescriptor descriptor, @Nullable String requestingBeanName,
      @Nullable Set<String> autowiredBeanNames, @Nullable TypeConverter typeConverter) throws BeansException;
```

方法介绍：解析定义在当前工厂中的bean与之对应的指定的依赖关系