# 一、java锁存在的必要性
要认识java锁，就必须对2个前置概念有一个深刻的理解：**多线程**和**共享资源**。

对于程序来说，数据就是资源。

在单个线程操作数据时，或快或慢不存在什么问题，一个人你爱干什么干什么。

多个线程操作各自操作不同的数据，各干各的，也不存在什么问题。

多个线程对共享数据进行读取操作，我就四处看看，什么也不动，也不存在什么问题。

但如果**多个线程**对**共享数据**进行**写**操作，问题就来了。

经典库存问题：

mysql 记录剩余：1，redis 缓存记录剩余：1。

小明上网下单，后台程序检查 redis 记录存货剩 1 台，数据库执行 -1，但小明网太卡了，数据库刚执行完 -1，redis没来得及更新成0，小红的华为5G直接下单，redis剩1台，数据库-1，redis-1，下单成功一气呵成。结果就是2个人买了同一台手机。

这种业务场景可以说比比皆是，所以要解决这种数据同步问题就要有对应的办法，所以发明了java锁这个工具来保证数据的一致性，举个例子：

在一个不分男女的公共厕所中上一把锁，有人进去，把门锁住，上完出来，把锁打开，以此类推。



# 二、2个重要的java锁

## synchronized关键字

synchronized关键字是java开发人员最常用的给共享资源上锁的方式，也基本可以满足一般的进程同步要求，使用 synchronized 无需手动执行加锁和释放锁的操作，只需在需要同步的**代码块、普通方法、静态方法**上加入该关键字即可，JVM 层面会帮我们自动的进行加锁和释放锁的操作。

### 修饰普通方法

```java
/**
 * synchronized 修饰普通方法
 */
public synchronized void method() {
    // ....
}
```
当 synchronized 修饰普通方法时，被修饰的方法被称为同步方法，其作用范围是整个方法，作用的对象是调用这个方法的对象。

### 修饰静态方法
```java
/**
 * synchronized 修饰静态方法
 */
public static synchronized void staticMethod() {
    // .......
}
```
当 synchronized 修饰静态方法时，其作用范围是整个程序，这个锁对于所有调用这个锁的对象都是互斥的。

### 修饰普通方法 VS 修饰静态方法
创建一个类，其中有synchronized修饰的普通方法和synchronized修饰的静态方法。
```java
public class SynchronizedUsage {
    /**
     * synchronized 修饰普通方法
     */
    public synchronized void method() {
        System.out.println("普通方法执行时间：" + LocalDateTime.now());
        try {
            // 休眠 3s
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    /**
     * synchronized 修饰静态方法
     */
    public static synchronized void staticMethod() {
        System.out.println("静态方法执行时间：" + LocalDateTime.now());
        try {
            // 休眠 3s
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```

测试
```java
public class Test01 {
    /**
     * 创建线程池同时执行任务
     */
    static ExecutorService threadPool = Executors.newFixedThreadPool(10);

    public static void main(String[] args) {

        // 执行两次静态方法
        threadPool.execute(() -> {
            SynchronizedUsage.staticMethod();
        });
        threadPool.execute(() -> {
            SynchronizedUsage.staticMethod();
        });

        // 执行两次普通方法
        threadPool.execute(() -> {
            SynchronizedUsage usage = new SynchronizedUsage();
            usage.method();
        });
        threadPool.execute(() -> {
            SynchronizedUsage usage2 = new SynchronizedUsage();
            usage2.method();
        });
    }
}
```

结果

![[Pasted image 20240206165337.png]]

说明：

普通方法的2次调用归属于不同的对象，也就是不同的锁，所以执行的时候互不影响。

静态方法的2次调用归属于同一个类，也就是相同的锁，所以分先后执行，间隔3s。



### 修饰代码块
我们在日常开发中，最常用的是给代码块加锁，而不是给方法加锁，因为给方法加锁，相当于给整个方法全部加锁，这样的话锁的粒度就太大了，程序的执行性能就会受到影响，所以通常情况下，我们会使用 synchronized 给代码块加锁，它的实现语法如下：

```java
public void classMethod() throws InterruptedException {
    // 前置代码...
    
    // 加锁代码
    synchronized (SynchronizedUsage.class) {
        // ......
    }
    
    // 后置代码...
}

```

从上述代码我们可以看出，相比于修饰方法，修饰代码块需要自己手动指定加锁对象，加锁的对象通常使用 this 或 xxx.class 这样的形式来表示，比如以下代码：
```java
// 加锁某个类
synchronized (SynchronizedUsage.class) {
    // ......
}

// 加锁当前类对象
synchronized (this) {
    // ......
}

```

