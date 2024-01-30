技术总是在不断更新变化的，尤其是在IT编程领域。

有时候我们理所当然的用着现成的框架，以至于用的太过于顺手，更要时不时的骂一句：

什么垃圾框架？我家狗都不会用！

如果那些被拍死在沙滩的“前浪”听到这话，怕是要顶开棺材板给你点个赞。

为什么？

因为太安逸了，安逸到一出生就开着拖拉机耕地，还嫌拖拉机费油。

今天来看看不费油的，费人。

---

现在MyBatis框架用的比较熟练了，但是有时候不明白原理，也不知道MyBatis具体做了什么工作，于是就把JDBC翻出来看一下，忆苦思甜。

# 什么是 JDBC

我们是学 java 的，我们要操作数据库，我们怎么办？

聪明的 Sun 公司想了个办法，我们定义一套 java 接口，一套标准的操作数据库的 API，你们各家数据库公司都按我的来，不按我来的都给我滚。

于是就有了 JDBC，JDBC 就是这套接口的名字，全称叫 Java DataBase Connectivity（Java 数据库连接）。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/25734432/1702983632260-71a04867-6d91-4fcd-af21-0b0db7017514.png#averageHue=%23f9f0ef&clientId=u4dffbcc7-e208-4&from=paste&height=307&id=u5b25adb2&originHeight=422&originWidth=798&originalType=binary&ratio=1.375&rotation=0&showTitle=false&size=48980&status=done&style=none&taskId=u040943ba-a02d-4a1b-8d6c-2c11d1356f8&title=&width=580.3636363636364)
我们也就应该知道，你光有接口是操作不了数据库的，所以你要用 MySQL 数据库，就得装上 MySQL 驱动，用谁家的数据库，就装谁家的数据库驱动。

比如：

```xml
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.28</version>
</dependency>
```

# JDBC 怎么用
有了接口实现，也就是驱动，那我们就可以用了呀？干嘛？操作数据库！

```java
public class JDBCDemo {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 1.注册驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        // 2.获取连接 Connection可管理事务
        String url = "jdbc:mysql://127.0.0.1:3306/test";
        String username = "root";
        String password = "root";
        Connection conn = DriverManager.getConnection(url, username, password);
        // 3.定义sql
        String sql = "update person set name = '张三' where id = 1";
        // 4.获取执行sql的对象Statement
        // 普通执行SQL对象 Statement createStatement() throws SQLException;
        // 预编译SQL的执行SQL对象：防止SQL注入 
        // PreparedStatement prepareStatement(String sql)throws SQLException;
        Statement stmt = conn.createStatement();
        // 5.执行sql Statement有各种执行sql的方法，可查看源码，注意返回的ResultSet
        int count = stmt.executeUpdate(sql);
        // 6.处理结果
        System.out.println(count);
        // 7.释放资源
        stmt.close();
        conn.close();
    }
}
```


从以上 JDBC 的实际开发中可以看到2个明显的缺点：
1.硬编码
2.操作繁琐
那么对此 MyBatis 做了什么？
1.硬编码配置到配置文件
2.操作繁琐的地方自动完成

---
谨以此文纪念JDBC，希望你永远不要醒来。