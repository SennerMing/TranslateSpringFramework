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

```java
/**
 * Scope identifier for the standard singleton scope: {@value}.
 * <p>Custom scopes can be added via {@code registerScope}.
 自定义的作用域可以通过registerScope()方法添加
 * @see #registerScope
 */
String SCOPE_SINGLETON = "singleton";
```

属性介绍：标准单例作用域的作用域标识符

```java
/**
 * Scope identifier for the standard prototype scope: {@value}.
 * <p>Custom scopes can be added via {@code registerScope}.
自定义的作用域可以通过registerScope()方法添加
 * @see #registerScope
 */
String SCOPE_PROTOTYPE = "prototype";
```

属性介绍：标准原型作用域的作用域标识符

```java
/**
 * Set the parent of this bean factory.
 * <p>Note that the parent cannot be changed: It should only be set outside
 * a constructor if it isn't available at the time of factory instantiation.
 * @param parentBeanFactory the parent BeanFactory
 * @throws IllegalStateException if this factory is already associated with
 * a parent BeanFactory
 * @see #getParentBeanFactory()
 */
void setParentBeanFactory(BeanFactory parentBeanFactory) throws IllegalStateException;
```

方法介绍：设置当前bean工厂的爸爸

注意这个父类是不可改变的：如果他在工厂初始化的时间不可获得，那么这个父类只应该在构造器之外设置。

```java
/**
 * Set the class loader to use for loading bean classes.
 * Default is the thread context class loader.
 * <p>Note that this class loader will only apply to bean definitions
 * that do not carry a resolved bean class yet. This is the case as of
 * Spring 2.0 by default: Bean definitions only carry bean class names,
 * to be resolved once the factory processes the bean definition.
 * @param beanClassLoader the class loader to use,
 * or {@code null} to suggest the default class loader
 */
void setBeanClassLoader(@Nullable ClassLoader beanClassLoader);
this is the case:确有其事
as of:在...时
```

方法介绍：设置类加载器用于加载bean的classes

默认是当前线程上下文的类加载器

注意这个类加载器将会之应用在还没有携带一个解析完毕的bean class的bean definitions。在Spring2.0中默认的情况下：bean definitions只会携带bean的class名称，一旦工厂处理这些bean definitions时立即会被解析。

```java
/**
 * Return this factory's class loader for loading bean classes
 * (only {@code null} if even the system ClassLoader isn't accessible).
 * @see org.springframework.util.ClassUtils#forName(String, ClassLoader)
 */
@Nullable
ClassLoader getBeanClassLoader();
```

方法介绍：返回工厂类加载器用于加载bean classes，如果连系统类加载器都不可获得，那么就会返回Null

```java
/**
 * Specify a temporary ClassLoader to use for type matching purposes.
 * Default is none, simply using the standard bean ClassLoader.
 * <p>A temporary ClassLoader is usually just specified if
 * <i>load-time weaving</i> is involved, to make sure that actual bean
 * classes are loaded as lazily as possible. The temporary loader is
 * then removed once the BeanFactory completes its bootstrap phase.
 * @since 2.5
 */
void setTempClassLoader(@Nullable ClassLoader tempClassLoader);
```

方法介绍：制定一个临时的类加载器用于类型匹配，默认是没有的，直接使用标准bean类加载器。

通常仅指定一个临时类加载器，当涉及到加载期织入，用以确保实际bean的classes尽可能延迟的加载。一旦BeanFactory完成了他的启动阶段，临时加载器会被立即移除。

```java
/**
 * Return the temporary ClassLoader to use for type matching purposes,
 * if any.
 * @since 2.5
 */
@Nullable
ClassLoader getTempClassLoader();
```

方法介绍：返回临时加载器用于类型匹配（如果有的话）

```java
/**
 * Set whether to cache bean metadata such as given bean definitions
 * (in merged fashion) and resolved bean classes. Default is on.
 * <p>Turn this flag off to enable hot-refreshing of bean definition objects
 * and in particular bean classes. If this flag is off, any creation of a bean
 * instance will re-query the bean class loader for newly resolved classes.
 */
void setCacheBeanMetadata(boolean cacheBeanMetadata);
```

方法介绍：设置是否缓存bean的元数据，像是给定的bean definitions（通过合并的方式）和解析完毕的bean classes。默认是开启的。

将这个标识关闭就可以激活bean definitions对象的尤其是bean classes的热刷新，任何bean实例的创建将会重新查找bean class加载器来获取最新的解析好的classes。

```java
/**
 * Return whether to cache bean metadata such as given bean definitions
 * (in merged fashion) and resolved bean classes.
 */
boolean isCacheBeanMetadata();
```

方法介绍：返回是否缓存bean元数据像是给定bean definitions（通过合并方式）和解析完毕的bean classes

```java
/**
 * Specify the resolution strategy for expressions in bean definition values.
 * <p>There is no expression support active in a BeanFactory by default.
 * An ApplicationContext will typically set a standard expression strategy
 * here, supporting "#{...}" expressions in a Unified EL compatible style.
 * @since 3.0
 */
void setBeanExpressionResolver(@Nullable BeanExpressionResolver resolver);
```

