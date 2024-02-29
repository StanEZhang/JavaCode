大家好，我是阿bin。

日常开发中几乎所有项目都会用到常量参数，有时候图省事就用 static final 定义一些常量，但我们还有更优雅的方式！

今天就来学一下Java枚举类。

# 一、什么是枚举

**枚举**大概可以理解为穷举，就是把可数的集合元素一一列举出来。

Java 5.0引入了枚举，枚举限制变量只能是预先设定好的值。

使用枚举可以减少代码中的 bug，方便很多场景使用。

# 二、枚举的优势

枚举相比于通过`static final`定义的常量有哪些优势呢？

举个例子。

我们定义周一到周日这7个常量，可以用7个不同的`int`表示：

```java
public class Weekday {
    public static final int SUN = 0;
    public static final int MON = 1;
    public static final int TUE = 2;
    public static final int WED = 3;
    public static final int THU = 4;
    public static final int FRI = 5;
    public static final int SAT = 6;
}
```

使用常量的时候，可以这么引用：

```java
if (day == Weekday.SAT || day == Weekday.SUN) {
    // TODO: work at home
}
```

无论是`int`常量还是`String`常量，使用这些常量来表示一组枚举值的时候，有一个严重的问题就是：

**编译器无法检查每个值的合理性。**

例如：

```java
if (weekday == 6 || weekday == 7) {
    if (tasks == Weekday.MON) {
        // TODO:
    }
}
```

上述代码编译和运行均不会报错，但存在两个问题：

- 注意到`Weekday`定义的常量范围是`0`~`6`，并不包含`7`，编译器无法检查不在枚举中的`int`值；
- 定义的常量仍可与其他变量比较，但其用途并非是枚举星期值。

---

和`int`定义的常量相比，使用`enum`定义枚举有如下好处：

首先，`enum`常量本身带有类型信息，即`Weekday.SUN`类型是`Weekday`，编译器会自动检查出类型错误。例如，下面的语句不可能编译通过：

```java
int day = 1;
if (day == Weekday.SUN) { // Compile error: bad operand types for binary operator '=='
}
```

其次，不可能引用到非枚举的值，因为无法通过编译。

最后，不同类型的枚举不能互相比较或者赋值，因为类型不符。

例如，不能给一个`Weekday`枚举类型的变量赋值为`Color`枚举类型的值：

```java
Weekday x = Weekday.SUN; // ok!
Weekday y = Color.RED; // Compile error: incompatible types
```

这就使得编译器可以在编译期自动检查出所有可能的潜在错误。

# 三、Java枚举的语法

**枚举类中的声明** 

```java
访问修辞符 enum 枚举名 {
    枚举成员,
    枚举成员,
    ...
};
```

**class类中枚举的声明** 

```java
访问修饰符 class 类名 {
    enum 枚举名 {
        枚举成员,
        枚举成员,
        ...
    }
}
```



# 四、使用规则

- **类的对象是确定的有限个数。**  
- **枚举类不能被继承** 
- **不能通过new关键字创建枚举类对象** 
- **枚举类中的枚举成员是用`,`隔开的，最后以`;`结束。** 



# 五、应用场景

- **星期：** Monday（星期一）、Tuesday（星期二）、Wednesday（星期三）、Thursday（星期四）、Firday（星期五）、Saturday（星期六）、Sunday（星期日）
- **性别：** Man（男）、Woman（女）
- **季节：** Spring（春天）、Summer（夏天）、Autumn（秋天）、Winter（冬天）
- **支付方式：** Cash（现金）、WeChatPay（微信）、Alipay（支付宝）、BankCard（银行卡）、CreditCard（信用卡）
- **订单状态：** Nonpayment（未付款）、Paid（已付款）、Fulfilled（已配货）、Delivered（已发货）、Return（退货）、Checked（已确认）
- **线程状态：** Establish（创建）、Ready（就绪）、Run（运行）、Obstruct（阻塞）、Die（死亡）
- 等等......



# 六、使用步骤

我们以奶茶的大、中、小杯为三种杯型创建枚举类。

```java
/**
 * 为珍珠奶茶添加三个杯型：大、中、小
 */
public enum PearlMilkTea {
    SMALL, MEDIUM, LARGE
}
```

其次，创建珍珠奶茶对象，再有方法来判断枚举类中的大、中、小杯。

```java
public class PearlMilkTeaTest {
    public static void main(String[] args) {
        //创建大杯的珍珠奶茶对象
        PearlMilkTea pearlMilkTea = PearlMilkTea.LARGE;
        PearlMilkTeaTest.drinkSize(pearlMilkTea);
    }

    public static void drinkSize(PearlMilkTea pearlMilkTea) {
        if (pearlMilkTea == PearlMilkTea.LARGE) {
            System.out.println("买了大杯珍珠奶茶！");
        } else if (pearlMilkTea == PearlMilkTea.MEDIUM) {
            System.out.println("买了中杯珍珠奶茶！");
        } else {
            System.out.println("买了小杯珍珠奶茶！");
        }
    }
}
```



# 七、有参枚举类

```java
public enum Season {
    SPRING("春天"),
    SUMMER("夏天"),
    AUTUMN("秋天"),
    WINTER("冬天");

    private final String seasonName;

    Season1(String seasonName) {
        this.seasonName = seasonName;
    }

    public String getSeasonName() {
        return seasonName;
    }
}
```

创建了该枚举类的测试类：

```java
public class SeasonTest {
    public static void main(String[] args) {
        Season1 spring = Season.SPRING;
        System.out.println(spring);                     //SPRING
        System.out.println(spring.getSeasonName());     //春天
    }
}
```



# 八、常用方法

## 8.1 name()和toString()

name()就是根据枚举成员来获取该枚举成员的字符串名称。而同String方法也是用来获取枚举成员的字符串名称。

```java
System.out.println(Season.SUMMER.name());			//SUMMER
System.out.println(Season.SUMMER.toString());		//SUMMER
```



## 8.2 valueOf()

根据枚举成员名称获取枚举成员。

```
System.out.println(Season.valueOf("WINTER"));			//WINTER
System.out.println(Season.valueOf("WIN"));				//java.lang.IllegalArgumentException
```



## 8.3 values

获取枚举成员的所有值，这些值并以数组的形式存储。

```java
Season[] seasons = Season.values();
for (Season season : seasons) {
    System.out.print(season + " ");
}
```

**结果为：** 

```java
SPRING SUMMER AUTUMN WINTER 
```



## 8.4 ordinal

该方法是获取枚举成员的序数，其第一个枚举成员位置为0。

```java
//获取指定枚举成员的次序
System.out.println(Season.SUMMER.ordinal());

//获取所有成员的次序
Season[] seasons = Season.values();
for (Season s : seasons) {
    System.out.println(s + " -> " + s.ordinal());
}
```



## 8.5 compareTo

比较两个枚举成员的次序数，并返回次序相减后的结果。

```java
System.out.println(Season.SUMMER.compareTo(Season.WINTER));			//SUMMER为1，WINTER为3，返回-2
```

# 九、高级特性

- 常量可以放在枚举容器中
- 支持 switch 判断
- 可以根据场景定义多个参数和方法
- 可以实现接口
- 可以使用接口对枚举分类