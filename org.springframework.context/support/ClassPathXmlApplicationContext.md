#### ClassPathXmlApplicationContext

```java
/**
 * Standalone XML application context, taking the context definition files
 * from the class path, interpreting plain paths as class path resource names
 * that include the package path (e.g. "mypackage/myresource.txt"). Useful for
 * test harnesses as well as for application contexts embedded within JARs.
 *
 * <p>The config location defaults can be overridden via {@link #getConfigLocations},
 * Config locations can either denote concrete files like "/myfiles/context.xml"
 * or Ant-style patterns like "/myfiles/*-context.xml" (see the
 * {@link org.springframework.util.AntPathMatcher} javadoc for pattern details).
 *
 * <p>Note: In case of multiple config locations, later bean definitions will
 * override ones defined in earlier loaded files. This can be leveraged to
 * deliberately override certain bean definitions via an extra XML file.
 *
 * <p><b>This is a simple, one-stop shop convenience ApplicationContext.
 * Consider using the {@link GenericApplicationContext} class in combination
 * with an {@link org.springframework.beans.factory.xml.XmlBeanDefinitionReader}
 * for more flexible context setup.</b>
 *
 * @author Rod Johnson
 * @author Juergen Hoeller
 * @see #getResource
 * @see #getResourceByPath
 * @see GenericApplicationContext
 */
interpreting：诠释，说明；把...理解为
harnesses：控制；马具；保护带。这里翻译为工具
denote：标志；预示；象征；
leveraged：杠杆作用；融资
deliberately：故意；蓄意；存心；不慌不忙地；小心翼翼地；从容不迫地
```

类介绍：一个独立的XML应用上下文，从类路径下获取上下文定义文件，将普通路径解释为类路径资源名，包括包路径（例如："mypackage/myresource.txt"）。对测试工具以及内嵌在JARS中的应用上下文都管用。

这个默认配置的路径（地址）可以通过getConfigLocations()覆盖，配置的位置可以标志具体的文件像是"/myfiles/context.xml"或是Ant-Style(Ant风格)模式像是"/myfiles/*-context.xml"(想要获得这个模式的详细信息，可以参考AntPathMatcher)的Java文档

注意：在有多个配置地址的情况下，后面的bean definitions将会覆盖定义在先前加载的文件。这个可以是故意的覆盖特定的bean definitions通过额外的XML文件。

这是一个简单地一站式便利的应用上下文。为了更加灵活的上下文设置，可以考虑使用GenericApplicationContext类与XmlBeanDefinitionReader捆绑使用。

```java
/**
	 * Create a new ClassPathXmlApplicationContext for bean-style configuration.
	 * @see #setConfigLocation
	 * @see #setConfigLocations
	 * @see #afterPropertiesSet()
	 */
	public ClassPathXmlApplicationContext() {
	}
```

方法介绍：为bean风格的配置，创建一个新的ClassPathXmlApplicationContext。

```java
/**
 * Create a new ClassPathXmlApplicationContext for bean-style configuration.
 * @param parent the parent context
 * @see #setConfigLocation
 * @see #setConfigLocations
 * @see #afterPropertiesSet()
 */
public ClassPathXmlApplicationContext(ApplicationContext parent) {
   super(parent);
}
```

方法介绍：与上面类似，多加了一个设置父ApplicationContext

```java
/**
 * Create a new ClassPathXmlApplicationContext, loading the definitions
 * from the given XML file and automatically refreshing the context.
 * @param configLocation resource location
 * @throws BeansException if context creation failed
 */
public ClassPathXmlApplicationContext(String configLocation) throws BeansException {
   this(new String[] {configLocation}, true, null);
}
```

方法介绍：创建一个新的ClassPathXmlApplicationContext，通过给定的XML文件加载bean定义，并且自动刷新上下文。

```java
/**
 * Create a new ClassPathXmlApplicationContext, loading the definitions
 * from the given XML files and automatically refreshing the context.
 * @param configLocations array of resource locations
 * @throws BeansException if context creation failed
 */
public ClassPathXmlApplicationContext(String... configLocations) throws BeansException {
   this(configLocations, true, null);
}
```

方法介绍：与上面方法类似，可以是多个配置文件地址。

```java
/**
 * Create a new ClassPathXmlApplicationContext with the given parent,
 * loading the definitions from the given XML files and automatically
 * refreshing the context.
 * @param configLocations array of resource locations
 * @param parent the parent context
 * @throws BeansException if context creation failed
 */
public ClassPathXmlApplicationContext(String[] configLocations, @Nullable ApplicationContext parent)
      throws BeansException {

   this(configLocations, true, parent);
}
```

方法介绍：通过给定的配置文件地址和父ApplicationContext创建一个新的ClassPathXmlApplicationContext，从给定的XML文件中加载bean definitions并且自动的刷新容器。

