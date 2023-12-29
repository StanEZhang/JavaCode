# 什么是反射
在说反射概念之前，我们先说另外2个概念：**编译期**和**运行期**。

编译期：

   - 编译期是源代码从文本形式转换为字节码的过程，这发生在Java代码被JVM执行之前。
   - 在编译期，编译器对源代码进行语法检查、类型检查、变量名解析等操作，确保代码符合Java的语法规则，并将其编译成字节码（.class文件）。
   - 编译期间的操作基于静态类型信息。编译器只能使用它在编译时了解的信息，而不能知晓运行时的具体情况。

运行期：

   - 运行期是指编译后的代码在Java虚拟机（JVM）上执行的过程。
   - 在运行期，JVM执行编译后的字节码，并进行各种运行时操作，如内存分配、垃圾回收等。

**反射机制**主要发生在**运行期**。反射允许程序在运行时动态访问和操作类、对象、方法、属性等。

使用反射，程序可以获取类的信息（如类名、方法、字段等），并可以创建对象、调用方法、修改字段值，这些都是在运行时发生的。

补充：在学习**JVM内存结构**时，我们知道在类信息是存放在**方法区**的。

# 反射的优缺点

**优点：**

在运行时获得类的各种内容，进行反编译，对于Java这种先编译再运行的语言，能够让我们很方便的创建灵活的代码，这些代码可以在运行时装配，无需在组件之间进行源代码的链接，更加容易实现面向对象。

**缺点：**

（1）反射会消耗一定的系统资源，因此，如果不需要动态地创建一个对象，那么就不需要用反射；

（2）反射调用方法时可以忽略权限检查，因此可能会破坏封装性而导致安全问题。

# 反射的入口：Class类
>  
类 Class 的实例代表在运行中的 Java 应用程序中的类和接口。枚举是一种类，注解是一种接口。每个数组也属于一个类，这个类以 Class 对象的形式反映出来，这个 Class 对象被所有具有相同元素类型和维数的数组共享。Java 的基本类型（boolean、byte、char、short、int、long、float 和 double）以及关键字 void 也表示为 Class 对象。

## 三种方式获取Class对象

- 通过实例对象获取Class对象
- 通过类名.class获取Class对象
- 通过Class.forName(String className)获取Class对象
```java
@SpringBootTest
class DemoReflectionApplicationTests {

    @Test
    void contextLoads() throws ClassNotFoundException {
        User user = new User();
        Class<? extends User> u1 = user.getClass();
        System.out.println(u1.getName());
        System.out.println(u1.hashCode());

        Class<User> u2 = User.class;
        System.out.println(u2.hashCode());

        Class<?> u3 = Class.forName("com.example.reflection.pojo.User");
        System.out.println(u3.hashCode());
    }

}
```
````java
com.example.reflection.pojo.User
1970707120
1970707120
1970707120
````

在运行期间，一个类只有一个Class对象产生。

## 获取到Class对象，我们能拿到哪些信息？
### 获取名称信息
```java
// 返回Java内部使用的真正的名称
public String getName();
// 返回的名称不带包信息
public String getSimpleName();
// 返回的名称更加友好
public String getCanonicalName();
// 返回包信息
public String getPackage();
```
```java
@Test
void contextLoad2() throws ClassNotFoundException {
    Class<?> u = Class.forName("com.example.reflection.pojo.User");
    System.out.println(u.getName());
    System.out.println(u.getSimpleName());
    System.out.println(u.getCanonicalName());
    System.out.println(u.getPackage());

    Class<String> s = String.class;
    System.out.println(s.getName());
    System.out.println(s.getSimpleName());
    System.out.println(s.getCanonicalName());
    System.out.println(s.getPackage());
}
```
结果：
```
com.example.reflection.pojo.User
User
com.example.reflection.pojo.User
package com.example.reflection.pojo
java.lang.String
String
java.lang.String
package java.lang, Java Platform API Specification, version 1.8
```
![[Pasted image 20231229124420.png]]
### 获取字段信息
Class获取字段信息的方法：
```java
// 返回所有的public字段，包括其父类的，如果没有字段，返回空数组
public Field[] getFields()
// 返回本类声明的所有字段，包括非public的，但不包括父类的
public Field[] getDeclaredFields()
// 返回本类或父类中指定名称的public字段，找不到抛出异常NoSuchFieldException
public Field getField(String name)
// 返回本类中声明的指定名称的字段，找不到抛出异常NoSuchFieldException
public Field getDeclaredField(String name)
```

