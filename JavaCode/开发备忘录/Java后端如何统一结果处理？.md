大家好，我是阿bin。

今天来研究一下后端如何封装统一的返回结果，给出代码示例，方便我们开发中的CV。

【表情包】

# 1. 为什么使用统一结果？

大部分前后端项目采用 **JSON** 格式进行数据交互。

定义一个统一的数据规范，有利于前后台的交互、以及信息处理。

# 2. 数据格式

- 是否响应成功（success： true / false）
- 响应状态码（code：200 / 400 / 500 等）
- 状态码描述（message：访问成功 / 系统异常等）
- 响应数据（data：处理的数据）

输出格式如下所示：

```json
{
  "success": true,
  "code": 200,
  "message": "查询用户列表",
  "data": {
    "itms": [
      {
        "id": "1",
        "username": "admin",
        "role": "ADMIN",
        "createTime": "2020-4-24T15:32:29",
        "modifiedTime": "2020-4-24T15:41:40"
      },{
        "id": "2",
        "username": "zhangsan",
        "role": "USER",
        "createTime": "2020-4-24T15:32:29",
        "modifiedTime": "2020-4-24T15:41:40"
      }
    ]
  }
}
```

# 3. 如何处理

success 设置成 Boolean 类型。

code 设置成 Integer 类型。　　

message 设置成 String 类型。

data 设置成 HashMap 类型。

构造器私有，且使用静态方法返回类对象。

采用链式调用（即方法返回对象为其本身，return this）。

# 4. 代码实现

## 第一步：创建一个统一结果处理类 Result

```java
import lombok.Data;
import org.apache.http.HttpStatus;

import java.util.HashMap;
import java.util.Map;

/**
 * 统一结果返回类。方法采用链式调用的写法（即返回类本身 return this）。
 * 构造器私有，不允许进行实例化，但提供静态方法 ok、error 返回一个实例。
 * 静态方法说明：
 *      ok     返回一个 成功操作 的结果（实例对象）。
 *      error  返回一个 失败操作 的结果（实例对象）。
 *
 * 普通方法说明：
 *      success      用于自定义响应是否成功
 *      code         用于自定义响应状态码
 *      message      用于自定义响应消息
 *      data         用于自定义响应数据
 *
 * 依赖信息说明：
 *      此处使用 @Data 注解，需导入 lombok 相关依赖文件。
 *      使用 HttpStatus 的常量表示 响应状态码，需导入 httpcore 相关依赖文件。
 */
@Data
public class Result {
    /**
     * 响应是否成功，true 为成功，false 为失败
     */
    private Boolean success;

    /**
     * 响应状态码， 200 成功，500 系统异常
     */
    private Integer code;

    /**
     * 响应消息
     */
    private String message;

    /**
     * 响应数据
     */
    private Map<String, Object> data = new HashMap<>();

    /**
     * 默认私有构造器
     */
    private Result(){}

    /**
     * 私有自定义构造器
     * @param success 响应是否成功
     * @param code 响应状态码
     * @param message 响应消息
     */
    private Result(Boolean success, Integer code, String message){
        this.success = success;
        this.code = code;
        this.message = message;
    }

    /**
     * 返回一个默认的 成功操作 的结果，默认响应状态码 200
     * @return 成功操作的实例对象
     */
    public static Result ok() {
        return new Result(true, HttpStatus.SC_OK, "success");
    }

    /**
     * 返回一个自定义 成功操作 的结果
     * @param success 响应是否成功
     * @param code 响应状态码
     * @param message 响应消息
     * @return 成功操作的实例对象
     */
    public static Result ok(Boolean success, Integer code, String message) {
        return new Result(success, code, message);
    }

    /**
     * 返回一个默认的 失败操作 的结果，默认响应状态码为 500
     * @return 失败操作的实例对象
     */
    public static Result error() {
        return new Result(false, HttpStatus.SC_INTERNAL_SERVER_ERROR, "error");
    }

    /**
     * 返回一个自定义 失败操作 的结果
     * @param success 响应是否成功
     * @param code 响应状态码
     * @param message 相应消息
     * @return 失败操作的实例对象
     */
    public static Result error(Boolean success, Integer code, String message) {
        return new Result(success, code, message);
    }

    /**
     * 自定义响应是否成功
     * @param success 响应是否成功
     * @return 当前实例对象
     */
    public Result success(Boolean success) {
        this.setSuccess(success);
        return this;
    }

    /**
     * 自定义响应状态码
     * @param code 响应状态码
     * @return 当前实例对象
     */
    public Result code(Integer code) {
        this.setCode(code);
        return this;
    }

    /**
     * 自定义响应消息
     * @param message 响应消息
     * @return 当前实例对象
     */
    public Result message(String message) {
        this.setMessage(message);
        return this;
    }

    /**
     * 自定义响应数据，一次设置一个 map 集合
     * @param map 响应数据
     * @return 当前实例对象
     */
    public Result data(Map<String, Object> map) {
        this.data.putAll(map);
        return this;
    }

    /**
     * 通用设置响应数据，一次设置一个 key - value 键值对
     * @param key 键
     * @param value 数据
     * @return 当前实例对象
     */
    public Result data(String key, Object value) {
        this.data.put(key, value);
        return this;
    }
}
```



## 第二步：依赖信息

```xml
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpcore</artifactId>
    <version>4.4.13</version>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.12</version>
    <scope>provided</scope>
</dependency>
```



## 第三步：HttpStatus 状态码

[[]]

 

## 第四步：使用

```java
@GetMapping("selectOne")
public Result selectOne(Integer id) {
    Emp emp = this.empService.queryById(id);
    if (emp == null) {
        return Result.error().message("用户不存在");
    }
    return Result.ok().data("items", emp).message("查询成功");
}
```

