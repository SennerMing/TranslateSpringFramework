#### ResourceLoader

```java
/**
 * Strategy interface for loading resources (e.. class path or file system
 * resources). An {@link org.springframework.context.ApplicationContext}
 * is required to provide this functionality, plus extended
 * {@link org.springframework.core.io.support.ResourcePatternResolver} support.
 *
 * <p>{@link DefaultResourceLoader} is a standalone implementation that is
 * usable outside an ApplicationContext, also used by {@link ResourceEditor}.
 *
 * <p>Bean properties of type Resource and Resource array can be populated
 * from Strings when running in an ApplicationContext, using the particular
 * context's resource loading strategy.
 *
 * @author Juergen Hoeller
 * @since 10.03.2004
 * @see Resource
 * @see org.springframework.core.io.support.ResourcePatternResolver
 * @see org.springframework.context.ApplicationContext
 * @see org.springframework.context.ResourceLoaderAware
 */
```

类介绍：用以加载资源（呃...类路径或者文件系统资源）的策略接口。ApplicationContext需要去提供这个功能，加上扩展ResourcePatternResolover(资源模式匹配解析器)支持。

DefaultResourceLoader是一个独立的实现可以在ApplicationContext之外单独使用，也可以被ResourceEditor使用。

当运行在ApplicationContext中时，可以用Strings来填充类型Resource(资源)和Resource数组的Bean属性，使用

特定上下文的资源加载策略。

```java
/** Pseudo URL prefix for loading from the class path: "classpath:". */
String CLASSPATH_URL_PREFIX = ResourceUtils.CLASSPATH_URL_PREFIX;
Pseudo：假的；伪语言；伪码
```

属性介绍：从类路径"classpath："加载的伪URL前缀。

```java
/**
 * Return a Resource handle for the specified resource location.
 * <p>The handle should always be a reusable resource descriptor,
 * allowing for multiple {@link Resource#getInputStream()} calls.
 * <p><ul>
 * <li>Must support fully qualified URLs, e.g. "file:C:/test.dat".
 * <li>Must support classpath pseudo-URLs, e.g. "classpath:test.dat".
 * <li>Should support relative file paths, e.g. "WEB-INF/test.dat".
 * (This will be implementation-specific, typically provided by an
 * ApplicationContext implementation.)
 * </ul>
 * <p>Note that a Resource handle does not imply an existing resource;
 * you need to invoke {@link Resource#exists} to check for existence.
 * @param location the resource location
 * @return a corresponding Resource handle (never {@code null})
 * @see #CLASSPATH_URL_PREFIX
 * @see Resource#exists()
 * @see Resource#getInputStream()
 */
Resource getResource(String location);
reusable：重复使用的；可再次使用的（我还以为啥高大上的词汇呢）
provided by：由...规定
imply：含有…的意思；暗示；暗指；说明
existing：现存的；现有的；存在；实际上有
existence：存在（态）；实体
```

方法介绍：通过给定的资源路径，返回一个指向其的资源句柄。

这个句柄应该永远是一个可以重复使用的资源描述符，允许多次 Resource的getInputStream()方法调用。

必须支持全限定URLs，例如："file:C:/test.dat"

必须支持类路径伪URLs，例如："classpath:test.dat"

应该支持相对文件路径，例如："WEB-INF/test.dat"

(这个方法将会被明确实现，通常被ApplicationContext实现提供)

注意Resource句柄不意味着一个现有资源，你需要调用Resource的exists()方法来检查存在。

```java
/**
 * Expose the ClassLoader used by this ResourceLoader.
 * <p>Clients which need to access the ClassLoader directly can do so
 * in a uniform manner with the ResourceLoader, rather than relying
 * on the thread context ClassLoader.
 * @return the ClassLoader
 * (only {@code null} if even the system ClassLoader isn't accessible)
 //只有当即使系统ClassLoader都不可用才返回null
 * @see org.springframework.util.ClassUtils#getDefaultClassLoader()
 * @see org.springframework.util.ClassUtils#forName(String, ClassLoader)
 */
@Nullable
ClassLoader getClassLoader();
```

方法介绍：将当前ResourceLoader中使用的ClassLoader暴露出去。

需要直接获得ClassLoader的客户端可以可以这样做，通过使用ResourceLoader的一个统一的方式，而不是依赖线程上下文ClassLoader