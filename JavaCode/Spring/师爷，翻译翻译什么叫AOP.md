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
> 汤师爷：AOP就是Aspect Oriented Programming，面向切面编程，面向！切面！横着切！切面！
>
> 张麻子：哈，大哥这是他妈的AOP啊，小弟我马上给个三连。



下面我们好好翻译一下AOP切面编程。

老规矩，在学习切面面编程之前要有前置知识：

[[动态代理]]

[[注解]]

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

放在实际开发中，我们以最常见的日志打印为例。

我们系统中有200个Controller，都要打印请求日志，那没有AOP思想的实践的话，我们只能一个一个在Controller里编写一遍又一遍的重复代码，有了AOP思想，就可以考虑把日志打印这件事抽离出来做成单独的业务，实现一劳永逸。

那怎么实现呢？

那就要靠我们的**动态代理**了。

什么？你还没看动态代理？

上帝在吗？把这个人的棉衣扒了。



# Spring实现AOP

我们知道框架的存在意义是用来简化开发的。

这里Spring简化了什么呢？

自然是我们在动态代理部分编写的一大堆要么看不懂，看懂了又不想写的代码。

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

编写这部分代码也是

### 定义一个切面

### 定义一个切点

### 选择通知类型

## 测试



还有一种结合自定义注解实现AOP的方式，更拉风，来练练手。

[[SpringBoot实现动态数据源配置]]