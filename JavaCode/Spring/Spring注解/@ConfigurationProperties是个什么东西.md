学习 @ConfigurationProperties 之前我们需要一些前置知识点：

@Value是个什么东西

我们在讲 @Value 时知道了如何将配置文件的属性注入到变量中，废话不多说，直接上代码。

这是配置文件配置：

```properties
spring.datasource.dynamic.mysql.driverClassName=com.mysql.cj.jdbc.Driver
spring.datasource.dynamic.mysql.url=jdbc:mysql://localhost:3306/db1
spring.datasource.dynamic.mysql.username=root
spring.datasource.dynamic.mysql.password=root
```

麻烦？

没事，有简单的，我们用简单的：

```yml
spring:
  datasource:
    dynamic:
      mysql:
        driverClassName: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/db1
        username: root
        password: root
```

使用 @Value 获取配置：

```java
@Configuration
public class DynamicDataSourceConfig {
    
    @Value("${spring.datasource.dynamic.mysql.driverClassName}")
    private String driverClassName;
    @Value("${spring.datasource.dynamic.mysql.url}")
    private String url;
    @Value("${spring.datasource.dynamic.mysql.username}")
    private String username;
    @Value("${spring.datasource.dynamic.mysql.password}")
    private String password;    

}
```

写完之后整整齐齐，帅！

如果有人觉得帅，一定还没成长成一个合格的程序员。

我们程序员追求什么？

优雅。

**优雅永不过时。**

优雅最重要的一条原则是什么？

不写重复代码！

所以怎么办？

有办法。

下面我们讲一下 **@ConfigurationProperties** 。

# 作用

> 将标注了 @ConfigurationProperties 注解的Spring容器中的Bean与配置文件中的属性进行一一绑定，用于更加快速、方便的读取配置文件的内容。

# 使用方式一：@Component

直接上代码：

```java
@Component
@ConfigurationProperties(prefix = "spring.datasource.dynamic.mysql")
@Data
public class DynamicDataSourceConfig {
    
    private String driverClassName;
    private String url;
    private String username;
    private String password;    

}
```

写完之后清清爽爽，帅！

我们发现了几个关键点：

**1.原来的 @Configuration 变成了 @Component**

这个不影响，因为 @Configuration 中包含了 @Component，这里主要强调**通过@Component注解将其注册成Bean**。

**2.@ConfigurationProperties(prefix = "")**

这是我们的主角，prefix 就是匹配我们属性值的前缀，有了它我们就少写好多代码。

**3.多了个@Data**

@Data的功能中包含了setter方法，没有setter怎么赋值？！



# 使用方式二：@EnableConfigurationProperties

WTF is @EnableConfigurationProperties？

@EnableConfigurationProperties 就是：

**@ConfigurationProperties，启动！**



如果配置类只配置了 @ConfigurationProperties 注解，而没有使用@Component，也就是在IOC容器中是获取不到bean。

那就需要 **@EnableConfigurationProperties** 把使用 @ConfigurationProperties 的类进行了一次**注入**。

怎么用呢？

**需要与@Configuration一起使用。**

**需要与@Configuration一起使用。**

**需要与@Configuration一起使用。**

上代码：

```java
@ConfigurationProperties(prefix = "father")
@Data
public class Father {
   private String name;
}
```

```java
@Configuration
@EnableConfigurationProperties(Father.class)
public class MainConfig {
}
```

实际开发中可能更多的写在 Application 启动类上。



这时候 A 同学问了：

**那启动类上没有 @Configuration 啊？**

有的，@SpringBootApplication 也是一个@Configuration。



又有 B 同学问了：

**那我有两个配置类怎么写呢？**

哎？好办。

```java
@Configuration
@EnableConfigurationProperties(Father.class, Son.class)
public class MainConfig {
}
```

又有 C 同学问了：

**那我有 100 个配置类呢？**

聪明的 B 同学说：

我知道我知道！

```java
@Configuration
@EnableConfigurationProperties(Father.class, Son.class, GrandSon.class, GrandgrandSon.class...)
public class MainConfig {
}
```

...

不愧是你。

那怎么办呢？

也有办法。



# 使用方式三：@ConfigurationPropertiesScan

SpringBoot 2.2.0版本以后提供了这个注解。

这个注解加到启动类上，注明包路径。

系统就会扫描这个包路径下的所有包含 @ConfigurationProperties 注解的配置类。

**如果不写包路径，就默认扫描启动类所在包及其子包。**

这样就不用写一百个类了。

```java
@SpringBootApplication
@ConfigurationPropertiesScan
public class DemoAnnotationApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoAnnotationApplication.class, args);
    }
}
```



差不多了吧？

什么？小明，你有话说？

那既然 @Component 能注册成 Bean，那我使用 @Configuration 加 @Bean 注册又怎么说？

**卧槽，还有高手？**



# 使用方式四：@Bean

```java
@Data
public class Animal {
    private String name;
}
```

```java
@Configuration
public class MainConfig {
    @Bean
    @ConfigurationProperties(prefix = "animal")
    public Animal getAnimalBean() {
        return new Animal();
    }
}
```

```properties
animal.name=Wangcai
```



**还有高手吗？**