#### AbstractRefreshableConfigApplicationContext

```java
/**
 * {@link AbstractRefreshableApplicationContext} subclass that adds common handling
 * of specified config locations. Serves as base class for XML-based application
 * context implementations such as {@link ClassPathXmlApplicationContext} and
 * {@link FileSystemXmlApplicationContext}, as well as
 * {@link org.springframework.web.context.support.XmlWebApplicationContext}.
 *
 * @author Juergen Hoeller
 * @since 2.5.2
 * @see #setConfigLocation
 * @see #setConfigLocations
 * @see #getDefaultConfigLocations
 */
Serves as：充当；提供；服务；接待
```

类介绍：是AbstractRefreshableApplicationContext的子类，相较于父类添加了对指定配置路径的通用处理。

充当基于XML的应用上下文实现类的基类，像是ClassPathXmlApplicationContext和FileSystemXmlApplicationContext，同样还有XmlWebApplicationContext。

```java
/**
 * Create a new AbstractRefreshableConfigApplicationContext with no parent.
 */
public AbstractRefreshableConfigApplicationContext() {
}
```

方法介绍：创建一个新的没有指定父ApplicationContext的AbstractRefreshableConfigApplicationContext

```java
/**
 * Create a new AbstractRefreshableConfigApplicationContext with the given parent context.
 * @param parent the parent context
 */
public AbstractRefreshableConfigApplicationContext(@Nullable ApplicationContext parent) {
   super(parent);
}
```

方法介绍：创建一个新的指定了父上下问的AbstractRefreshableConfigApplicationContext

```java
/**
 * Set the config locations for this application context in init-param style,
 * i.e. with distinct locations separated by commas, semicolons or whitespace.
 * <p>If not set, the implementation may use a default as appropriate.
 */
public void setConfigLocation(String location) {
   setConfigLocations(StringUtils.tokenizeToStringArray(location, CONFIG_LOCATION_DELIMITERS));
}
i.e. ：即
distinct：清晰的；清楚的；明白的；明显的；截然不同的；有区别的；不同种类的；确实的；确切的
commas：逗号
semicolons：分号
whitespace：空格
as appropriate:视情况而定
```

方法介绍：为当前应用上下文以初始化参数的方式，设置配置文件路径，即以逗号、分号或者空格分隔的不同路径，如果没有设置的话，那么实现类可视情况而定使用默认值。

```java
/**
 * Set the config locations for this application context.
 * <p>If not set, the implementation may use a default as appropriate.
 */
public void setConfigLocations(@Nullable String... locations) {
   if (locations != null) {
      Assert.noNullElements(locations, "Config locations must not be null");
      this.configLocations = new String[locations.length];
      for (int i = 0; i < locations.length; i++) {
         this.configLocations[i] = resolvePath(locations[i]).trim();
      }
   }
   else {
      this.configLocations = null;
   }
```

方法介绍：为当前应用上下文设置配置文件路径。如果没有设置，那么实现类可视情况而定使用默认值

```java
/**
 * Return an array of resource locations, referring to the XML bean definition
 * files that this context should be built with. Can also include location
 * patterns, which will get resolved via a ResourcePatternResolver.
 * <p>The default implementation returns {@code null}. Subclasses can override
 * this to provide a set of resource locations to load bean definitions from.
 * @return an array of resource locations, or {@code null} if none
 * @see #getResources
 * @see #getResourcePatternResolver
 */
@Nullable
protected String[] getConfigLocations() {
   return (this.configLocations != null ? this.configLocations : getDefaultConfigLocations());
}
```

方法介绍：返回一个资源路径的数组，这个数组包含了对构建当前上下文的XML bean definition文件的引用。也可以包含路径模式，location patterns将会被ResourcePatternResolver解析。默认的实现返回null，子类可以重写这个方法，用来提供一组资源路径用来加载bean definitions。

```java
/**
 * Return the default config locations to use, for the case where no
 * explicit config locations have been specified.
 * <p>The default implementation returns {@code null},
 * requiring explicit config locations.
 * @return an array of default config locations, if any
 * @see #setConfigLocations
 */
@Nullable
protected String[] getDefaultConfigLocations() {
   return null;
}
explicit：清除明白的；易于理解的；清晰的，明确的；直言的
```

方法介绍：返回默认配置路径来使用，在还没有地方显示指定配置路径的情况下。默认的实现是返回一个null。

```java
/**
 * Resolve the given path, replacing placeholders with corresponding
 * environment property values if necessary. Applied to config locations.
 * @param path the original file path
 * @return the resolved file path
 * @see org.springframework.core.env.Environment#resolveRequiredPlaceholders(String)
 */
protected String resolvePath(String path) {
   return getEnvironment().resolveRequiredPlaceholders(path);
}
replace...width:用...代替；
```

方法介绍：解析给定的路径，必要情况下会用对应环境属性值替代占位符。应用与配置路径

```java
/**
 * Sets the id of this context to the bean name by default,
 * for cases where the context instance is itself defined as a bean.
 */
@Override
public void setBeanName(String name) {
   if (!this.setIdCalled) {
      super.setId(name);
      setDisplayName("ApplicationContext '" + name + "'");
   }
}
```

方法介绍：默认情况下，如果上下文实例本身被定义为一个bean，则将此上下文的id设置为bean的名称。

```java
/**
 * Triggers {@link #refresh()} if not refreshed in the concrete context's
 * constructor already.
 */
@Override
public void afterPropertiesSet() {
   if (!isActive()) {
      refresh();
   }
}
```

方法介绍：如果在具体上下文的构造器中还没有刷新，则触发refresh()



