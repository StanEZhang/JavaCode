大家好，我是阿bin。

参数校验是每个后端开发都要面对的问题，很多同学包括我在内最拿手的就是if-else，但是这样校验会造成大量的代码冗余，今天就来学习如何**优雅**的校验参数。

# 一、JSR 303

## 1. 什么是 JSR 303？

JSR 是 Java Specification Requests 的缩写，即 Java 规范提案。

存在各种各样的 JSR，简单的理解为 JSR 是一种 Java 标准。 

JSR 303 就是**数据检验的一个标准**（Bean Validation (JSR 303)）。 


## 2. 为什么使用 JSR 303？

处理一段业务逻辑，首先要确保数据输入的正确性，然后才能进行下一步的处理。

前端有自己的校验方式，那后端呢？

后端最简单的实现就是直接在业务方法中对数据进行处理，但是不同的业务方法可能会出现同样的校验操作，这样就出现了**数据的冗余**。

为了解决这个情况，JSR 303 出现了。

JSR 303 使用 Bean Validation，即**在 Bean 上添加相应的注解，去实现数据校验**。

这样就可以减少代码冗余。



## 3. JSR 303 常见操作？

（1）可以通过简单的注解校验 Bean 属性，比如 @NotNull、@Null 等。

（2）可以通过 Group 分组自定义需要执行校验的属性。

（3）可以自定义注解并指定校验规则。

（4）支持基于 JSR 303 的实现，比如 Hibernate Validator（额外添加一些注解）。



# 二、JSR303 的简单使用

## 1. 未使用 JSR303 相关注解时

直接在业务方法里手动if-else。

```java
@RestController
@RequestMapping("api")
public class EmpController {
    @Resource
    private EmpService empService;

    @PostMapping("/emp")
    public Result createEmp(@RequestBody Emp emp) {
        if (emp.getId() == null || emp.getName() == null) {
            return Result.error().message("数据不存在");
        }
        return Result.ok().data("items", emp).message("数据插入成功");
    }
}
```



## 2. 使用 JSR 303 相关注解处理逻辑

### 第一步：在相关的 Bean 上标注注解，并写上异常信息

```java
import lombok.Data;

import javax.validation.constraints.NotNull;
import java.io.Serializable;

@Data
public class Emp implements Serializable {
    private static final long serialVersionUID = 281903912367009575L;

    @NotNull(message = "id 不能为 null")
    private Integer id;

    @NotNull(message = "name 不能为 null")
    private String name;
    
    private Double salary;
    
    private Integer age;
    
    private String email;
}	
```

### 第二步：修改 Controller 方法，使用 @Valid 注解标记需要检测的数据

当检测到数据异常时，会抛出异常。

```java
@RestController
@RequestMapping("api")
public class EmpController {
    @Resource
    private EmpService empService;

    @PostMapping("/emp")
    public Result createEmp(@Valid @RequestBody Emp emp) {
        return Result.ok().data("items", emp).message("数据插入成功");
    }
}
```

这里抛出的异常可能不是我们希望看到的异常格式，怎么办呢？

### 第三步：使用 BindingResult 去处理捕获到的数据并进行相关处理

```java
@RestController
@RequestMapping("api")
public class EmpController {
    @Resource
    private EmpService empService;

    @PostMapping("/emp")
    public Result createEmp(@Valid @RequestBody Emp emp, BindingResult result) {
        if (result.hasErrors()) {
            Map<String, String> map = new HashMap<>();
            // 获取校验结果，遍历获取捕获到的每个校验结果
            result.getFieldErrors().forEach(item ->{
                // 获取校验的信息
                String message = item.getDefaultMessage();
                String field = item.getField();
                // 存储得到的校验结果
                map.put(field, message);
            });
            return Result.error().message("数据不合法").data("items", map);
        }
        return Result.ok().data("items", emp).message("数据插入成功");
    }
}
```

### 第四步：统一异常处理

通过上面的步骤，已经可以捕获异常、处理异常。

但是每次都是在业务方法中手动处理逻辑，这样的实现，代码肯定会冗余。

可以将其抽出，使用**统一异常处理**。

需要使用 **@RestControllerAdvice** 与 **@ExceptionHandler**。

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    private Logger logger = LoggerFactory.getLogger(getClass());

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Result handlerValidException(MethodArgumentNotValidException e) {
        logger.error(e.getMessage(), e);
        BindingResult result = e.getBindingResult();
        Map<String, String> map = new HashMap<>();
        // 获取校验结果，遍历获取捕获到的每个校验结果
        result.getFieldErrors().forEach(item ->{
            // 存储得到的校验结果
            map.put(item.getField(), item.getDefaultMessage());
        });
        return Result.error().message("数据校验不合法").data("items", map);
    }
}
```

这时就不需要再用 BindingResult 去处理数据了。

```java
@RestController
@RequestMapping("api")
public class EmpController {
    @Resource
    private EmpService empService;

    @PostMapping("/emp")
    public Result createEmp(@Valid @RequestBody Emp emp) {
        return Result.ok().data("items", emp).message("数据插入成功");
    }
}
```



## 3. JSR 303 分组校验

**为什么使用分组校验？**

通过上面的过程，可以了解到单个方法的校验规则。 

如果出现多个方法，都需要校验 Bean，且校验规则不同的时候，怎么办呢？

分组校验就可以去解决该问题.

**每个分组指定不同的校验规则，不同的方法执行不同的分组**，就可以得到不同的校验结果。



### 第一步：定义空接口，用于指定分组，内部不需要任何实现

```JAVA
// 用于指定 添加数据 时的校验规则
public interface AddGroup{
    
}
// 用于指定 修改数据 时的校验规则
public interface UpdateGroup{
    
}
```



### 第二步：给 Bean 添加注解，并指定分组信息

```java
@Data
public class Emp implements Serializable {
    private static final long serialVersionUID = 281903912367009575L;

