对注解不了解的可以看一下：

[[Java注解，看完就会用]]



首先我们要明确：

**@Value 是 Spring 框架的注解。**



它有什么作用呢？

# 作用

> @Value 通过注解将**常量**、**配置文件中的值**、其他**bean的属性值**注入到变量中，作为变量的初始值。

# 使用方式

## 常量注入

顾名思义，就是把一个写死的值赋给对应变量，形式如下：

```java
// 注入普通字符串
@Value("Bin")
private String username; 

// 注入文件资源
@Value("classpath:com/test/config.xml")
private Resource resource; 

// 注入URL资源
@Value("http://www.baidu.com")
private Resource url; 

```

以上做法显而易见，并不能做到动态配置。

**这跟直接赋值有啥区别？**

所以这种方式应用很少，知道有这么个用法就行了。

## 读取配置文件

这种是应用最多的方式了，重点记住这种。

形式也简单，废话不多说，直接上代码。

配置文件 application.properties 或者 application.yml，写法略有不同。

application.properties：

```properties
common.name=bin
```

application.yml：

```yml
common:
  name: bin
```

```java
@Value("${common.name}")
private String name; 

// 配置文件找不到的话，给一个默认值
@Value("${common.name:JohnDoe}")   
private String name;
```



## 读取Bean的属性

读取Bean属性与读取配置文件属性用法不一样，**前者使用 $ 符号，后者使用 # 符号**。

首先将对象注册到 Spring 容器中：

```java
@Data
@Component
public class User {
    private String id;
    private String name;
}
```

Bean 属性注入：

```java
@Value("#{user.name}")
private String name;
```



# 使用案例

最后我们用一个简单案例做一个实现。

创建一个Bean对象：

```java
@Component
@Data
public class Person {
    
    @Value("bin")
    private String name;
    
    @Value("${age}")
    private Integer age;
}
```
配置文件：

```properties
age=22
```
测试：

```java
@SpringBootTest
class DemoApplicationTests {
    // 读取bean属性
    @Value("#{person.name}")
    private String name;
    // 读取bean属性
    @Value("#{person.age}")
    private String age;
    
    @Test
    void contextLoads() {
        System.out.println("常量注入获取name为：" + name);
        System.out.println("常量注入获取age为：" + age);
    }
}
```
结果：

```text
常量注入获取name为：bin
常量注入获取age为：22
```



以上就是对 @Value 的简单介绍，其实@Value还有许多高级用法，本篇不再深入。

---

联系我：
https://stanezhang.github.io/