获取到Field以后，进一步获取字段信息：
```java
// 获取字段的名称
public String getName()
// 判断当前程序是否有该字段的访问权限
public boolean isAccessible()
// flag设为true表示忽略Java的访问检查机制，以允许读写非public的字段
public void setAccessible(boolean flag)
// 获取指定对象obj中该字段的值
public Object get(Object obj)
// 将指定对象obj中该字段的值设为value
public void set(Object obj, Object value)

// 返回字段的修饰符
public int getModifiers()
//返回字段的类型
public Class<? > getType()
//查询字段的注解信息，下一章介绍注解
public <T extends Annotation> T getAnnotation(Class<T> annotationClass)
public Annotation[] getDeclaredAnnotations()
```
### 获取方法信息
类中定义的**静态方法**和**实例方法**都算在内，用类Method表示。
```java
//返回所有的public方法，包括其父类的，如果没有方法，返回空数组
public Method[] getMethods()
//返回本类声明的所有方法，包括非public的，但不包括父类的
public Method[] getDeclaredMethods()
//返回本类或父类中指定名称和参数类型的public方法，
//找不到抛出异常NoSuchMethodException
public Method getMethod(String name, Class<? >... parameterTypes)
//返回本类中声明的指定名称和参数类型的方法，找不到抛出异常NoSuchMethodException
public Method getDeclaredMethod(String name, Class<? >... parameterTypes)
```

获取到Method后，进一步获取方法的详细信息：
```java
//获取方法的名称
public String getName()
//flag设为true表示忽略Java的访问检查机制，以允许调用非public的方法
public void setAccessible(boolean flag)
//在指定对象obj上调用Method代表的方法，传递的参数列表为args
public Object invoke(Object obj, Object... args) throws IllegalAccessException, IllegalArgumentException, InvocationTargetException
```
### 创建对象和构造方法
Class直接创建对象，调用无参构造器。
```java
public T newInstance()
```

Class获取构造器:
```java
//获取所有的public构造方法，返回值可能为长度为0的空数组
public Constructor<? >[] getConstructors()
//获取所有的构造方法，包括非public的
public Constructor<? >[] getDeclaredConstructors()
//获取指定参数类型的public构造方法，没找到抛出异常NoSuchMethodException
public Constructor<T> getConstructor(Class<? >... parameterTypes)
//获取指定参数类型的构造方法，包括非public的，没找到抛出异常NoSuchMethodException
public Constructor<T> getDeclaredConstructor(Class<? >... parameterTypes)
```
获取到构造器用构造器创建对象：
```java
public T newInstance(Object ... initargs)
```
### 类型检查和转换
类型检查：
```java
/**
* Params:
* obj – the object to check
* Returns:
* true if obj is an instance of this class
*/
public native boolean isInstance(Object obj);
```
类型转换：
```java
// 将一个对象转换为由此 Class 对象所代表的类或接口。
public T cast(Object obj) 
```
### 获取Class的类型信息
Class代表的类型既可以是普通的类，也可以是内部类，还可以是基本类型、数组等，对于一个给定的Class对象，它到底是什么类型呢？可以通过以下方法进行检查：
```java
public native boolean isArray()  //是否是数组
public native boolean isPrimitive()  //是否是基本类型
public native boolean isInterface()  //是否是接口
public boolean isEnum()  //是否是枚举
public boolean isAnnotation()  //是否是注解
public boolean isAnonymousClass()  //是否是匿名内部类
public boolean isMemberClass()  //是否是成员类，成员类定义在方法外，不是匿名类
public boolean isLocalClass()  //是否是本地类，本地类定义在方法内，不是匿名类
```
### 获取类的声明信息
```java
//获取修饰符，返回值可通过Modifier类进行解读
public native int getModifiers()
//获取父类，如果为Object，父类为null
public native Class<? super T> getSuperclass()
//对于类，为自己声明实现的所有接口，对于接口，为直接扩展的接口，不包括通过父类继承的
public native Class<? >[] getInterfaces();
//自己声明的注解
public Annotation[] getDeclaredAnnotations()
//所有的注解，包括继承得到的
public Annotation[] getAnnotations()
//获取或检查指定类型的注解，包括继承得到的
public <A extends Annotation> A getAnnotation(Class<A> annotationClass)
public boolean isAnnotationPresent(Class<? extends Annotation> annotationClass)
```

对于反射这一块内容的话，其实理解起来并不是很难，但确实是很重要，所以非常建议把代码全部敲一遍。

对于编程学习来说，Coding百遍，其义自见。

仍然是不变的硬道理。



以上就是关于反射的所有内容，感谢阅读！

---

联系我：
[https://haibin9527.gitee.io/about_me/](https://haibin9527.gitee.io/about_me/)