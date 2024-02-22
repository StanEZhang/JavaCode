顾名思义，`classpath`就是`class`的`path`，也就是类文件(`*.class`的路径)。

一谈到文件的路径，我们就很有必要了解一个`java`项目（通常也是`web`项目）它在真正运行时候，这个项目内部的目录、文件的结构。

这样，我们才好分析、理解`classpath`。

# 开发时期的web项目结构
下面，我以一个`ssm`的项目为例，我先把开发时候的项目的目录结构图放出来。根据`maven`的约定，一般我们的项目结构就像下面这样。
![[Pasted image 20240222150955.png]]

# classpath用在哪里了
我们经常用到`classpath`的地方，就是在指定一些配置/资源文件的时候会使用到。

比如说，当我们把`*Mapper.xml`文件放在了`main/java/../mapping/`文件夹下时，在 mybatis 的配置文件中配置其位置，我们使用：

```properties
classpath*:**/mapper/mapping/*Mapper.xml
```
上面classpath的配置，是为了告诉配置文件，去哪里寻找我们要指定的配置文件。

要想弄清楚为什么是上面这样写的，我们就要来看看项目运行时（或者是发布后）的目录结构了。

# web项目发布后的目录结构
我们使用IDEA对项目进行打包，一种是war包，一种是explorer的文件夹，war包解压后就是explorer了。我们来对解压后的目录结构进行分析。

![[Pasted image 20240222151022.png]]

经过对比，我们要注意到，开发时期的项目里，`src/main/`下面的`java`和`resources`文件夹都被(编译)打包到了生产包的`WEB-INF/classes/`目录下；而原来`WEB-INF`下面的`views`和`web.xml`则仍然还是在`WEB-INF`下面。同时由maven引入的依赖都被放入到了WEB-INF/lib/下面。

**最后，编译后的class文件和资源文件都放在了classes目录下。**

![[Pasted image 20240222151106.png]]

# classpath原来是这个
在编译打包后的项目中，根目录是`META-INF`和`WEB-INF` 。

**这个时候，我们可以看到`classes`这个文件夹，它就是我们要找的`classpath`**。

**需要声明的一点是，使用`classpath:`这种前缀，就只能代表一个文件。**

`classpath*:**/mapper/mapping/*Mapper.xml`，使用`classpath*:`这种前缀，则可以代表多个匹配的文件；

`**/mapper/mapping/*Mapper.xml`，双星号**表示在任意目录下，也就是说在`WEB-INF/classes/`下任意层的目录，只要符合后面的文件路径，都会被作为资源文件找到。

---

文章来源：https://segmentfault.com/a/1190000015802324



