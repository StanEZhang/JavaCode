# 一、什么是注解

定义：注解（Annotation），也叫元数据。一种代码级别的说明。

它是JDK1.5及以后版本引入的一个特性，与类、接口、枚举是在同一个层次。

它可以声明在包、类、字段、方法、局部变量、方法参数等的前面，用来对这些元素进行说明，注释。

# 二、内置注解

- **@Override：** 标记在成员方法上，用于标识当前方法是重写父类（父接口）方法，编译器在对该方法进行编译时会检查是否符合重写规则，如果不符合，编译报错。
- **@Deprecated：** 用于标记当前类、成员变量、成员方法或者构造方法过时如果开发者调用了被标记为过时的方法，编译器在编译期进行警告。
- **@SuppressWarnings：** 压制警告注解，可放置在类和方法上，该注解的作用是阻止编译器发出某些警告信息。

# 三、元注解

元注解即注解的注解。

在jdk的中java.lang.annotation包中定义了四个元注解。

## @Target

指定被修饰的注解的作用范围，如果不写默认是任何地方都可以使用。

可选的参数值在**枚举类ElemenetType**中包括：

```java
TYPE： 用在类,接口上
FIELD：用在成员变量上
METHOD： 用在方法上
PARAMETER：用在参数上
CONSTRUCTOR：用在构造方法上
LOCAL_VARIABLE：用在局部变量上
```

## @Retention

指定了被修饰的注解的生命周期。

可选的参数值在枚举类型RetentionPolicy中包括:

```java
SOURCE：注解只存在于Java源代码中，编译生成的字节码文件中就不存在了。
CLASS：注解存在于Java源代码、编译以后的字节码文件中，运行的时候内存中没有，默认值。
RUNTIME：注解存在于Java源代码中、编译以后的字节码文件中、运行时内存中，程序可以通过反射获取该注解。
```

正常开发中我们自定义注解的时候无脑用 **Runtime** 就可以了。

## @Documented

指定了被修饰的注解是可以Javadoc等工具文档化。

## @Inherited

指定了被修饰的注解修饰程序元素的时候是可以被子类继承的。

# 四、自定义注解

## 定义格式

```java
元注解
public @interface 注解名称{
	属性列表;
}
```

## 注解的属性

- **格式1：数据类型 属性名();**
- **格式2：数据类型 属性名() default 默认值;**

示例如下：

```java
public @interface Student {
  String name(); 
  int age() default 18; 
  String gender() default "男"; 
} 
```

**注意：**
没有默认值的属性是必填的，当属性只有一个，且属性名为 value 时，可以省略。

示例：

```java
public class Test {
    @Student("zhangsan")
    public void test(){

    }
}
public @interface Student {
  String value(); 
} 
```

## 反射解析注解

反射请移步：[[Java反射，看完就会用]]

如果反射懂了，注解懂了，那就不需要过多解释直接看示例就行了。

**自定义注解 Book**

```java
@Target({ElementType.METHOD,ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface Book {
    // 书名
    String value();
    // 价格
    double price() default 100;
    // 作者
    String[] authors();
}
```


**BookStore类**

```java
@Book(value = "红楼梦",authors = "曹雪芹",price = 998)
public class BookStore {
}
```


**TestAnnotation类**

```java
public class TestAnnotation {
    public static void main(String[] args)  throws Exception{
        System.out.println("---------获取类上注解的数据----------");
        test();
    }

    /**
     * 获取BookStore类上使用的Book注解数据
     */
    public static void test(){
        // 获得BookStore类对应的Class对象
        Class c = BookStore.class;
        // 判断BookStore类是否使用了Book注解
        if(c.isAnnotationPresent(Book.class)) {
            // 根据注解Class对象获取注解对象
            Book book = (Book) c.getAnnotation(Book.class);
            // 输出book注解属性值
            System.out.println("书名：" + book.value());
            System.out.println("价格：" + book.price());
            System.out.println("作者：" + Arrays.toString(book.authors()));
        }
    }
}
```

---

联系我：
https://stanezhang.github.io/