以上2中加锁方式类似于上文中普通方法与静态方法的区别，加锁当前类对象this只作用于当前对象，对象不同则锁不同，加锁某个类则作用于该类，同属于一个类的对象使用同一把锁。

创建一个类
```java
public class SynchronizedUsageBlock {
    /**
     * synchronized(this) 加锁
     */
    public void thisMethod() {
        synchronized (this) {
            System.out.println("synchronized(this) 加锁：" + LocalDateTime.now());
            try {
                // 休眠 3s
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * synchronized(xxx.class) 加锁
     */
    public void classMethod() {
        synchronized (SynchronizedUsageBlock.class) {
            System.out.println("synchronized(xxx.class) 加锁：" + LocalDateTime.now());
            try {
                // 休眠 3s
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

测试
```java
public class Test02 {
    public static void main(String[] args) {
        // 创建线程池同时执行任务
        ExecutorService threadPool = Executors.newFixedThreadPool(10);

        // 执行两次 synchronized(this)
        threadPool.execute(() -> {
            SynchronizedUsageBlock usage = new SynchronizedUsageBlock();
            usage.thisMethod();
        });
        threadPool.execute(() -> {
            SynchronizedUsageBlock usage2 = new SynchronizedUsageBlock();
            usage2.thisMethod();
        });

        // 执行两次 synchronized(xxx.class)
        threadPool.execute(() -> {
            SynchronizedUsageBlock usage3 = new SynchronizedUsageBlock();
            usage3.classMethod();
        });
        threadPool.execute(() -> {
            SynchronizedUsageBlock usage4 = new SynchronizedUsageBlock();
            usage4.classMethod();
        });
    }
}
```

结果

![[Pasted image 20240206165354.png]]

## Lock接口
Lock接口及其相关的实现类是在JDK 1.8之后在并发包中新增的，最常用且常见的就是ReentrantLock。与synchronized不同的是，ReentrantLock在使用时需要显式的获取和释放锁。

虽然它缺少了隐式获取释放锁的便捷性，但是却拥有了锁获取与释放的可操作性、**可中断的获取锁**以及**超时获取锁**等多种synchronized关键字所不具备的同步特性。

**Lock接口提供的synchronized不具备的特性**

![[Pasted image 20240206165407.png]]

**Lock接口中定义的方法**

![[Pasted image 20240206165417.png]] 

尽管java实现的锁机制有很多种，并且有些锁机制性能也比synchronized高，但还是强烈推荐在多线程应用程序中使用该关键字，因为实现方便，后续工作由jvm来完成，可靠性高。只有在确定锁机制是当前多线程程序的性能瓶颈时，才考虑使用其他机制，如ReentrantLock等。



# 三、java锁的核心分类
## 悲观锁
悲观锁总是假设最坏的情况，每次取数据时都认为其他线程会对数据进行修改，所以都会加锁，当其他线程想要访问数据时，都需要阻塞挂起。所以悲观锁总结为**悲观加锁阻塞线程**。

- **悲观锁适合写操作多的场景，先加锁可以保证写操作时数据正确。**

MySQL数据库中的表锁、行锁、读锁、写锁等，Java中，synchronized关键字和Lock的实现类都是悲观锁。

![[Pasted image 20240206165428.png]]

## 乐观锁
而乐观锁认为自己在使用数据时不会有别的线程修改数据，所以**不会添加锁**，只是在更新数据的时候去判断之前有没有别的线程更新了这个数据。如果这个数据没有被更新，当前线程将自己修改的数据成功写入。如果数据已经被其他线程更新，则根据不同的实现方式执行不同的操作（例如**报错**或者**自动重试**）。总结为**乐观无锁回滚重试**。

- **乐观锁适合读操作多的场景，不加锁的特点能够使其读操作的性能大幅提升。**
- **乐观锁天生免疫死锁。**

![[Pasted image 20240206165436.png]]

乐观锁一般有2种实现方式：

### CAS算法
即compare and swap 或者 compare and set，涉及到三个操作数，数据所在的内存值，预期值，新值。当需要更新时，判断当前内存值与之前取到的值是否相等，若相等，则用新值更新，若失败则重试，一般情况下是一个自旋操作，即不断的重试。

**优点：**

效率比较高，无阻塞，无等待，重试。

**缺点：**

**ABA问题：**因为CAS需要在操作值的时候，检查值有没有发生变化，如果没有变化则更新，但是如果一个值原来是A，变成了B，又变成了A，那么CAS检查时发现它的值没有发生变化，但实际上发生了变化：A->B->A的过程。

**循环时间长，开销大：**自旋CAS如果长时间不成功，会给CPU带来很大的执行开销。

**只能保证一个共享变量的原子操作：**当对一个共享变量操作时，我们可以采用CAS的方式来保证原子操作，但是对多个共享变量操作时，循环CAS就无法保证操作的原子性。

### 版本号机制
一般是在数据表中加上一个数据版本号version字段，表示数据被修改的次数，当数据被修改时，version值会加1。当线程A要更新数据值时，在读取数据的同时也会读取version值，在提交更新时，若刚才读取到的version值为当前数据库中的version值相等时才更新，否则重试更新操作，直到更新成功。
```plsql
update table 
set x = x + 1, version = version + 1 
where id = #{id} and version = #{version};
```

**MybaisPlus对乐观锁的实现**

1）在数据库中添加 version 字段，作为乐观锁的版本号

```plsql
--在数据库中的user表中添加一个version字段，用于实现乐观锁
ALTER TABLE `user` ADD COLUMN `version` INT
```
2）在对应的实体类中添加 version 属性，并且在这个属性上面添加 @Version 注解

```java
@Data
public class User {
    @TableId(type = IdType.AUTO)//主键自动增长
    private Long id;
    private String name;
    private Integer age;
    private String email;
 