方法介绍：为bean definition的值表达式指定解析策略。

默认情况下，在beanfactory中没有活动的表达式支持。一个应用上下文将会通常会在这里设置一个标准表达式策略，支持"#{...}"这样的表达式通过一种统一的与EL表达式兼容的方式。

```java
/**
 * Return the resolution strategy for expressions in bean definition values.
 * @since 3.0
 */
@Nullable
BeanExpressionResolver getBeanExpressionResolver();
```

方法介绍：返回一个解析策略用于解析bean definition值的表达式

```java
/**
 * Specify a Spring 3.0 ConversionService to use for converting
 * property values, as an alternative to JavaBeans PropertyEditors.
 * @since 3.0
 */
void setConversionService(@Nullable ConversionService conversionService);
```

方法介绍：指定一个Spring3.0的转换服务用于转换属性值，作为JavaBean属性编辑器的替代品。

```java
/**
 * Return the associated ConversionService, if any.
 * @since 3.0
 */
@Nullable
ConversionService getConversionService();
```

方法介绍：返回一个相关的转换服务（如果存在的话）

```java
/**
 * Add a PropertyEditorRegistrar to be applied to all bean creation processes.
 * <p>Such a registrar creates new PropertyEditor instances and registers them
 * on the given registry, fresh for each bean creation attempt. This avoids
 * the need for synchronization on custom editors; hence, it is generally
 * preferable to use this method instead of {@link #registerCustomEditor}.
 * @param registrar the PropertyEditorRegistrar to register
 */
void addPropertyEditorRegistrar(PropertyEditorRegistrar registrar);
```

方法介绍：添加一个属性编辑器的注册器用于所有bean的创建过程。这样的注册器会创建新的属性编辑器实例，并在给定的注册表中注册它们，每次bean创建尝试都是新的。这就避免了在自定义编辑器上进行同步的需要；因此，通常最好使用此方法而不是{@link#registerCustomEditor}。

```java
/**
 * Register the given custom property editor for all properties of the
 * given type. To be invoked during factory configuration.
 * <p>Note that this method will register a shared custom editor instance;
 * access to that instance will be synchronized for thread-safety. It is
 * generally preferable to use {@link #addPropertyEditorRegistrar} instead
 * of this method, to avoid for the need for synchronization on custom editors.
 * @param requiredType type of the property
 * @param propertyEditorClass the {@link PropertyEditor} class to register
 */
void registerCustomEditor(Class<?> requiredType, Class<? extends PropertyEditor> propertyEditorClass);
```

方法介绍：为给定类型的所有属性注册一个给定的自定义的属性编辑器。会在工厂配置期间被调用。

注意这个方法将会注册一个共享的自定义编辑器实例；为了线程安全，访问这个实例将会被加锁。通常来说更推荐使用addPropertyEditorRegistar来替代这个方法，用以避免在自定义编辑器上进行同步。

```java
/**
 * Initialize the given PropertyEditorRegistry with the custom editors
 * that have been registered with this BeanFactory.
 * @param registry the PropertyEditorRegistry to initialize
 */
void copyRegisteredEditorsTo(PropertyEditorRegistry registry);
```

方法介绍：通过已经在当前工厂注册的自定义编辑器来初始化给定的PropertyEditorRegistry

```java
/**
 * Set a custom type converter that this BeanFactory should use for converting
 * bean property values, constructor argument values, etc.
 * <p>This will override the default PropertyEditor mechanism and hence make
 * any custom editors or custom editor registrars irrelevant.
 * @since 2.5
 * @see #addPropertyEditorRegistrar
 * @see #registerCustomEditor
 */
void setTypeConverter(TypeConverter typeConverter);
```

方法介绍：设置一个自定义类型转换器，当前工可应用来转换bean的属性值，构造器入参的值等等。

这个属性编辑器将会覆盖默认的属性编辑器机制并且因此会让任何自定义的编辑器和编辑器注册器变得无关紧要。

```java
/**
 * Obtain a type converter as used by this BeanFactory. This may be a fresh
 * instance for each call, since TypeConverters are usually <i>not</i> thread-safe.
 * <p>If the default PropertyEditor mechanism is active, the returned
 * TypeConverter will be aware of all custom editors that have been registered.
 * @since 2.5
 */
TypeConverter getTypeConverter();
```

方法介绍：获得当前bean工厂使用的类型转换器。每次调用此方法，返回的转换器可能会是一个全新的实例，由于TypeConverters通常不是线程安全的，如果默认的属性编辑器机制是激活状态的话，返回的类型转换器将会知道所有的已经注册的自定义编辑器。

```java
/**
 * Add a String resolver for embedded values such as annotation attributes.
 * @param valueResolver the String resolver to apply to embedded values
 * @since 3.0
 */
void addEmbeddedValueResolver(StringValueResolver valueResolver);
```

方法介绍：为内嵌的值添加一个String类型解析器像是一个注解属性



