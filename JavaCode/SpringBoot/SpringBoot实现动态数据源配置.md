

**场景描述：**

前一阵子接手的新项目中需要使用2个数据源。

一个叫行云数据库，一个叫OceanBase数据库。

就是说，我有时候查询要查行云的数据，有时候查询要查 OceanBase 的数据，咋办？

废话不多说， 下面以mysql为例，开整。

# 一、环境依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.3.0</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.28</version>
</dependency>
```
# 二、实现思路
在进行下一步之前，我们必须要知道 **SpringBoot 自动配置的相关原理**，因为之前，我们大多是单数据源。
```yaml
# 配置mysql数据库
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/blog?serverTimezone=Asia/Shanghai&allowMultiQueries=true
    username: root
    password: root
```
从前只是会用，会配置，甚至都是别人给配置的自己直接拿来用，但是要搞懂**动态数据源**就必须要先搞懂**自动配置**。

[[SpringBoot自动配置]]

现在我们要实现**多数据源**，并且可以**自动切换**。

也就是我 A 查询连接的是行云数据库。

而我 B 查询却连接的是 OceanBase 数据库。

怎么办？

那第一个肯定就不能再使用DataSourceAutoConfigurtation了。

我直接反手一个 exclude 。

```java
@EnableAutoConfiguration(exclude = {DataSourceAutoConfiguration.class})
```

然后呢？

Spring boot想得很周到，它提供了AbstractRoutingDataSource 抽象类。

这个类能**根据用户定义的规则选择当前的数据源**。

---



有同学要问了：

AbstractRoutingDataSource 是什么东西？

AbstractRoutingDataSource 是一个抽象类。

它继承了 AbstractDataSource 抽象类。

而 AbstractDataSource 实现了 DataSource 接口。

也就是说：**AbstractRoutingDataSource 他实际上就是一个DataSource 。**

AbstractRoutingDataSource 中有两个关键方法。

```
setTargetDataSources(Map<Object, Object> targetDataSources)
protected abstract Object determineCurrentLookupKey();
```

第一个方法见名知意，**设置目标数据源**（复数也就是多个）。

第二个方法是**仅有的一个**抽象方法，需要开发者具体实现，也可以见名知意，**决定当前使用（目标数据源中的）哪个**。

我们要做的是什么？

我们准备 2 个数据源，全部配置好放进 Map<Object, Object> targetDataSources 里备用。

我们继承 AbstractRoutingDataSource 并实现抽象方法 determineCurrentLookupKey() 。

当我们继承AbstractRoutingDataSource时我们自身也是一个数据源，对于数据源必然有连接数据库的动作，只是AbstractRoutingDataSource的getConnection()方法里实际是调用determineTargetDataSource()返回的数据源的getConnection()方法。
这样我们可以在执行查询之前，设置使用的数据源。实现可动态路由的数据源，在每次数据库查询操作前执行。它的抽象方法 determineCurrentLookupKey() 决定使用哪个数据源。

我知道肯定有人看不懂要上嘴脸了：

**Talk is cheap, show me the FUCKING code !!!**



## 2.1 配置文件
```java
spring:
  datasource:
    dynamic:
      db1:
        driverClassName: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/db1?serverTimezone=Asia/Shanghai&allowMultiQueries=true
        username: root
        password: root
      db2:
        driverClassName: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/db2?serverTimezone=Asia/Shanghai&allowMultiQueries=true
        username: root
        password: root
```
## 2.2 自定义动态数据源
DynamicDataSource继承AbstractRoutingDataSource。
```java
public class DynamicDataSource extends AbstractRoutingDataSource {
    @Override
    protected Object determineCurrentLookupKey() {
        return DynamicDataSourceContextHolder.getDataSource();
    }
}
```
这里的determineCurrentLookupKey方法，需要返回一个数据源。

又有同学问了：

DynamicDataSourceContextHolder 又是什么东西？

```java
public class DynamicDataSourceContextHolder {
    public static final ThreadLocal<String> CONTEXT_HOLDER = new ThreadLocal<>();

    public static String getDataSource() {
        return CONTEXT_HOLDER.get();
    }

    public static void setDataSource(String dataSource) {
        CONTEXT_HOLDER.set(dataSource);
    }

