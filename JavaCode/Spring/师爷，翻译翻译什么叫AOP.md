> 张麻子：汤师爷，翻译翻译，什么叫AOP？
>
> 汤师爷：这还用翻译。
>
> 张麻子：我让你翻译给我听，什么叫AOP？
>
> 汤师爷：不用翻译，切面编程就是AOP啊。
>
> 黄四郎：难道你听不懂什么叫AOP？
>
> 张麻子：我就想让你翻译翻译，什么叫AOP！
>
> 汤师爷：AOP嘛。
>
> 张麻子：翻译出来给我听，什么他妈的叫AOP！什么他妈的叫他妈的AOP！
>
> 汤师爷：什么他妈的叫AOP啊？
>
> 黄四郎：AOP就是Aspect Oriented Programming，面向切面编程！明白了吗？
>
> 汤师爷：这就是AOP啊。
>
> 张麻子：翻译翻译。
>
> 汤师爷：...
>
> 汤师爷：AOP就是Aspect Oriented Programming！面向切面编程！面向！切面！横着切！切面！
>
> 张麻子：哈，大哥这是他妈的AOP啊，小弟我马上给个三连。



下面我们好好翻译一下AOP切面编程。

老规矩，在学习切面面编程之前要有前置知识：

[[终于搞懂动态代理了！]]

# 目标

- 动态代理搞明白，AOP就明白了
- 学会在开发中使用Spring的AOP技术

# 概念

AOP 是 Aspect Oriented Programming，和OOP（Object Oriented Programming）一词之差，OOP强调万物皆是对象，那AOP呢？

要真正理解 AOP 就要理解 AOP 的核心：**Aspect**。

**WTF is Aspect?**

我们把aspect翻译成切面，但是切面这个词对应中文语义其实很难理解到位。

我们换种解释，aspect我们理解为**事物的某个方面、某个视角**。

与面向对象思想相对，对象强调一个整体，一个人站在你面前，我们称之为对象。

而aspect强调**功能化、模块化、关注点分离**。

天气变冷了，人要多穿衣服，上帝控制这么多人的对象，总不能一件一件穿吧？

所以AOP思想就是把天气变冷穿衣服这件事抽离出来，模块化，单独进行关注，然后经过编码实现后，上帝就可以进行一键穿衣，节省了大量工作。

放在实际开发中，我们以最常见的**日志打印**为例。

我们系统中有200个Controller，都要打印请求日志，那没有AOP思想的实践的话，我们只能一个一个在Controller里编写一遍又一遍的重复代码，有了AOP思想，就可以考虑把日志打印这件事抽离出来做成单独的业务，实现一劳永逸。

那怎么实现呢？

那就要靠我们的**动态代理**了。

什么？你还没看动态代理？

上帝在吗？把这个人的棉衣扒了。



# Spring实现AOP

我们知道框架的存在意义是用来简化开发的。

这里Spring简化了什么呢？

自然是我们在动态代理部分编写的一大堆**要么看不懂，看懂了又不想写**的代码。

AOP作为Spring的左膀右臂之一，自然对这部分加以简化。

但Spring那一大堆xml也是够够的。

所以SpringBoot才是我们永远的家。



废话不多说直接上代码示例，在AOP代码的编写中提出问题，解决问题，那么最后就算是学会了。

## 引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!--aop-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```



## 编写代码

编写这部分代码的逻辑也是非常符合我们的认知逻辑的。

我们前边已经说清楚AOP是做什么的了。

那我们编写代码就要做3件事：

- **抽离出功能模块**——定义切面
- **确认功能代码加在哪**——定义切点
- **确认功能代码什么时候执行**——选择通知类型



我们先把controller给出来：

```java
@RestController
public class AopController {

    @RequestMapping("/hello")
    public String sayHello(){
        System.out.println("hello");
        return "hello";
    }
}
```

### 定义一个切面

也就是**抽离出功能模块**。

先随便写个类。

然后就直接一个@Aspect就行了，那这个类就是一个切面类。

还要再加一个@Component将该类纳入Ioc容器。

就这么简单，狗来了都会写。

```java
@Aspect
@Component
public class AopAdvice {
}
```

### 定义一个切点

也就是**确认功能代码加在哪**。

先随便写一个方法。

然后就直接一个@Pointcut就行了，那这个方法就是一个切点。

还要再加上表达式，让系统知道代码加到什么位置。

```java
@Aspect
@Component
public class AopAdvice {
    
    @Pointcut("execution (* com.example.aop.controller.*.*(..))")
    public void test() {
    }
}
```

这时候有同学问：

啊这个execution是什么？

里面那又是一坨什么？

根本看不懂。

举报了。

这个我只能说，这是固定的表达式，是规定。

规定什么？

**看规定之前先记住：表达式一定从右往左匹配。**

**看规定之前先记住：表达式一定从右往左匹配。**

**看规定之前先记住：表达式一定从右往左匹配。**

```text
execution(访问修饰符(可省略) 方法返回值 包名.类名.方法名(参数))
参数： ..代表任何参数
方法： *代表任何方法
类名： *代表所有类
包名： *代表所有包 ..代表子孙包
返回值： *代表所有类型返回值
```

具体的写法实际五花八门，而且除了execution还有一大堆，为了不让大脑过度疲劳，我们一次只有一个目标：

**会用，但不精通。**

### 选择通知类型

也就是**确认功能代码什么时候执行**。

下面就是通知类型5种，前3种比较常用：

**前置通知**（@Before）：在目标方法调用之前调用通知

**后置通知**（@After）：在目标方法完成之后调用通知

**环绕通知**（@Around）：在被通知的方法调用之前和调用之后执行自定义的方法

**返回通知**（@AfterReturning）：在目标方法成功执行之后调用通知

**异常通知**（@AfterThrowing）：在目标方法抛出异常之后调用通知



代码如下：

```java
@Aspect
@Component
public class AopAdvice {

    @Pointcut("execution (* com.example.aop.controller.*.*(..))")
    public void test() {

    }

    @Before("test()")
    public void beforeAdvice() {
        System.out.println("beforeAdvice...");
    }

    @After("test()")
    public void afterAdvice() {
        System.out.println("afterAdvice...");
    }

    @Around("test()")
    public void aroundAdvice(ProceedingJoinPoint joinPoint) {
        System.out.println("before");
        try {
            joinPoint.proceed();
        } catch (Throwable t) {
            t.printStackTrace();
        }
        System.out.println("after");
    }
}
```

从以上代码中可以看出，@Before和@After都已经简单到不能再简单了。

我们需要说一下这个**@Around 环绕通知**。

joinPoint.proceed()这行代码我们就理解为我们**需要增强的那个方法的替身**就行了。

这也不是想说的，这里我们主要讲一下 ProceedingJoinPoint joinPoint 这个参数。

ProceedingJoinPoint 是一个接口，也就是说这里实际是使用了多态，这不重要。

ProceedingJoinPoint 继承了 **JoinPoint ** 这个接口。

这个JoinPoint有2个方法是我们需要说的。

```java
Object getTarget();
Signature getSignature();
```

**`getTarget()` 方法返回的是目标对象，即那个被增强方法所属的对象实例。**

**`getSignature()` 方法返回的是连接点的签名，即关于被调用的方法（或访问的字段等）的静态信息，如方法名、返回类型、参数类型等。**

那么通过这2个方法，就能获取到**所有的对象信息和方法信息**，那么能做的事就太多了。

## 测试

启动项目，浏览器访问：

```text
http://localhost:8080/hello
```

运行结果：

```text
before
beforeAdvice...
hello
afterAdvice...
after
```


