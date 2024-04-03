学习 SpringBoot 自动配置之前我们需要一些前置知识点：

[[Java注解，看完就会用]]

[[@ConfigurationProperties注解]]

[[@Import注解]]

[[@Conditional注解]]

---

首先我们提出2个问题：

**SpringBoot 是干什么的？**

是用来简化 Spring 原生的复杂的 xml 配置的进阶框架。

**自动配置是什么？**

我们用另外一个问题回答这个问题。

**我们在 SpringBoot 开发中，就写了几个配置，怎么连接上的数据库？**

```yaml
spring:
  datasource:
    driverClassName: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://192.0.0.1:3306/test?useUnicode=true&useSSL=false&serverTimezone=Asia/Shanghai
    username: root
    password: root
```

在新手村时期，我们照着教程生搬硬抄的时候可能也想过这个问题，今天就来简单探究一下。

再次强调：

看明白本篇内容需要前置知识点，尤其是 **@Import** 注解。



这一切都要从 **@SpringBootApplication** 注解讲起。

# @SpringBootApplication

@SpringBootApplication 注解是一个复合注解，它由如下三个注解组成。
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration  // 第一个
@EnableAutoConfiguration  // 第二个
@ComponentScan(excludeFilters = {  // 第三个
		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM,
				classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
}
```
```java
@SpringBootApplication
├── @ComponentScan
├── @SpringBootConfiguration
│   └── @Configuration
└── @EnableAutoConfiguration
    ├── @Import(AutoConfigurationImportSelector.class)
    └── @AutoConfigurationPackage
        └── @Import(AutoConfigurationPackages.Registrar.class)
```


**@SpringBootConfiguration**

**@ComponentScan**

**@EnableAutoConfiguration**

从注解的中文意思中也可以看出来，第三个 **@EnableAutoConfiguration** 是与我们自动配置紧密相关的。

我们先快速搞定前2个。

## @SpringBootConfiguration

这个最简单，把头套摘下来，他就是一个普普通通的 @Configuration 注解包装而成而已，表示当前类是一个配置类。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration // 本体在这
public @interface SpringBootConfiguration {
}
```
## @ComponentScan 

> 可以指定basePackageClasses或basePackages（或其别名value）来定义要扫描的特定包。如果没有定义特定包，扫描将从声明此注解的类的包开始。

顾名思义就是扫描Component。

**扫描哪里？**

可以通过该注解的属性指定Spring应该扫描的包。如果没有指定包，则默认扫描声明 @ComponentScan 的类所在的包及其子包。

**哪些Component？**

使用 @Component, @Service, @Repository, @Controller 等注解的类。

**扫描了做什么用？**

将扫描到的组件注册为Spring的Bean，加入到ioC容器进行统一管理。



## @EnableAutoConfiguration

这是自动配置的核心注解！
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
}    
```
我们能看到，其中有2个注解：

**@AutoConfigurationPackage**

**@Import(AutoConfigurationImportSelector.class)**

### @AutoConfigurationPackage

我们再点进去看一下，发现它又是一个 **@Import** 。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {

}
```
@Import(AutoConfigurationPackages.Registrar.class) 中的 Regisgrar 类如下：

```java
static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {

    @Override
    public void registerBeanDefinitions(AnnotationMetadata metadata,
                                        BeanDefinitionRegistry registry) {
        register(registry, new PackageImport(metadata).getPackageName());
    }

    @Override
    public Set<Object> determineImports(AnnotationMetadata metadata) {
        return Collections.singleton(new PackageImport(metadata));
    }

}
```
如果你有去好好看 @Import 注解的那边文章，就会知道这是 **ImportBeanDefinitionRegistrar 接口实现**的方式。

这种方式提供一种手动方式灵活注册 bean。

在这里，我们根据两个提示就可以知道其功能：

- @AutoConfigurationPackage 是自动配置包的意思
- register(registry, new PackageImport(metadata).getPackageName()); 根据包名注册 bean

总结一下就是：

**根据通过注解提供的元数据，动态地向 Spring 容器中注册特定包中的类作为 beans。**



### @Import(AutoConfigurationImportSelector.class)

如果你有去好好看 @Import 注解的那边文章，就会知道这是 **ImportSelector接口实现**的方式。

这种方式只需实现 selectImports 方法，并以**数组**的形式返回要导入的类名，即可实现批量注册组件。

AutoConfigurationImportSelector 类中通过自己的源码实现了如下一个功能：

**把 spring-boot-autoconfigure 依赖中 META-INF/spring.factories 文件中需要自动配置的类名读取到数组中。**

它长这个样子：

```properties
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
...
```

那它到底怎么读取的？

这里我们不讨论具体实现，**有兴趣**且**有实力**的可以扒源码自己看一下。

当然这些类不会全部都用到，经过筛选，**去除重复、去除相应功能模块未开启的配置类、去除人为exclude掉的**，将剩余的最终配置类全类名String数组返回。



最后我们再来回收一下开始的那个问题。

**我们在 SpringBoot 开发中，就写了几个配置，怎么连接上的数据库？**

我们简单捋一下：

SpringBoot 启动。

@SpringBootApplication 注解生效。

@EnableAutoConfiguration 注解生效。

@Import(AutoConfigurationImportSelector.class) 注解生效。

读取到了org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration。

注册一个数据源bean。

读取到 xml 的数据源配置。

大功告成！



