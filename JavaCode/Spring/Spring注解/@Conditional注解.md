在了解 @Conditional 之前先花 10 秒钟复习一下 @Configuration 这个注解。



@Configuration 是干什么？

是配合 @Bean 注解来配置 Spring 容器的 bean 的。

那它为什么会出现呢？

因为配置 bean 的另一种方式是 xml ，狗都不用。

那给个示例看看呗？

简单。

```java
@Configuration
public class AppConfig {
    @Bean
    public MyBean myBean() {
        // 初始化, 配置, 返回bean...
    }
}
```



下面进入主题：

**@Conditional 是什么东西？**



首先明确第一点：

@Conditional 是 **Spring** 提供的。

其次明确第二点：

Conditional 中文译为：**有条件的，依...而定的。**



前边既然讲了 @Configuration 的简单用法，那么问题就是：

**@Configuration + @Conditional 有没有搞头？**



有搞头。

怎么搞？



那就是可以给@Configuration加上一个开关。

**我打开开关，你这配置就好用。**

**我关闭开关，你这配置就不好用。**



进一步升级。

我给 @Configuration 里的 @Bean 加一个开关。

**我打开开关，你这 bean 就注册。**

**我关闭开关，你这 bean 就不注册。**



那开关在哪里呢？

废话不多说，先上代码。

```java
@Configuration
@Conditional(MyCondition.class)
public class ConditionConfig {
    @Bean
    public ConditionBean conditionBean() {
        return new ConditionBean();
    }
}
```

开关就是代码中的 **MyCondition.class**。



**WTF is MyCondition.class ?**



MyCondition类就是我们自定义的开关，我们定义什么时候开，什么时候关的逻辑。

很明显这个逻辑不可能是 Spring 给的。

但 Spring 又必须插手管理。

通过什么呢？

没错，通过接口 Condition。

这个接口中什么都没有，只有一个 matches 方法，返回一个 boolean 值。

显而易见，你返回 true, 开关打开，返回 false，开关关闭。

MyCondition 类代码如下：

```java
public class MyCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        return true;
    }
}
```

可以看到 matches 方法中的两个参数，可以简单介绍一下。

**ConditionContext context** 这个参数提供了一种方法来访问关于Spring容器的信息。

**AnnotatedTypeMetadata metadata** 这个参数提供了对被注解类的元数据的访问。

基于这两个参数我们就可以**创建复杂和动态的条件来控制开关的闭合**。

例如，你可以基于环境变量、定义的bean、类的存在等因素，来决定是否创建某个bean。

​	

除了自定义 Condition，Springboot 还为我们扩展了一些常用的 Condition。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/25734432/1702457477336-2a124003-938c-4d15-91a1-03285c3bf522.png#averageHue=%23fbfbfa&clientId=uebcc11ba-fd13-4&from=paste&height=333&id=u63fad8d6&originHeight=458&originWidth=521&originalType=binary&ratio=1.375&rotation=0&showTitle=false&size=27062&status=done&style=none&taskId=ubadfc314-902c-45c7-af26-58a91c440c9&title=&width=378.90909090909093)