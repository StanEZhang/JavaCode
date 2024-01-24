Feign 是什么？

Feign 是一个声明式的 HTTP 客户端，它可以帮助开发者更加方便地调用 RESTful API。

首先我们明确，在远程调用中的两个角色：

1.调用方

2.被调用方



**被调用方要做什么？**

你被调用方基本什么也不用做，你就做一件事，提供服务，也就是说，人家调用方要调用你的东西，你得把东西准备好。

**调用方要做什么？**

1.引入依赖

你没有依赖包怎么远程调用？

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

2.开启Feign功能

你光有依赖不打开开关也不好使呀！

3.指定客户端位置

你打开开关还不行，你还得告诉openfeign你写的客户端在哪呢？

```java
@EnableFeignClients("com.haibin.member.feign")
@SpringBootApplication
public class MemberApplication {
    public static void main(String[] args) {
        SpringApplication.run(MemberApplication.class, args);
    }
}
```

4.客户端？什么客户端？

你得告诉openfeign你要去**哪个服务**要东西，**要什么东西？**

```java
@FeignClient("coupon") // feign客户端注解，括号内填被调用方的服务名
public interface CouponFeignService {
    @GetMapping("/coupon/list") // 所有路径定义在接口方法上
    List<Coupon> test();
}
```