    @NotNull(message = "id 不能为 null", groups = {AddGroup.class})
    private Integer id;

    @NotNull(message = "name 不能为 null", groups = {AddGroup.class, UpdateGroup.class})
    private String name;

    private Double salary;

    private Integer age;

    private String email;
}
```

### 第三步：在业务方法上，通过 @Validated 注解指定分组，去指定校验

```java
@RestController
@RequestMapping("api")
public class EmpController {
    @Resource
    private EmpService empService;

    @PostMapping("/emp")
    public Result createEmp(@Validated({AddGroup.class}) @RequestBody Emp emp) {
        return Result.ok().data("items", emp).message("数据插入成功");
    }

    @PutMapping("/emp")
    public Result UpdateEmp(@Validated({UpdateGroup.class}) @RequestBody Emp emp) {
        return Result.ok().data("items", emp).message("数据插入成功");
    }
}
```



## 4. JSR 303 自定义校验注解

**为什么使用自定义校验注解？**

上面的**注解满足不了业务需求**时，可以自定义校验注解，自定义校验规则。

### 第一步：自定义一个校验注解

自定义一个校验注解，@TestValid，用于判断一个值的长度是否合法。

```java
import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.Documented;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

/**
 * 用于判断一个值的长度是否合法
 */
@Target({FIELD})
@Retention(RUNTIME)
@Documented
@Constraint(validatedBy = {TestValidConstraintValidator.class})
public @interface TestValid {
    String message() default "{com.lyh.test.TestValid.message}";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };

    /**
     * 返回一个长度
     * @return 默认为 5
     */
    int length() default 5;
}
```

其中的 message 错误信息可以从 ValidationMessages.properties 配置文件中获取。

```properties
com.lyh.test.TestValid.message="值不能为bull,且长度不超过5"
```



### 第二步：自定义一个校验器，即自定义校验规则

```java
import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

/**
 * 实现 ConstraintValidator 接口，
 * 其中 ConstraintValidator 的泛型，一个需要指定自定义的注解，一个需要指定需要获取的值的类型。
 * 比如：
 *  ConstraintValidator<TestValid, String> 中
 *      TestValid   表示自定义注解
 *      String      表示获取的值的类型
 * 即定义规则，判断一个 String 的值的长度是否满足条件
 */
public class TestValidConstraintValidator implements ConstraintValidator<TestValid, String> {

    /**
     * 用于保存自定义的（默认）长度
     */
    private int length;

    /**
     * 初始化方法，获取默认数据
     * @param constraintAnnotation 注解对象
     */
    @Override
    public void initialize(TestValid constraintAnnotation) {
        length = constraintAnnotation.length();
    }

    /**
     * 自定义校验规则，如果 String 为 Null 或者 长度大于 5，则校验失败（返回 false）
     * @param value 需要校验的值
     * @param context
     * @return true 表示校验成功，false 表示校验失败
     */
    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        return value == null ? false : length > value.length();
    }

}
```



### 第三步：使用注解

```java
@Data
public class Emp implements Serializable {
    private static final long serialVersionUID = 281903912367009575L;

    @NotNull(message = "id 不能为 null", groups = {AddGroup.class})
    private Integer id;

    @TestValid(groups = {AddGroup.class})
    @NotNull(message = "name 不能为 null", groups = {AddGroup.class, UpdateGroup.class})
    private String name;

    private Double salary;

    private Integer age;

    @TestValid(length = 10, message = "值不能为 Null 且长度不超过 10", groups = {AddGroup.class})
    private String email;
}
```



# 三、JSR 303 相关注解

## 1. 空检查相关注解

```
注解                     注解详情
@Null                  被指定的注解元素必须为 Null
@NotNull               任意类型，不能为 Null，但可以为空，比如空数组、空字符串。
@NotBlank              针对字符串，不能为 Null，且去除前后空格后的字符串长度要大于 0。
@NotEmpty              针对字符串、集合、数组，不能为 Null，且长度要大于 0。	
```

## 2. 长度检查

```
注解                      注解详情
@Size                   针对字符串、集合、数组，判断长度是否在给定范围内。
@Length                 针对字符串，判断长度是否在给定范围内。
```

## 3. 布尔值检查

```
注解                      注解详情
@AssertTrue             针对布尔值，用来判断布尔值是否为 true
@AssertFalse            针对布尔值，用来判断布尔值是否为 false
```

## 4. 日期检查

```
注解                     注解详情
@Past                  针对日期，用来判断当前日期是否为 过去的日期
@Future                针对日期，用来判断当前日期是否为 未来的日期
```

## 5. 数值检查

```
注解                  注解详情
@Max(value)         针对字符串、数值，用来判断是否小于等于某个指定值
@Min(value)         针对字符串、数值，用来判断是否大于等于某个指定值
```

## 6. 其他

```java
注解                   注解详情
@Pattern             验证字符串是否满足正则表达式
@Email               验证字符串是否满足邮件格式
@Url                 验证是否满足 url 格式
```