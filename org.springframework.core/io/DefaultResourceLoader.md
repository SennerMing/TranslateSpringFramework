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
introspection：内省！
```

方法介绍：返回当前注册的协议解析器集合，允许内省和修改。

```java
/**
 * Obtain a cache for the given value type, keyed by {@link Resource}.
 * @param valueType the value type, e.g. an ASM {@code MetadataReader}
 值类型，例如ASM MetadataReader
 * @return the cache {@link Map}, shared at the {@code ResourceLoader} level
 返回缓存Map，在ResourceLoader层级共享
 * @since 5.0
 */
@SuppressWarnings("unchecked")
public <T> Map<Resource, T> getResourceCache(Class<T> valueType) {
   return (Map<Resource, T>) this.resourceCaches.computeIfAbsent(valueType, key -> new ConcurrentHashMap<>());
}
```

方法介绍：获得一个给定值类型的缓存，以Resource为键。

```java
/**
 * Clear all resource caches in this resource loader.
 * @since 5.0
 * @see #getResourceCache
 */
public void clearResourceCaches() {
   this.resourceCaches.clear();
}
```

方法介绍：清除所有在当这个资源加载器中的资源缓存

```java
@Override
public Resource getResource(String location) {
   Assert.notNull(location, "Location must not be null");

   for (ProtocolResolver protocolResolver : getProtocolResolvers()) {
      Resource resource = protocolResolver.resolve(location, this);
      if (resource != null) {
         return resource;
      }
   }

   if (location.startsWith("/")) {
      return getResourceByPath(location);
   }
   else if (location.startsWith(CLASSPATH_URL_PREFIX)) {
      return new ClassPathResource(location.substring(CLASSPATH_URL_PREFIX.length()), getClassLoader());
   }
   else {
      try {
         // Try to parse the location as a URL...
        //试图将路径当做URL来翻译
         URL url = new URL(location);
         return (ResourceUtils.isFileURL(url) ? new FileUrlResource(url) : new UrlResource(url));
      }
      catch (MalformedURLException ex) {
         // No URL -> resolve as resource path.
        //没有URL-->当做资源路径进行解析
         return getResourceByPath(location);
      }
   }
}
```

```java
/**
 * Return a Resource handle for the resource at the given path.
 * <p>The default implementation supports class path locations. This should
 * be appropriate for standalone implementations but can be overridden,
 * e.g. for implementations targeted at a Servlet container.
 * @param path the path to the resource
 * @return the corresponding Resource handle
 * @see ClassPathResource
 * @see org.springframework.context.support.FileSystemXmlApplicationContext#getResourceByPath
 * @see org.springframework.web.context.support.XmlWebApplicationContext#getResourceByPath
 */
protected Resource getResourceByPath(String path) {
   return new ClassPathContextResource(path, getClassLoader());
}
```

方法介绍：返回一个通过给定路径访问资源的资源句柄。这个默认的实现支持类路径地址。这个应该适用于独立的实现但是可以被重写，例如针对Servlet容器实现。

























