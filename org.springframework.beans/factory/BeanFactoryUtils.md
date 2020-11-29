#### BeanFactoryUtils

ListableBeanFactory常提到这个工具类，可以去查找所有的祖先工厂中的Beans。

```java
/**
 * Convenience methods operating on bean factories, in particular
 * on the {@link ListableBeanFactory} interface.
 *
 * <p>Returns bean counts, bean names or bean instances,
 * taking into account the nesting hierarchy of a bean factory
 * (which the methods defined on the ListableBeanFactory interface don't,
 * in contrast to the methods defined on the BeanFactory interface).
 *
 * @author Rod Johnson
 * @author Juergen Hoeller
 * @author Chris Beams
 * @since 04.07.2003
 */
take into account：考虑（想当然的理解为，将xxx纳入计数范围）
in contrast to：与xxx对比
```

类介绍：Bean工厂中操作的方便方法。特别是在ListableBeanFactory接口中。

返回bean的数量，bean的名称或者bean的实例，会将一个bean工厂所处的层级关系也纳入考虑范围（和BeanFactory中定义的方法相比，ListableBeanFactory接口中定义的方法就不会考虑层级结构）。

```java
/**
 * Separator for generated bean names. If a class name or parent name is not
 * unique, "#1", "#2" etc will be appended, until the name becomes unique.
 */
public static final String GENERATED_BEAN_NAME_SEPARATOR = "#";
```

属性介绍：已经生成bean names的分隔符，如果一个类的名称或者他的父类名称不是唯一的，"#1"，"#2"等等会被追加在名称的后面，直到这个名称成为全局唯一。

```java
/**
 * Cache from name with factory bean prefix to stripped name without dereference.
 * @since 5.1
 * @see BeanFactory#FACTORY_BEAN_PREFIX
 */
private static final Map<String, String> transformedBeanNameCache = new ConcurrentHashMap<>();
```

属性介绍：缓存从带有factory bean前缀（应该是&了）的剥离后没有简介引用的名称。

```java
/**
 * Return whether the given name is a factory dereference
 * (beginning with the factory dereference prefix).
 * @param name the name of the bean
 * @return whether the given name is a factory dereference
 * @see BeanFactory#FACTORY_BEAN_PREFIX
 */
public static boolean isFactoryDereference(@Nullable String name) {
   return (name != null && name.startsWith(BeanFactory.FACTORY_BEAN_PREFIX));
}
```

方法介绍：返回检验指定名称是否为一个工厂的间接引用（是不是以工厂间接引用前缀符号开头）。

```java
/**
 * Return the actual bean name, stripping out the factory dereference
 * prefix (if any, also stripping repeated factory prefixes if found).
 * @param name the name of the bean
 * @return the transformed name
 * @see BeanFactory#FACTORY_BEAN_PREFIX
 */
public static String transformedBeanName(String name) {
   Assert.notNull(name, "'name' must not be null");
   if (!name.startsWith(BeanFactory.FACTORY_BEAN_PREFIX)) {
      return name;
   }
   return transformedBeanNameCache.computeIfAbsent(name, beanName -> {
      do {
         beanName = beanName.substring(BeanFactory.FACTORY_BEAN_PREFIX.length());
      }
      while (beanName.startsWith(BeanFactory.FACTORY_BEAN_PREFIX));
      return beanName;
   });
}
```

方法介绍：返回实际的Bean名称，剥离掉工厂间接引用前缀（如果发现存在重复的工厂前缀也都会被去掉）。

```java
/**
 * Return whether the given name is a bean name which has been generated
 * by the default naming strategy (containing a "#..." part).
 * @param name the name of the bean
 * @return whether the given name is a generated bean name
 * @see #GENERATED_BEAN_NAME_SEPARATOR
 * @see org.springframework.beans.factory.support.BeanDefinitionReaderUtils#generateBeanName
 * @see org.springframework.beans.factory.support.DefaultBeanNameGenerator
 */
public static boolean isGeneratedBeanName(@Nullable String name) {
   return (name != null && name.contains(GENERATED_BEAN_NAME_SEPARATOR));
}
```