    public static void clearDataSource() {
        CONTEXT_HOLDER.remove();
    }
}
```
看到 Context 应该很熟悉了，跟上下文有关。

它的作用就是你查询数据库的时候用哪个数据源，就 setDataSource 哪个。

还有点懵？没事，继续往下看。

```java
@Configuration
@EnableAutoConfiguration(exclude = {DataSourceAutoConfiguration.class})
public class DynamicDataSourceConfig {
    @Bean("db1")
    @ConfigurationProperties(prefix = "spring.datasource.dynamic.db1")
    public DataSource db1() {
        return DataSourceBuilder.create().build();
    }
    @Bean("db2")
    @ConfigurationProperties(prefix = "spring.datasource.dynamic.db2")
    public DataSource db2() {
        return DataSourceBuilder.create().build();
    }

    @Bean
    @Primary
    public DataSource dataSource() {
        Map<Object, Object> dataSourceMap = new HashMap<>(2);
        dataSourceMap.put("db1", db1());
        dataSourceMap.put("db2", db2());

        DynamicDataSource dynamicDataSource = new DynamicDataSource();
        // 需要设置的多数据源
        dynamicDataSource.setTargetDataSources(dataSourceMap);
        // 主数据源/默认数据源
        dynamicDataSource.setDefaultTargetDataSource(db1());
        return dynamicDataSource;
    }
}
```

这是比较常见的自定义数据源配置了。

可以看到一共注册了3个数据源。

但是最后一个DynamicDataSource有 @Primary 注解，它表明这个数据源优先级更高。

DynamicDataSource中设置了dataSourceMap，也就是保存了 db1 和 db2。



以上我们动态数据源配置的工作就做完了。

我们以实际查询中的操作完整捋一遍这当中到底发生了什么！

```java
@Service
public class UserService {
    @Autowired
    private UserMapper userMapper;
    /**
     *使用db2数据源
     */
    public void saveUser(UserDto userDto) {
        DynamicDatasourceHolder.setDataSource("db2");
        User user = new User();
        user.setUName(userDto.getName());
        userMapper.insert(user);
        DynamicDatasourceHolder.removeDataSource("db2");
    }
}
```

首先，DynamicDatasourceHolder 设置了数据源 db2 。

CONTEXT_HOLDER 中就保存了一个 “db2” 字符串。

userMapper 进行数据库操作之前，MyBatis 框架替我们做了一些事。

其中一件事是**获取数据库连接**。

MyBatis 就在想：我得找个 DataSource ，因为DataSource 有getConnection() 方法。

谁是 DataSource ？

继承了 AbstractRoutingDataSource 的 DynamicDataSource 大声喊到：我是 ！

开始连接数据库！

```java
@Override
public Connection getConnection() throws SQLException {
	return determineTargetDataSource().getConnection();
}
```

连接哪个？

```java
protected DataSource determineTargetDataSource() {
    // 哥，看这一行！
   Object lookupKey = determineCurrentLookupKey();
   DataSource dataSource = this.resolvedDataSources.get(lookupKey);
   return dataSource;
}
```

连接这个！

```java
@Override
protected Object determineCurrentLookupKey() {
    return DynamicDataSourceContextHolder.getDataSource();
}
```

连接完成！

insert 完成！

removeDataSource("db2") ！



每次这样用嫌麻烦？

办他！

# 三、动态数据源注解@DS
看这部分之前需要一些前置知识点：

[[Java注解，看完就会用]]

[[师爷，翻译翻译什么叫AOP]]

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Component
public @interface DS {
    String value() default "db1";
}
```
```java
@Component
@Aspect
public class DynamicDataSourceAspect {

    @Pointcut("@annotation(com.example.demo.annotation.DS)")
    public void dynamicDataSourcePointCut() {

    }

    @Around("dynamicDataSourcePointCut()")
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        String dataSourceKey = "db1";
        // 类上的注解
        Class<?> aClass = joinPoint.getTarget().getClass();
        DS annotation = aClass.getAnnotation(DS.class);

        // 方法上的注解
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        DS annotationMethod = signature.getMethod().getAnnotation(DS.class);

        if (Objects.nonNull(annotationMethod)) {
            dataSourceKey = annotationMethod.value();
        } else {
            dataSourceKey = annotation.value();
        }
        // 设置数据源
        DynamicDataSourceContextHolder.setDataSource(dataSourceKey);
        try {
             return joinPoint.proceed();
        }finally {
            DynamicDataSourceContextHolder.clearDataSource();
        }
    }
}
```

具体我就不讲解了，自己对照前两篇文章研究一下吧。

---

最后再补充一下，动态数据源实际上有现成的依赖包可以用，可以参考使用，地址如下：

https://github.com/baomidou/dynamic-datasource