# 一、程序计数器
程序计数器内存很小，可以看作是**当前线程**所执行字节码的**行号指示器**。

有了它，程序就能被正确的执行。

因为有**线程切换**的存在，则每个线程必须有各自独立的程序计数器，即**线程私有**的内存。

这里再解释一下什么是**线程切换**，线程切换指的是：

单处理器在执行多线程时所进行的线程切换，多线程的交替运行会产生同时运行的错觉。

程序计数器不会发生OOM原因：

占用内存非常小，当线程结束时程序计数器也会随之回收。

# 二、本地方法栈与虚拟机栈
栈是stack的翻译，那stack又是什么？

在英文语境中，stack指的是一摞盘子堆叠起来、一摞书堆叠起来的这种状态，也就是 a stack of books. 借这种现实物理情境来描述计算机中的数据结构。

这种结构的特征就是LIFO, Last In First Out, 即后进先出。

也就是，一摞盘子，你只能一个一个往上堆，也只能一个一个从顶上往外取，对应入栈和出栈（弹栈）的操作。

以上是对栈这种结构的解释。

接下来说这两种栈结构：Native Method Stack 和 JVM stack.

栈是**线程私有**的，它的生命周期和线程是相同的。

栈里面保存**栈帧**。

什么是栈帧？

每个方法执行时都会创建一个栈帧。栈帧存储了**局部变量表、操作数栈、动态连接和方法出口**等信息。每个方法从调用到运行结束的过程，就**对应着一个栈帧在栈中入栈到出栈的过程**。

栈有可能出现什么异常？

**StackOverflowError和OutOfMemoryError**。

前者主要是深度递归和复杂嵌套方法调用造成。

后者的话发生在栈在进行动态扩展的时候，也就是说 jvm 实现中栈的大小此时是不固定的，因为**线程操作需要更多的栈空间而在申请内存的时候失败**就会抛出**OutOfMemoryError**错误。

JVM 中的栈包括 Java 虚拟机栈和本地方法栈。

两者的区别就是：

Java 虚拟机栈**为 JVM 执行 Java 方法**服务，本地方法栈则**为 JVM 使用到的 Native 方法**（比如C或C++）服务。

# 四、堆
Heap有什么特征？

- JVM管理的最大内存区域
- 线程共享
- 存放对象实例
- 内存空间可以物理上不连续
- **垃圾回收**的主要区域

关于垃圾回收的内容这里不展开讲了。
# 五、方法区
JVM规范把方法区描述为堆的一个逻辑部分，但有一个别名叫Non-Heap,即Heap中的Non-heap, 也是说明它和堆其实还是不一样的。

方法区有什么特征？

- 线程共享
- 存放已被虚拟机加载的**类信息、常量、静态变量、即时编译器编译后的代码**等数据
- 垃圾回收较少出现，甚至可选择不进行垃圾回收

方法区的垃圾回收主要针对**常量池的回收和对类的卸载**。

这里主要说一下**运行时常量池：**

Class文件中除了有类的版本、字段、方法、接口等描述信息外还有一项**常量池**（Constant Pool Table）,用于存放编译期生成的各种字面量和符号引用，**这部分内容**将在类加载后进入方法区的**运行时常量池**中存放。

这里出现了两个常量池，它们是不一样的，一个叫常量池，一个叫运行时常量池（Runtime Constant Pool）, 运行时常量池具备动态性，那怎么理解动态性呢？

就是说除了编译期产生的常量进入了常量池在**类加载**后又紧接着进入了运行时常量池以外，**运行期间新的常量**也会进入运行时常量池。

关于**类加载**的介绍我们再单独写一篇。

[《学一点关于JVM类加载的知识》](https://mp.weixin.qq.com/s/MIrZa5_B5rHIUA4EseKl7Q )

下面举一些例子把这里具体搞搞清楚。

**常量池有什么用 ？**

**优点：**常量池避免了频繁的创建和销毁对象而影响系统性能，其实现了对象的共享。

下面具体讲一下**字符串常量池（String常量池）：**

String 是由 final 修饰的类，是不可以被继承的。通常有两种方式来创建对象。

```java
//1、这种存在Heap中，每次new都会创建一个全新对象
String str = new String("abcd");
 
//2、这种是在栈上创建对象引用变量str,指向字符串常量池中的“abcd”(没有的话新建一个)
String str = "abcd";
```
**关于字符串 + 号连接问题：**

对于字符串常量的 + 号连接，在程序编译期，JVM就会将其优化为 + 号连接后的值。**所以在编译期其字符串常量的值就确定了。**

```java
String a = "a1";   
String b = "a" + 1;   
System.out.println((a == b)); //result = true  
 
String a = "atrue";   
String b = "a" + "true";   
System.out.println((a == b)); //result = true 
 
String a = "a3.4";   
String b = "a" + 3.4;   
System.out.println((a == b)); //result = true 
```
**关于字符串引用 + 号连接问题：

对于字符串**引用**的 + 号连接问题，由于字符串引用在**编译期是无法确定**下来的，在程序的**运行期动态分配并创建新的地址存储对象**。

```java
public static void main(String[] args){
       String str1 = "a";
	   String str2 = "ab";
	   String str3 = str1 + "b";
	   System.out.print(str2 == str3);//false
    }
```
通过 jad 反编译工具，分析上述代码到底做了什么。
```java
public class TestDemo{
    public TestDemo(){
    }
    public static void main(String args[]){
        String s = "a";
        String s1 = "ab";
        String s2 = (new StringBuilder()).append(s).append("b").toString();
        System.out.print(s1 = s2);
    }
}
```
发现 **new 了一个 StringBuilder 对象，然后使用 append 方法优化了 + 操作符**。new 在**堆**上创建对象，而 String s1=“ab”则是在**常量池**中创建对象，两个应用所指向的内存地址是不同的，所以 s1 == s2 结果为 false。

这里引出一个实际开发中关于字符串拼接的问题。就是**尽量不要在 for 循环中使用 + 号来操作字符串**。

因为如果用“+”号的话，每次循环都会创建和销毁一个StringBuilder对象，这样还不如在循环外创建一个StringBuilder对象，然后使用append方法。

```java
public static void main(String[] args){
        StringBuilder s = new StringBuilder();
        for(int i = 0; i < 100; i++){
            s.append("a");
        }
    }
```
**使用final修饰的字符串**
```java
public static void main(String[] args){
        final String str1 = "a";
        String str2 = "ab";
        String str3 = str1 + "b";
        System.out.print(str2 == str3);//true
    }
```
final 修饰的变量是一个常量，编译期就能确定其值。所以 str1 + "b"就等同于 "a" + "b"，所以结果是 true。

**String对象的intern方法。**

```java
public static void main(String[] args){
        String s = "ab";
        String s1 = "a";
        String s2 = "b";
        String s3 = s1 + s2;
        System.out.println(s3 == s);//false
        System.out.println(s3.intern() == s);//true
    }
```
通过前面学习我们知道，s1+s2 实际上在堆上 new 了一个 StringBuilder 对象，而 s 在常量池中创建对象 “ab”，所以 s3 == s 为 false。

但是 s3 调用 intern 方法，返回的是s3的内容（ab）在常量池中的地址值。所以 s3.intern() == s 结果为 true。