方法介绍：检查指定名称是不是已经被默认命名策略生成的Bean名称（没错，就是那些包含"#..."的名称）。

```java
/**
 * Extract the "raw" bean name from the given (potentially generated) bean name,
 * excluding any "#..." suffixes which might have been added for uniqueness.
 * @param name the potentially generated bean name
 * @return the raw bean name
 * @see #GENERATED_BEAN_NAME_SEPARATOR
 */
public static String originalBeanName(String name) {
   Assert.notNull(name, "'name' must not be null");
   int separatorIndex = name.indexOf(GENERATED_BEAN_NAME_SEPARATOR);
   return (separatorIndex != -1 ? name.substring(0, separatorIndex) : name);
}
```

方法介绍：从给定的（潜在生成的）name中抽取原始bean名称。排除任何可能是为了唯一化而添加的"#..."后缀。

```java
/**
 * Count all beans in any hierarchy in which this factory participates.
 * Includes counts of ancestor bean factories.
 * <p>Beans that are "overridden" (specified in a descendant factory
 * with the same name) are only counted once.
 * @param lbf the bean factory
 * @return count of beans including those defined in ancestor factories
 * @see #beanNamesIncludingAncestors
 */
public static int countBeansIncludingAncestors(ListableBeanFactory lbf) {
   return beanNamesIncludingAncestors(lbf).length;
}
```

方法介绍：返回当前工厂参与的所有的所有层级关系，包括祖先bean工厂的个数。

Bean会被覆盖（后代工厂中某个Bean指定了相同的名称）会只计算一次。

```java
/**
 * Return all bean names in the factory, including ancestor factories.
 * @param lbf the bean factory
 * @return the array of matching bean names, or an empty array if none
 * @see #beanNamesForTypeIncludingAncestors
 */
public static String[] beanNamesIncludingAncestors(ListableBeanFactory lbf) {
   return beanNamesForTypeIncludingAncestors(lbf, Object.class);
}
```

方法介绍：返回当前工厂的所有bean的名称，包括其祖先工厂。

```java
/**
 * Get all bean names for the given type, including those defined in ancestor
 * factories. Will return unique names in case of overridden bean definitions.
 * <p>Does consider objects created by FactoryBeans, which means that FactoryBeans
 * will get initialized. If the object created by the FactoryBean doesn't match,
 * the raw FactoryBean itself will be matched against the type.
 * <p>This version of {@code beanNamesForTypeIncludingAncestors} automatically
 * includes prototypes and FactoryBeans.
 * @param lbf the bean factory
 * @param type the type that beans must match (as a {@code ResolvableType})
 * @return the array of matching bean names, or an empty array if none
 * @since 4.2
 * @see ListableBeanFactory#getBeanNamesForType(ResolvableType)
 */
public static String[] beanNamesForTypeIncludingAncestors(ListableBeanFactory lbf, ResolvableType type) {
   Assert.notNull(lbf, "ListableBeanFactory must not be null");
   String[] result = lbf.getBeanNamesForType(type);
   if (lbf instanceof HierarchicalBeanFactory) {
      HierarchicalBeanFactory hbf = (HierarchicalBeanFactory) lbf;
      if (hbf.getParentBeanFactory() instanceof ListableBeanFactory) {
         String[] parentResult = beanNamesForTypeIncludingAncestors(
               (ListableBeanFactory) hbf.getParentBeanFactory(), type);
         result = mergeNamesWithParent(result, parentResult, hbf);
      }
   }
   return result;
}
```

方法介绍：根据指定类型获得所有bean的名称，包括那些定义在祖先工厂中的beans。这个方法在bean defintions被重写的情况下将会返回唯一的名称。考虑到FactoryBean创建的对象，这意味着FactoryBeans将会被初始化。如果FactoryBean创建的对象没有匹配，则将会去和FactoryBean本身的类型进行匹配。

当前版本的beanNamesForTypeIncludingAncestors()会自动包括Prototypes和FactoryBeans。

