文章来源：[https://segmentfault.com/a/1190000015802324](https://segmentfault.com/a/1190000015802324)
顾名思义，classpath就是class的path，也就是类文件(*.class的路径)。一谈到文件的路径，我们就很有必要了解一个java项目（通常也是web项目）它在真正运行时候，这个项目内部的目录、文件的结构。这样，我们才好分析、理解classpath。
# 开发时期的web项目结构
下面，我以一个ssm的项目为例，我先把开发时候的项目的目录结构图放出来。根据maven的约定，一般我们的项目结构就像下面这样。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/25734432/1692688977218-a60f0300-a25e-425b-8340-bc13d8f68ef1.png#averageHue=%23909598&clientId=uf827cc7a-1981-4&from=paste&height=309&id=u526a3410&originHeight=425&originWidth=925&originalType=binary&ratio=1.375&rotation=0&showTitle=false&size=318712&status=done&style=none&taskId=u0c0ffc20-c112-40e4-ad22-406419c7d26&title=&width=672.7272727272727)
# classpath用在哪里了
我们经常用到classpath的地方，就是在指定一些配置/资源文件的时候会使用到。比如说，当我们把*Mapper.xml文件放在了main/java/../mapping/文件夹下时，在mybatis的配置文件中配置其位置，我们使用：
```properties
classpath*:**/mapper/mapping/*Mapper.xml
```
上面classpath的配置，是为了告诉配置文件，去哪里寻找我们要指定的配置文件。要想弄清楚为什么是上面这样写的，我们就要来看看项目运行时（或者是发布后）的目录结构了。
# web项目发布后的目录结构
我们使用IDEA对项目进行打包，一种是war包，一种是explorer的文件夹，war包解压后就是explorer了。我们来对解压后的目录结构进行分析。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/25734432/1692689188670-486c9367-1c8a-4968-a6e4-d27f72a5cf25.png#averageHue=%23888c8c&clientId=uf827cc7a-1981-4&from=paste&height=401&id=uae67ed03&originHeight=552&originWidth=921&originalType=binary&ratio=1.375&rotation=0&showTitle=false&size=383550&status=done&style=none&taskId=ue84d75b3-a466-4179-a9a4-8333018ea8f&title=&width=669.8181818181819)
经过对比，我们要注意到，开发时期的项目里，src/main/下面的java和resources文件夹都被(编译)打包到了生产包的WEB-INF/classes/目录下；而原来WEB-INF下面的views和web.xml则仍然还是在WEB-INF下面。同时由maven引入的依赖都被放入到了WEB-INF/lib/下面。**最后，编译后的class文件和资源文件都放在了classes目录下。**
![image.png](https://cdn.nlark.com/yuque/0/2023/png/25734432/1692689325036-0451871f-2f2f-45cf-ba43-ed49c0fffc33.png#averageHue=%239a9fa0&clientId=uf827cc7a-1981-4&from=paste&height=580&id=uf65ccca8&originHeight=798&originWidth=814&originalType=binary&ratio=1.375&rotation=0&showTitle=false&size=541149&status=done&style=none&taskId=u4738d8c2-58c5-4124-b56f-d53422193e9&title=&width=592)
# classpath原来是这个
在编译打包后的项目中，根目录是META-INF和WEB-INF 。**这个时候，我们可以看到classes这个文件夹，它就是我们要找的classpath**。
**需要声明的一点是，使用classpath:这种前缀，就只能代表一个文件。**
classpath*:**/mapper/mapping/*Mapper.xml，**使用classpath*:这种前缀，则可以代表多个匹配的文件**；**/mapper/mapping/*Mapper.xml，双星号**表示在任意目录下，也就是说在WEB-INF/classes/下任意层的目录，只要符合后面的文件路径，都会被作为资源文件找到。

---

联系我：[https://haibin9527.gitee.io/about_me/](https://haibin9527.gitee.io/about_me/)
