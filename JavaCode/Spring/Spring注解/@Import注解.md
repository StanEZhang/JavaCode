首先我们要明确：@Import 注解是 Spring 提供的。

然后我们看一下该注解的官方注释：

```text
Indicates one or more component classes to import — typically @Configuration classes.

Provides functionality equivalent to the <import/> element in Spring XML. Allows for importing @Configuration classes, ImportSelector and ImportBeanDefinitionRegistrar implementations, as well as regular component classes (as of 4.2; analogous to AnnotationConfigApplicationContext.register).

@Bean definitions declared in imported @Configuration classes should be accessed by using @Autowired injection. Either the bean itself can be autowired, or the configuration class instance declaring the bean can be autowired. The latter approach allows for explicit, IDE-friendly navigation between @Configuration class methods.

May be declared at the class level or as a meta-annotation.

If XML or other non-@Configuration bean definition resources need to be imported, use the @ImportResource annotation instead.
```

我们将其中我们关心的重要部分提取出来翻译整理如下：

- 该注解可导入一个或多个组件类——通常是Configuration类。

- 允许导入@Configuration类、ImportSelector接口和ImportBeanDefinitionRegistrar接口的实现，以及普通类（从4.2版本开始）。

- @Import注解可以在类上声明，也可以作为元注解声明在其他注解上。



那么 @Import 注解到底有什么作用和优势呢？

记住下面几句话：

**导入配置。**

**从各个地方、通过各种方式导入配置。**

**在你需要的时候，从各个地方、通过各种方式导入配置。**

**在你需要的时候，从各个地方、通过各种方式导入并改造成你喜欢的配置。**



【表情包】



下面我们通过示例讲 4 种官方注释给出的导入类型，分别是：

- **普通类**

- **@Configuration类**
- **ImportSelector接口实现**
- **ImportBeanDefinitionRegistrar接口实现**



# 一、普通类

普通类不必啰嗦，有手就行。

```java
public class Circle {
}
```



```java
@Configuration
@Import({Circle.class})
public class MainConfig {
}
```



# 二、导入@Configuration类

我们建立三个不同的模块，MainModule, Module1, Module2。 

**让 MainModule 引入 Module1、Module2依赖。**

在 MainModule 中创建 MainConfig 类如下：

```java
@Configuration
@Import({Config1.class, Config2.class})
public class MainConfig {
}
```

很明显，Config1，Config2 分别属于2个模块，如下：

```java
@Configuration
public class Config1 {
    @Bean
    public String config1() {
        return "我是config1配置类！";
    }
}
```



```java
@Configuration
public class Config2 {
    @Bean
    public String config2() {
        return "我是config2配置类！";
    }
}
```

在 MainModule 中创建测试类看是否生效：

```java
@Test
void contextLoads() {
    AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(MainConfig.class);
    for (String name : context.getBeanDefinitionNames()) {
        System.out.println(String.format("%s->%s", name, context.getBean(name)));
    }
}
```

测试结果如下：

```text
mainConfig->com.example.annotation.config.MainConfig$$EnhancerBySpringCGLIB$$a98d5b6a@55a609dd
com.example.module1.config1->com.example.module1.config.Config1$$EnhancerBySpringCGLIB$$72aca5d0@4afd21c6
config1->我是config1配置类！
com.example.module2.config2->com.example.module2.config.Config2$$EnhancerBySpringCGLIB$$72aca5d0@4afd21c6
config2->我是config2配置类！
```



# 三、ImportSelector接口实现

首先我们需要认识一下 ImportSelector 接口：

```java
public interface ImportSelector {

	String[] selectImports(AnnotationMetadata importingClassMetadata);

	@Nullable
	default Predicate<String> getExclusionFilter() {
		return null;
	}
}
```

只有一个需要实现的方法：**selectImports** 。

**参数 AnnotationMetadata 是什么？**

翻译为注解元数据。

**有什么用？**

用来获取被@Import标注的类上面所有的注解信息。

**String[] 是什么？**

返回需要导入的类名的数组，可以是任何普通类，配置类。



上示例：

创建一个配置类

```java
@Configuration
public class Config {
    @Bean
    public String name() {
        return "公众号：JavaCode";
    }
}
```

定义一个ImportSelectors实现类

```java
public class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        return new String[]{
                Config.class.getName()
        };
    }
}
```

上述代码中需要说明一下，**Config.class.getName()** 得到的是类的**全类名**，也就是[包名.类名]，也就是com.example.annotation.pojo.Config。



导入MyImportSelector

```java
@Import({MyImportSelector.class})
public class MainConfig {
}
```

这时候有人问了：

这里怎么没有 @Configuration 注解？

没关系哈，虽然 @Import 注解通常是与 @Configuration 注解一起使用的，但**不是绝对必要**的。



# 四、ImportBeanDefinitionRegistrar接口实现

首先我们还是先认识一下 ImportBeanDefinitionRegistrar 接口：

```java
public interface ImportBeanDefinitionRegistrar {

	default void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry, BeanNameGenerator importBeanNameGenerator) {
		registerBeanDefinitions(importingClassMetadata, registry);
	}
	default void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
	}
}
```

可以看到只有2个方法，第一个方法调用第二个方法，所以我们就只看第二个方法就好了。

AnnotationMetadata 我们前边说过了：

**用来获取被@Import标注的类上面所有的注解信息。**

BeanDefinitionRegistry 呢？

**它是一个接口，内部提供了注册bean的各种方法，用于我们手动注册bean。**



上示例：

创建一个 Java 类

```java
public class Rectangle {
    public void sayHi() {
        System.out.println("Rectangle sayHi()");
    }
}
```

创建 ImportBeanDefinitionRegistrar 实现类

```java
public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {

    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        RootBeanDefinition rootBeanDefinition = new RootBeanDefinition(Rectangle.class);
        // 注册一个名字叫做 rectangle 的 bean
        registry.registerBeanDefinition("rectangle", rootBeanDefinition);
    }
}
```



# 总结

**第一种：**

我们有什么就可以导什么。

**第二种：**

我们可以把分散的配置集中起来，更加清晰和有组织。

**第三种：**

除了我们模块现有的配置，你只要能拿到类的全类名，就能注册进来。

**第四种：**

我们不光能到处拿，我们还能拿来以后随便改！



以上四种方式就实现了我们当初的豪言：

**导入配置。**

**从各个地方、通过各种方式导入配置。**

**在你需要的时候，从各个地方、通过各种方式导入配置。**

**在你需要的时候，从各个地方、通过各种方式导入并改造成你喜欢的配置。**