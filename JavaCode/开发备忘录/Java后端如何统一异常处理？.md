今天我们来学习一下项目开发中如何**统一异常处理**。

# 1. 为什么？

使用统一结果处理时，有些异常我们可以提前预知并处理。

但是一个**运行时异常**，我们不一定能预知并处理。

这时可以使用统一异常处理。

当异常发生时，触发该处理操作，从而保证程序的健壮性。

# 2. 怎么做？

使用 `@ControllerAdvice `或者 `@RestControllerAdvice` 注解作为统一异常处理的核心。

这两个注解都是 Spring MVC 提供的。作用于**控制层**的一种切面通知。

**注**：`@ControllerAdvice` 与 `@RestControllerAdvice` 区别？

```
@RestControllerAdvice 注解包含了 @ControllerAdvice 与 @ResponseBody 注解。
类似于 @Controller 与 @RestController 的区别。
@RestControllerAdvice 返回 json 数据时不需要添加 @ResponseBody 注解。
```

# 3. 代码实现？

## 第一步（可选）：自定义一个异常类

该异常类用于处理项目中的异常，并收集异常信息。

```java
import lombok.Data;
import org.apache.http.HttpStatus;

/**
 * 自定义异常，
 * 可以自定义 异常信息 message 以及 响应状态码 code（默认为 500）。
 *
 * 依赖信息说明：
 *      此处使用 @Data 注解，需导入 lombok 相关依赖文件。
 *      使用 HttpStatus 的常量表示 响应状态码，需导入 httpcore 相关依赖文件。
 */
@Data
public class GlobalException extends RuntimeException {
    /**
     * 保存异常信息
     */
    private String message;

    /**
     * 保存响应状态码
     */
    private Integer code = HttpStatus.SC_INTERNAL_SERVER_ERROR;

    /**
     * 默认构造方法，根据异常信息 构建一个异常实例对象
     * @param message 异常信息
     */
    public GlobalException(String message) {
        super(message);
        this.message = message;
    }

    /**
     * 根据异常信息、响应状态码构建 一个异常实例对象
     * @param message 异常信息
     * @param code 响应状态码
     */
    public GlobalException(String message, Integer code) {
        super(message);
        this.message = message;
        this.code = code;
    }

    /**
     * 根据异常信息，异常对象构建 一个异常实例对象
     * @param message 异常信息
     * @param e 异常对象
     */
    public GlobalException(String message, Throwable e) {
        super(message, e);
        this.message = message;
    }

    /**
     * 根据异常信息，响应状态码，异常对象构建 一个异常实例对象
     * @param message 异常信息
     * @param code 响应状态码
     * @param e 异常对象
     */
    public GlobalException(String message, Integer code, Throwable e) {
        super(message, e);
        this.message = message;
        this.code = code;
    }
}
```

## 第二步：定义一个全局的异常处理类

 定义一个全局的异常处理类 `GlobalExceptionHandler`。

使用 `@RestControllerAdvice` 注解标记这个类。

内部使用 `@ExceptionHandler` 注解去捕获异常。

```java
import com.lyh.common.util.Result;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

/**
 * 全局异常处理类。
 * 使用 slf4j 保存日志信息。
 * 此处使用了 统一结果处理 类 Result 用于包装异常信息。
 */
@RestControllerAdvice
public class GlobalExceptionHandler {
    private Logger logger = LoggerFactory.getLogger(getClass());

    /**
     * 处理 Exception 异常
     * @param e 异常
     * @return 处理结果
     */
    @ExceptionHandler(Exception.class)
    public Result handlerException(Exception e) {
        logger.error(e.getMessage(), e);
        return Result.error().message("系统异常");
    }

    /**
     * 处理空指针异常
     * @param e 异常
     * @return 处理结果
     */
    @ExceptionHandler(NullPointerException.class)
    public Result handlerNullPointerException(NullPointerException e) {
        logger.error(e.getMessage(), e);
        return Result.error().message("空指针异常");
    }

    /**
     * 处理自定义异常
     * @param e 异常
     * @return 处理结果
     */
    @ExceptionHandler(GlobalException.class)
    public Result handlerGlobalException(GlobalException e) {
        logger.error(e.getMessage(), e);
        return Result.error().message(e.getMessage()).code(e.getCode());
    }
}
```



## 第三步：使用

修改某个 `controller` 如下所示：

参数不存在时，抛出**空指针异常**。

参数为 `-1` 时，抛出**自定义异常**并处理。

查询结果为 `null` 时，抛出**自定义异常**并处理。

查询成功时，正确处理并返回。

```java
@GetMapping("selectOne")
public Result selectOne(Integer id) {
    Emp emp = this.empService.queryById(id);
    if (id == null) {
        throw new NullPointerException();
    }
    if (id == -1) {
        throw new GlobalException("参数异常", 400);
    }
    if (emp == null) {
        throw new GlobalException("未查询到结果，请确认输入是否正确");
    }
    return Result.ok().data("items", emp).message("查询成功");
}
```

结果：

①参数不存在时：

```json
{
    "success": false,
    "code": 500,
    "message": "空指针异常",
    "data": {}
}
```

②参数存在，但为 -1 时：

```json
{
    "success": false,
    "code": 400,
    "message": "参数异常",
    "data": {}
}
```

③参数存在，但查询结果为 null 时：

```json
{
    "success": false,
    "code": 500,
    "message": "未查询到结果，请确认输入是否正确",
    "data": {}
}
```

④参数存在，查询结果存在时：

```json
{
    "success": true,
    "code": 200,
    "message": "查询成功",
    "data": {
        "items": {
            "id": 1,
            "name": "tom",
            "salary": 6000.0,
            "age": 20,
            "email": "tom@163.com"
        }
    }
}
```