    @TableField(fill = FieldFill.INSERT)//INSERT的含义就是添加，也就是说在做添加操作时，下面一行中的createTime会有值
    private Date createTime;
 
    @TableField(fill = FieldFill.INSERT_UPDATE)//INSERT_UPDATE的含义就是在做添加和修改时下面一行中的updateTime都会有值，因为是第一次添加，还没有做修改（一般都使用这个）
    private Date updateTime;
 
    @Version//版本号，用于实现乐观锁(这个一定要加)
    @TableField(fill = FieldFill.INSERT)//添加这个注解是为了在后面设置初始值，不加也可以
    private Integer version;
}
```
3）写一个配置类，配置乐观锁插件
```java
@Configuration
@MapperScan("cn.hb.mapper")
public class MpConfig {
    //乐观锁插件
    @Bean
    public OptimisticLockerInterceptor optimisticLockerInterceptor() {
        return new OptimisticLockerInterceptor();
    }
}
```
4）设置版本号 version 的初始值为1

5）向表中添加一条数据，看 version 的值是否为1

6）测试乐观锁，看 version 的值是否加1



# 四、java锁的其他分类
## synchronized性能优化
### 锁膨胀/锁升级

JDK 6之前synchronized是一个独占式的悲观锁、重量级锁，效率偏低。JDK 6中为了减少获得锁和释放锁带来的性能消耗，引入了“偏向锁”和“轻量级锁”。

所以目前锁一共有4种状态，级别从低到高依次是：无锁、偏向锁、轻量级锁和重量级锁。锁状态只能升级不能降级。（注意无锁和偏向锁是同一级别，锁标志位都是01，二者之间不存在膨胀关系，可以理解为无锁状态是轻量锁的空闲状态）

**偏向锁**

在程序第一次执行到 synchronized 代码块的时候，锁对象变成 偏向锁 ，即偏向于第一个获得它的线程的锁。在程序第二次执行到改代码块时，线程会判断此时持有锁的线程是否就是它自己，如果是就继续往下面执行。值得注意的是，在第一次执行完同步代码块时，并不会释放这个偏向锁。从效率角度来看，如果第二次执行同步代码块的线程一直是一个，并不需要重新做加锁操作，没有额外开销，效率极高。
偏向锁主要针对单线程环境或者同一线程重入这两种情况的优化。

**轻量级锁**

当锁是偏向锁的时候，被另外的线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，从而提高性能。

这里不同情况需值得注意：当第二个线程想要获取锁时，且这个锁是偏向锁时，会判断当前持有锁的线程是否仍然存活，如果该持有锁的线程没有存活，那么偏向锁并不会升级为轻量级锁 。

**重量级锁**

若当前只有一个等待线程，则该线程通过自旋进行等待。但是当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁升级为重量级锁。

当其他线程再次尝试获取锁的时候，发现现在的锁是重量级锁，此时线程都会进入阻塞状态。

自旋次数过多意味着锁的持有时间很长，这时候自旋操作效果不好，浪费了大量的CPU资源，多线程竞争也就是高并发也是一样，会浪费CPU资源，所以这种情况就要让线程进入阻塞，直到锁是可用的。

### 锁消除
锁消除即删除不必要的加锁操作。JVM在运行时，对一些“在代码上要求同步，但是**被检测到不可能存在共享数据竞争情况”的锁进行消除。**根据代码逃逸技术，如果判断到一段代码中，**堆上的数据不会逃逸出当前线程**，那么就可以认为这段代码是线程安全的，无需加锁。
### 锁粗化
假设一系列的连续操作都会**对同一个对象反复加锁及解锁**，甚至加锁操作是出现在循环体中的，即使没有出现线程竞争，频繁地进行互斥同步操作也会导致不必要的性能损耗。

如果JVM检测到有一连串零碎的操作都是对同一对象的加锁，将会**扩大加锁同步的范围（即锁粗化）到整个操作序列的外部。**

### 自适应自旋锁
**自旋锁：

现在绝大多数的个人电脑和服务器都是多路（核）处理器系统，如果物理机器有一个以上的处理器或者处理器核心，能让两个或以上的线程同时并行执行，就可以让后面请求锁的那个线程“稍等一会”，但不放弃处理器的执行时间，看看持有锁的线程是否很快就会释放锁。
自旋锁优点在于它避免一些线程的挂起和恢复操作，因为挂起线程和恢复线程都需要从用户态转入内核态，这个过程是比较慢的，所以通过自旋的方式可以一定程度上避免线程挂起和恢复所造成的性能开销。

**自适应自旋锁：**

但是，如果长时间自旋还获取不到锁，那么也会造成一定的资源浪费，所以我们通常会给自旋设置一个固定的值来避免一直自旋的性能开销。然而对于 synchronized 关键字来说，它的自旋锁更加的“智能”，synchronized 中的自旋锁是自适应自旋锁。

自适应自旋锁是指，**线程自旋的次数不再是固定的值，而是一个动态改变的值，这个值会根据前一次自旋获取锁的状态来决定此次自旋的次数。**比如上一次通过自旋成功获取到了锁，那么这次通过自旋也有可能会获取到锁，所以这次自旋的次数就会增多一些，而如果上一次通过自旋没有成功获取到锁，那么这次自旋可能也获取不到锁，所以为了避免资源的浪费，就会少循环或者不循环，以提高程序的执行效率。简单来说，**如果线程自旋成功了，则下次自旋的次数会增多，如果失败，下次自旋的次数会减少。**

### 防止死锁

- 不要写嵌套锁，容易死锁
- 尽量少用同步代码块(Synchronized)
- 尽量使用ReentrantLock的tryLock方法设置超时时间，超时可以退出，防止死锁
- 尽量降低锁粒度，尽量不要几个功能一把锁



## 公平锁与非公平锁
当一个线程持有的锁释放时，其他线程按照先后顺序，先申请的先得到锁，那么这个锁就是公平锁。反之，如果后申请的线程有可能先获取到锁，就是非公平锁 。

Java 中的 ReentrantLock 可以通过其构造函数来指定是否是公平锁，**默认是非公平锁**。一般来说，使用非公平锁可以获得较大的吞吐量，所以**推荐优先使用非公平锁**。

synchronized 是一种非公平锁。



## 可重入锁和非可重入锁
可重入锁又名递归锁，是指在同一个线程在外层方法获取锁的时候，再进入该线程的内层方法会自动获取锁（前提锁对象得是同一个对象或者class），不会因为之前已经获取过还没释放而阻塞。Java中**ReentrantLock和synchronized都是可重入锁，可重入锁的一个优点是可一定程度避免死锁。**

```java
public class Widget {
    public synchronized void doSomething() {
        System.out.println("方法1执行...");
        doOthers();
    }

    public synchronized void doOthers() {
        System.out.println("方法2执行...");
    }
}
```
在上面的代码中，类中的两个方法都是被内置锁synchronized修饰的，doSomething()方法中调用doOthers()方法。因为内置锁是可重入的，所以同一个线程在调用doOthers()时可以直接获得当前对象的锁，进入doOthers()进行操作。

如果是一个**不可重入锁**，那么当前线程在调用doOthers()之前需要将执行doSomething()时获取当前对象的锁释放掉，实际上该对象锁已被当前线程所持有，且无法释放。所以此时会出现死锁。
## 独享锁与共享锁
独占锁是一种思想： 只能有一个线程获取锁，以独占的方式持有锁。

Java中用到的独占锁： synchronized，ReentrantLock

共享锁是一种思想： 可以有多个线程获取读锁，以共享的方式持有锁。

Java中用到的共享锁： ReentrantReadWriteLock。

