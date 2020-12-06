#### AbstractXmlApplicationContext

```java
/**
 * Convenient base class for {@link org.springframework.context.ApplicationContext}
 * implementations, drawing configuration from XML documents containing bean definitions
 * understood by an {@link org.springframework.beans.factory.xml.XmlBeanDefinitionReader}.
 *
 * <p>Subclasses just have to implement the {@link #getConfigResources} and/or
 * the {@link #getConfigLocations} method. Furthermore, they might override
 * the {@link #getResourceByPath} hook to interpret relative paths in an
 * environment-specific fashion, and/or {@link #getResourcePatternResolver}
 * for extended pattern resolution.
 *
 * @author Rod Johnson
 * @author Juergen Hoeller
 * @see #getConfigResources
 * @see #getConfigLocations
 * @see org.springframework.beans.factory.xml.XmlBeanDefinitionReader
 */
furthermore：再者；此外；而且；与此同时
interpret：理解；说明；诠释；领会；把...理解为
```

类介绍：为ApplicationContext的实现类提供方便的基类，从被XmlBeanDeifinitonReader理解的包含bean definitions的XML文档中绘制配置。

子类只要实现getConfigResources()和/或者getConfigLocations()方法。再者这些实现类可能会重写getResourceByPath()钩子，以在特定环境的方式，对相对路径进行解释，并且/或者为了扩展模式解析，重写getResourcePatternResolver。

```java
/**
 * Create a new AbstractXmlApplicationContext with no parent.
 */
public AbstractXmlApplicationContext() {
}
```

方法介绍：创建一个新的没有父ApplicationContext的AbstractXmlApplicationContext

```java
/**
 * Create a new AbstractXmlApplicationContext with the given parent context.
 * @param parent the parent context
 */
public AbstractXmlApplicationContext(@Nullable ApplicationContext parent) {
   super(parent);
}
```

方法介绍：创建一个新的指定父ApplicationContext的AbstractXmlApplicationContext

```java
/**
 * Set whether to use XML validation. Default is {@code true}.
 */
public void setValidating(boolean validating) {
   this.validating = validating;
}
```

方法介绍：设置是否启用XML检验，模式是启动的

```java
/**
 * Loads the bean definitions via an XmlBeanDefinitionReader.
 * @see org.springframework.beans.factory.xml.XmlBeanDefinitionReader
 * @see #initBeanDefinitionReader
 * @see #loadBeanDefinitions
 */
@Override
protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {
   // Create a new XmlBeanDefinitionReader for the given BeanFactory.
   //为给定的BeanFactory创建一个新的XmlBeanDefinitionReader
   XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);

   // Configure the bean definition reader with this context's
   // resource loading environment.
   //通过当前上下文的资源加载环境配置bean definitions阅读器
   beanDefinitionReader.setEnvironment(this.getEnvironment());
   beanDefinitionReader.setResourceLoader(this);
   beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));

   // Allow a subclass to provide custom initialization of the reader,
   // then proceed with actually loading the bean definitions.
   // 允许子类提供阅读器自定义的初始化
   // 然后继续实际加载bean definitions
   initBeanDefinitionReader(beanDefinitionReader);
   loadBeanDefinitions(beanDefinitionReader);
}
proceed with：继续做
```

方法介绍：通过XmlBeanDefinitionReader加载bean definitions

```java
/**
 * Initialize the bean definition reader used for loading the bean
 * definitions of this context. Default implementation is empty.
 * <p>Can be overridden in subclasses, e.g. for turning off XML validation
 * or using a different XmlBeanDefinitionParser implementation.
 * @param reader the bean definition reader used by this context
 当前上下文使用的bean definition阅读器
 * @see org.springframework.beans.factory.xml.XmlBeanDefinitionReader#setDocumentReaderClass
 */
protected void initBeanDefinitionReader(XmlBeanDefinitionReader reader) {
   reader.setValidating(this.validating);
}
```

方法介绍：初始化用来加载当前上下文bean definition的阅读器。默认实现是空的。

子类可以从写该方法，例如：用来关闭XML检验或者使用一个不同的XmlBeanDefinitionParser的实现。

```java
/**
 * Load the bean definitions with the given XmlBeanDefinitionReader.
 * <p>The lifecycle of the bean factory is handled by the {@link #refreshBeanFactory}
 * method; hence this method is just supposed to load and/or register bean definitions.
 * @param reader the XmlBeanDefinitionReader to use
 * @throws BeansException in case of bean registration errors
 * @throws IOException if the required XML document isn't found
 * @see #refreshBeanFactory
 * @see #getConfigLocations
 * @see #getResources
 * @see #getResourcePatternResolver
 */
protected void loadBeanDefinitions(XmlBeanDefinitionReader reader) throws BeansException, IOException {
   Resource[] configResources = getConfigResources();
   if (configResources != null) {
      reader.loadBeanDefinitions(configResources);
   }
   String[] configLocations = getConfigLocations();
   if (configLocations != null) {
      reader.loadBeanDefinitions(configLocations);
   }
}
supposed to：应该
```

方法介绍：通过给定的XmlBeanDefinitionReader加载bean definitions。

bean factory的生命周期会被refreshBeanFactory()函数进行处理；因此这个方法只是去加载 并且/或者 注册bean definitions。

```java
/**
 * Return an array of Resource objects, referring to the XML bean definition
 * files that this context should be built with.
 * <p>The default implementation returns {@code null}. Subclasses can override
 * this to provide pre-built Resource objects rather than location Strings.
 * @return an array of Resource objects, or {@code null} if none
 * @see #getConfigLocations()
 */
@Nullable
protected Resource[] getConfigResources() {
   return null;
}
```

方法介绍：返回一个资源对象的数组，这个数组引用了用于构建当前上下问的Xml bean definition文件。

这个默认的实现返回null。子类可以重写这个方法用来提供预先构建的资源对象而不是位置字符串。