```java
/**
 * Get all bean names for the given type, including those defined in ancestor
 * factories. Will return unique names in case of overridden bean definitions.
 * <p>Does consider objects created by FactoryBeans if the "allowEagerInit"
 * flag is set, which means that FactoryBeans will get initialized. If the
 * object created by the FactoryBean doesn't match, the raw FactoryBean itself
 * will be matched against the type. If "allowEagerInit" is not set,
 * only raw FactoryBeans will be checked (which doesn't require initialization
 * of each FactoryBean).
 * @param lbf the bean factory
 * @param type the type that beans must match (as a {@code ResolvableType})
 * @param includeNonSingletons whether to include prototype or scoped beans too
 * or just singletons (also applies to FactoryBeans)
 includeNonSingletons标识，是否将prototype或者作用域bean纳入考虑范围，还是仅包括单例（也适用于FactoryBeans）
 * @param allowEagerInit whether to initialize <i>lazy-init singletons</i> and
 * <i>objects created by FactoryBeans</i> (or by factory methods with a
 * "factory-bean" reference) for the type check. Note that FactoryBeans need to be
 * eagerly initialized to determine their type: So be aware that passing in "true"
 * for this flag will initialize FactoryBeans and "factory-bean" references.
 * @return the array of matching bean names, or an empty array if none
 * @since 5.2
 * @see ListableBeanFactory#getBeanNamesForType(ResolvableType, boolean, boolean)
 */
public static String[] beanNamesForTypeIncludingAncestors(
      ListableBeanFactory lbf, ResolvableType type, boolean includeNonSingletons, boolean allowEagerInit) {

   Assert.notNull(lbf, "ListableBeanFactory must not be null");
   String[] result = lbf.getBeanNamesForType(type, includeNonSingletons, allowEagerInit);
   if (lbf instanceof HierarchicalBeanFactory) {
      HierarchicalBeanFactory hbf = (HierarchicalBeanFactory) lbf;
      if (hbf.getParentBeanFactory() instanceof ListableBeanFactory) {
         String[] parentResult = beanNamesForTypeIncludingAncestors(
               (ListableBeanFactory) hbf.getParentBeanFactory(), type, includeNonSingletons, allowEagerInit);
         result = mergeNamesWithParent(result, parentResult, hbf);
      }
   }
   return result;
}
```

方法介绍：根据指定的类型获得bean的名称，包括定义在祖先工厂的beans。在bean definitions被重写的情况下，这个方法将会返回唯一的名称。

