#### DefaultResourceLoader

```java
/**
 * Default implementation of the {@link ResourceLoader} interface.
 * Used by {@link ResourceEditor}, and serves as base class for
 * {@link org.springframework.context.support.AbstractApplicationContext}.
 * Can also be used standalone.
 *
 * <p>Will return a {@link UrlResource} if the location value is a URL,
 * and a {@link ClassPathResource} if it is a non-URL path or a
 * "classpath:" pseudo-URL.
 *
 * @author Juergen Hoeller
 * @since 10.03.2004
 * @see FileSystemResourceLoader
 * @see org.springframework.context.support.ClassPathXmlApplicationContext
 */
```

类介绍：ResourceLoader接口的默认实现，被ResourceEditor使用，并且作为AbstractApplicationContext的基类，也可以被单独使用

将会返回一个UrlResource如果路径的值是一个URL，和一个ClassPathResource如果他是非URL路径或者一个"classpath:"的伪URL

```java
/**
	 * Create a new DefaultResourceLoader.
	 * <p>ClassLoader access will happen using the thread context class loader
	 * at the time of this ResourceLoader's initialization.
	 * @see java.lang.Thread#getContextClassLoader()
	 */
	public DefaultResourceLoader() {
		this.classLoader = ClassUtils.getDefaultClassLoader();
	}
```

方法介绍：创建一个新的DefaultResourceLoader。类加载器访问将会使用线程上下文类加载器在这个ResourceLoader的初始化期间。

```java
/**
	 * Create a new DefaultResourceLoader.
	 * @param classLoader the ClassLoader to load class path resources with, or {@code null}
	 * for using the thread context class loader at the time of actual resource access
	 */
//用以加载类路径资源的加载器或者是null用以在实际资源获取的时候使用线程上下文类加载器
	public DefaultResourceLoader(@Nullable ClassLoader classLoader) {
		this.classLoader = classLoader;
	}
```

方法介绍：创建一个新的DefaultResourceLoader。

```java
/**
	 * Specify the ClassLoader to load class path resources with, or {@code null}
	 * for using the thread context class loader at the time of actual resource access.
	 * <p>The default is that ClassLoader access will happen using the thread context
	 * class loader at the time of this ResourceLoader's initialization.
	 */
	public void setClassLoader(@Nullable ClassLoader classLoader) {
		this.classLoader = classLoader;
	}
```

方法介绍：指定ClassLoader来加载类路径资源，或者null用以在实际资源获取的时候使用线程上下文类加载器。

默认情况ClassLoader获取将会发生使用线程上下文类加载器当ResourceLoader的初始化时候

```java
/**
	 * Return the ClassLoader to load class path resources with.
	 * <p>Will get passed to ClassPathResource's constructor for all
	 * ClassPathResource objects created by this resource loader.
	 * @see ClassPathResource
	 */
	@Override
	@Nullable
	public ClassLoader getClassLoader() {
		return (this.classLoader != null ? this.classLoader : ClassUtils.getDefaultClassLoader());
	}
```

方法介绍：返回ClassLoader来加载类路径下资源。将会传递给资源加载器创建的所有ClassPathResource对象的构造函数。

```java
/**
	 * Register the given resolver with this resource loader, allowing for
	 * additional protocols to be handled.
	 * <p>Any such resolver will be invoked ahead of this loader's standard
	 * resolution rules. It may therefore also override any default rules.
	 * @since 4.3
	 * @see #getProtocolResolvers()
	 */
	public void addProtocolResolver(ProtocolResolver resolver) {
		Assert.notNull(resolver, "ProtocolResolver must not be null");
		this.protocolResolvers.add(resolver);
	}
```

方法介绍：通过这个资源加载器注册给定解析器，允许处理其他协议。

任何这样的解析器将会在当前加载器的标准解析规则之前被调用。他可能因此也会覆盖任何默认的规则

```java
/**
	 * Return the collection of currently registered protocol resolvers,
	 * allowing for introspection as well as modification.
	 * @since 4.3
	 */
	public Collection<ProtocolResolver> getProtocolResolvers() {
		return this.protocolResolvers;
	}
```

方法介绍：返回当前注册的协议解析器集合，