```java
/**
 * Create a new ClassPathXmlApplicationContext, loading the definitions
 * from the given XML files.
 * @param configLocations array of resource locations
 * @param refresh whether to automatically refresh the context,
 * loading all bean definitions and creating all singletons.
 加载所有的bean定义并且创建所有的单例
 * Alternatively, call refresh manually after further configuring the context.
 要不或者，在更进一步的上下文配置中手动调用刷新
 * @throws BeansException if context creation failed
 * @see #refresh()
 */
public ClassPathXmlApplicationContext(String[] configLocations, boolean refresh) throws BeansException {
   this(configLocations, refresh, null);
}
```

方法介绍：创建一个新的ClassPathXmlApplicationContext，通过给定的XML文件加载bean定义

```java
/**
 * Create a new ClassPathXmlApplicationContext with the given parent,
 * loading the definitions from the given XML files.
 * @param configLocations array of resource locations
 * @param refresh whether to automatically refresh the context,
 * loading all bean definitions and creating all singletons.
 * Alternatively, call refresh manually after further configuring the context.
 这两句的翻译和上面的方法一样。
 * @param parent the parent context
 * @throws BeansException if context creation failed
 * @see #refresh()
 */
public ClassPathXmlApplicationContext(
      String[] configLocations, boolean refresh, @Nullable ApplicationContext parent)
      throws BeansException {

   super(parent);
   setConfigLocations(configLocations);
   if (refresh) {
      refresh();
   }
}
```

方法介绍：通过给定的父ApplicationContext，创建一个新的ClassPathXmlApplicationContext加载给定的XML文件

```java
/**
 * Create a new ClassPathXmlApplicationContext, loading the definitions
 * from the given XML file and automatically refreshing the context.
 * <p>This is a convenience method to load class path resources relative to a
 * given Class. For full flexibility, consider using a GenericApplicationContext
 * with an XmlBeanDefinitionReader and a ClassPathResource argument.
 * @param path relative (or absolute) path within the class path
 * @param clazz the class to load resources with (basis for the given paths)
 * @throws BeansException if context creation failed
 * @see org.springframework.core.io.ClassPathResource#ClassPathResource(String, Class)
 * @see org.springframework.context.support.GenericApplicationContext
 * @see org.springframework.beans.factory.xml.XmlBeanDefinitionReader
 */
public ClassPathXmlApplicationContext(String path, Class<?> clazz) throws BeansException {
   this(new String[] {path}, clazz);
}
```

方法介绍：通过给定的父ApplicationContext，创建一个新的ClassPathXmlApplicationContext加载给定的XML文件并且会自动刷新上下文。

这是一个相对于给定类加载类路径资源的方便的方法，为了获得充分的灵活性，考虑使用带有XmlBeanDefinitionReader和ClassPathResource的GenericApplicationContext。

```java
/**
 * Create a new ClassPathXmlApplicationContext, loading the definitions
 * from the given XML files and automatically refreshing the context.
 * @param paths array of relative (or absolute) paths within the class path
 * @param clazz the class to load resources with (basis for the given paths)
 * @throws BeansException if context creation failed
 * @see org.springframework.core.io.ClassPathResource#ClassPathResource(String, Class)
 * @see org.springframework.context.support.GenericApplicationContext
 * @see org.springframework.beans.factory.xml.XmlBeanDefinitionReader
 */
public ClassPathXmlApplicationContext(String[] paths, Class<?> clazz) throws BeansException {
   this(paths, clazz, null);
}
```

方法介绍：创建一个新的ClassPathXmlApplicationContext，通过给定的XML文件加载bean definitions并且自动刷新应用上下文。

```java
/**
 * Create a new ClassPathXmlApplicationContext with the given parent,
 * loading the definitions from the given XML files and automatically
 * refreshing the context.
 * @param paths array of relative (or absolute) paths within the class path
 * @param clazz the class to load resources with (basis for the given paths)
 * @param parent the parent context
 * @throws BeansException if context creation failed
 * @see org.springframework.core.io.ClassPathResource#ClassPathResource(String, Class)
 * @see org.springframework.context.support.GenericApplicationContext
 * @see org.springframework.beans.factory.xml.XmlBeanDefinitionReader
 */
basis：基础，要点
public ClassPathXmlApplicationContext(String[] paths, Class<?> clazz, @Nullable ApplicationContext parent)
      throws BeansException {

   super(parent);
   Assert.notNull(paths, "Path array must not be null");
   Assert.notNull(clazz, "Class argument must not be null");
   this.configResources = new Resource[paths.length];
   for (int i = 0; i < paths.length; i++) {
      this.configResources[i] = new ClassPathResource(paths[i], clazz);
   }
   refresh();
}
```

方法介绍：通过给定父ApplicationContext创建一个新的ClassPathXmlApplicationContext，通过给定的XML文件加载bean definitions并且自动刷新应用上下文。