考虑到FactoryBeans创建的对象，如果allowEagerInit设置为true，这就意味着FactoryBeans将会被初始化。如果FactoryBean创建的对象没有匹配上，那么将会与FactoryBean本身的类型进行匹配。如果allowEagerInit设置为false，那么只有FactoryBeans的本身的对象会进行检查是否匹配（这就不需要对每个FactoryBean进行初始化）.不太理解第三个@Param的“prototype or scoped beans”难道说prototype不属于[scoped](https://blog.csdn.net/smile_from_2015/article/details/51259463)？

```java
/**
 * Get all bean names for the given type, including those defined in ancestor
 * factories. Will return unique names in case of overridden bean definitions.
 * <p>Does consider objects created by FactoryBeans, which means that FactoryBeans
 * will get initialized. If the object created by the FactoryBean doesn't match,
 * the raw FactoryBean itself will be matched against the type.
 * <p>This version of {@code beanNamesForTypeIncludingAncestors} automatically
 * includes prototypes and FactoryBeans.
 * @param lbf the bean factory
 * @param type the type that beans must match (as a {@code Class})
 * @return the array of matching bean names, or an empty array if none
 * @see ListableBeanFactory#getBeanNamesForType(Class)
 */
public static String[] beanNamesForTypeIncludingAncestors(ListableBeanFactory lbf, Class<?> type) {
   Assert.notNull(lbf, "ListableBeanFactory must not be null");
   String[] result = lbf.getBeanNamesForType(type);
   if (lbf instanceof HierarchicalBeanFactory) {
      HierarchicalBeanFactory hbf = (HierarchicalBeanFactory) lbf;
      if (hbf.getParentBeanFactory() instanceof ListableBeanFactory) {
         String[] parentResult = beanNamesForTypeIncludingAncestors(
               (ListableBeanFactory) hbf.getParentBeanFactory(), type);
         result = mergeNamesWithParent(result, parentResult, hbf);
      }
   }
   return result;
}
```

方法介绍：通过指定类型获得bean的所有names，检查的范围包括那些定义在祖先工厂中的beans。在bean definitions被重写的情况下会返回全局唯一的名称。

将会考虑FactoryBeans创建的对象，这意味着FactoryBeans将会被初始化，如果FactoryBean创建的对象类型没有被匹配上，那么将会和FactoryBean的本身类型进行匹配。

当前版本的beanNamesForTypeIncludingAncestors()会自动包括prototypes和FactoryBeans。

```java
/**
 * Get all bean names for the given type, including those defined in ancestor
 * factories. Will return unique names in case of overridden bean definitions.
 * <p>Does consider objects created by FactoryBeans if the "allowEagerInit"
 * flag is set, which means that FactoryBeans will get initialized. If the
 * object created by the FactoryBean doesn't match, the raw FactoryBean itself
 * will be matched against the type. If "allowEagerInit" is not set,
 * only raw FactoryBeans will be checked (which doesn't require initialization
 * of each FactoryBean).
 * @param lbf the bean factory
 * @param includeNonSingletons whether to include prototype or scoped beans too
 * or just singletons (also applies to FactoryBeans)
 * @param allowEagerInit whether to initialize <i>lazy-init singletons</i> and
 * <i>objects created by FactoryBeans</i> (or by factory methods with a
 * "factory-bean" reference) for the type check. Note that FactoryBeans need to be
 * eagerly initialized to determine their type: So be aware that passing in "true"
 * for this flag will initialize FactoryBeans and "factory-bean" references.
 allowEagerInit标志，是否会初始化懒加载单例和FactoryBean创建的对象（或者是通过具有factory-bean引用的工厂方法创建的对象）用于类型检查。注意FactoryBeans需要被急切初始化去确定他们的类型：所以注意当这个参数为true时将会初始化FactoryBeans和具有factory-bean的引用。
 * @param type the type that beans must match
 * @return the array of matching bean names, or an empty array if none
 * @see ListableBeanFactory#getBeanNamesForType(Class, boolean, boolean)
 */
public static String[] beanNamesForTypeIncludingAncestors(
      ListableBeanFactory lbf, Class<?> type, boolean includeNonSingletons, boolean allowEagerInit) {

   Assert.notNull(lbf, "ListableBeanFactory must not be null");
   String[] result = lbf.getBeanNamesForType(type, includeNonSingletons, allowEagerInit);
   if (lbf instanceof HierarchicalBeanFactory) {
      HierarchicalBeanFactory hbf = (HierarchicalBeanFactory) lbf;
      if (hbf.getParentBeanFactory() instanceof ListableBeanFactory) {
         String[] parentResult = beanNamesForTypeIncludingAncestors(
               (ListableBeanFactory) hbf.getParentBeanFactory(), type, includeNonSingletons, allowEagerInit);
         result = mergeNamesWithParent(result, parentResult, hbf);
      }
   }
   return result;
}
```

方法介绍：将会获得指定类型的bean的names，将会检查包括那些定义在祖先工厂中的beans。在bean definitions被覆盖的情况下会返回一个全局唯一的名称。

如果allowEagerInit设置为true，则将会考虑FactoryBeans创建的对象，这就意味着FactoryBeans将会被初始化。如果FactoryBean创建的对象没有匹配的上，那么将会与FactoryBean本身的类型进行匹配。

```java
/**
 * Get all bean names whose {@code Class} has the supplied {@link Annotation}
 * type, including those defined in ancestor factories, without creating any bean
 * instances yet. Will return unique names in case of overridden bean definitions.
 * @param lbf the bean factory
 * @param annotationType the type of annotation to look for
 * @return the array of matching bean names, or an empty array if none
 * @since 5.0
 * @see ListableBeanFactory#getBeanNamesForAnnotation(Class)
 */
public static String[] beanNamesForAnnotationIncludingAncestors(
      ListableBeanFactory lbf, Class<? extends Annotation> annotationType) {

   Assert.notNull(lbf, "ListableBeanFactory must not be null");
   String[] result = lbf.getBeanNamesForAnnotation(annotationType);
   if (lbf instanceof HierarchicalBeanFactory) {
      HierarchicalBeanFactory hbf = (HierarchicalBeanFactory) lbf;
      if (hbf.getParentBeanFactory() instanceof ListableBeanFactory) {
         String[] parentResult = beanNamesForAnnotationIncludingAncestors(
               (ListableBeanFactory) hbf.getParentBeanFactory(), annotationType);
         result = mergeNamesWithParent(result, parentResult, hbf);
      }
   }
   return result;
}
```

方法介绍：获得具备指定注解的Bean的names，包括那些定义在祖先工厂中的beans，而且不会创建任何bean实例。在bean definitions被重写的情况下会返回全局唯一的名称。

```java
/**
 * Return all beans of the given type or subtypes, also picking up beans defined in
 * ancestor bean factories if the current bean factory is a HierarchicalBeanFactory.
 * The returned Map will only contain beans of this type.
 * <p>Does consider objects created by FactoryBeans, which means that FactoryBeans
 * will get initialized. If the object created by the FactoryBean doesn't match,
 * the raw FactoryBean itself will be matched against the type.
 * <p><b>Note: Beans of the same name will take precedence at the 'lowest' factory level,
 * i.e. such beans will be returned from the lowest factory that they are being found in,
 * hiding corresponding beans in ancestor factories.</b> This feature allows for
 * 'replacing' beans by explicitly choosing the same bean name in a child factory;
 * the bean in the ancestor factory won't be visible then, not even for by-type lookups.
 * @param lbf the bean factory
 * @param type type of bean to match
 * @return the Map of matching bean instances, or an empty Map if none
 * @throws BeansException if a bean could not be created
 * @see ListableBeanFactory#getBeansOfType(Class)
 */
public static <T> Map<String, T> beansOfTypeIncludingAncestors(ListableBeanFactory lbf, Class<T> type)
      throws BeansException {

   Assert.notNull(lbf, "ListableBeanFactory must not be null");
   Map<String, T> result = new LinkedHashMap<>(4);
   result.putAll(lbf.getBeansOfType(type));
   if (lbf instanceof HierarchicalBeanFactory) {
      HierarchicalBeanFactory hbf = (HierarchicalBeanFactory) lbf;
      if (hbf.getParentBeanFactory() instanceof ListableBeanFactory) {
         Map<String, T> parentResult = beansOfTypeIncludingAncestors(
               (ListableBeanFactory) hbf.getParentBeanFactory(), type);
         parentResult.forEach((beanName, beanInstance) -> {
            if (!result.containsKey(beanName) && !hbf.containsLocalBean(beanName)) {
               result.put(beanName, beanInstance);
            }
         });
      }
   }
   return result;
}
```

方法介绍：将会返回匹配指定类型或者子类型的所有beans，如果当前bean factory是一个HierarchicalBeanFactory类型，那么该函数也会去祖先bean工厂中的beans去筛选。返回的Map将只会包含指定类型的beans。

该方法将会考虑由FactoryBeans创建的对象，这就意味着FactoryBeans将会被初始化。如果FactoryBeans创建的对象类型皮配不上，那么将会与FactoryBean本身的类型进行匹配。

注意：Bean名称相同的情况下，最低层级工厂优先级最高，也就是说，如果在低层级的工厂中查找到匹配的bean那么它将会被作为结果返回，隐藏祖先工厂中相应的beans。这个特性允许通过显示选择子工厂同名的beans去替换祖先工厂的beans；这个在祖先工厂的同名的bean将会是不可见的，即使是通过按类型查询。

```java
/**
 * Return all beans of the given type or subtypes, also picking up beans defined in
 * ancestor bean factories if the current bean factory is a HierarchicalBeanFactory.
 * The returned Map will only contain beans of this type.
 * <p>Does consider objects created by FactoryBeans if the "allowEagerInit" flag is set,
 * which means that FactoryBeans will get initialized. If the object created by the
 * FactoryBean doesn't match, the raw FactoryBean itself will be matched against the
 * type. If "allowEagerInit" is not set, only raw FactoryBeans will be checked
 * (which doesn't require initialization of each FactoryBean).
 * <p><b>Note: Beans of the same name will take precedence at the 'lowest' factory level,
 * i.e. such beans will be returned from the lowest factory that they are being found in,
 * hiding corresponding beans in ancestor factories.</b> This feature allows for
 * 'replacing' beans by explicitly choosing the same bean name in a child factory;
 * the bean in the ancestor factory won't be visible then, not even for by-type lookups.
 * @param lbf the bean factory
 * @param type type of bean to match
 * @param includeNonSingletons whether to include prototype or scoped beans too
 * or just singletons (also applies to FactoryBeans)
 * @param allowEagerInit whether to initialize <i>lazy-init singletons</i> and
 * <i>objects created by FactoryBeans</i> (or by factory methods with a
 * "factory-bean" reference) for the type check. Note that FactoryBeans need to be
 * eagerly initialized to determine their type: So be aware that passing in "true"
 * for this flag will initialize FactoryBeans and "factory-bean" references.
 * @return the Map of matching bean instances, or an empty Map if none
 * @throws BeansException if a bean could not be created
 * @see ListableBeanFactory#getBeansOfType(Class, boolean, boolean)
 */
public static <T> Map<String, T> beansOfTypeIncludingAncestors(
      ListableBeanFactory lbf, Class<T> type, boolean includeNonSingletons, boolean allowEagerInit)
      throws BeansException {

   Assert.notNull(lbf, "ListableBeanFactory must not be null");
   Map<String, T> result = new LinkedHashMap<>(4);
   result.putAll(lbf.getBeansOfType(type, includeNonSingletons, allowEagerInit));
   if (lbf instanceof HierarchicalBeanFactory) {
      HierarchicalBeanFactory hbf = (HierarchicalBeanFactory) lbf;
      if (hbf.getParentBeanFactory() instanceof ListableBeanFactory) {
         Map<String, T> parentResult = beansOfTypeIncludingAncestors(
               (ListableBeanFactory) hbf.getParentBeanFactory(), type, includeNonSingletons, allowEagerInit);
         parentResult.forEach((beanName, beanInstance) -> {
            if (!result.containsKey(beanName) && !hbf.containsLocalBean(beanName)) {
               result.put(beanName, beanInstance);
            }
         });
      }
   }
   return result;
}
```

方法介绍：将会返回匹配上的指定类型或子类型的所有beans，如果当前bean factory是HierarchicalBeanFactory类型，那么也将会从定义在祖先bean factories中的beans进行筛选。

返回的Map只包含指定类型的beans。

如果allowEagerInit为true，将会考虑FactoryBeans创建的对象,这意味着FactoryBeans将会被初始化。如果FactoryBean创建的对象类型不匹配，那么将会与FactoryBean本身的类型进行匹配。如果allowEagerInit设置为false，则将只会匹配FactoryBean的类型是否匹配（这就意味着不会初始化每一个FactoryBean）。

注意：在Bean的名称相同的情况下，最低层级的bean的优先级最高，换言之，当在最底层及的工厂中发现了匹配的beans，则他们将会被作为结果返回。在祖先工厂中相应的beans将会被隐藏。这个特性允许通过显示选择子工场同名的bean去替换祖先工厂的beans，那么祖先工厂的同名的bean将会不可见，即使是通过按类型查找的方式。

















