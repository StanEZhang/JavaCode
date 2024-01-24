# 一、nacos注册中心

## 1. Linux安装nacos

安装nacos之前，安装jdk。

[[Linux安装jdk]]

到github下载nacos最新版。

https://github.com/alibaba/nacos/releases

上传到服务器某路径：/usr/local/nacos/

解压，进入到bin目录下，编辑startup.sh，将cluster模式改为standalone模式，然后启动。

```shell
tar -xzvf 包名
# xzvf卸载违法
vim startup.sh
# 按i进入编辑模式，修改完成按ESC退出编辑模式，:wq保存退出
# 启动
sh startup.sh
```

nacos地址：

ip:8848/nacos

默认用户名密码：nacos/nacos

## 2. 注册中心

common模块添加公共依赖

```xml
<!--使用spring-cloud-alibaba-nacos做服务注册中心-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

配置nacos地址

```yaml
spring:
  application:
    name: coupon
  cloud:
    nacos:
      discovery:
        server-addr: 43.143.198.2:8848
```

开启服务发现

```java
@EnableDiscoveryClient
public class CouponApplication {
    public static void main(String[] args) {
        SpringApplication.run(CouponApplication.class, args);
    }
}
```

注意要注册成功还需要application.name

## 3.配置中心

```xml
<!--使用spring-cloud-alibaba-nacos做配置中心-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

```yaml
spring:
  cloud:
    nacos:
      config:
        server-addr: 43.143.198.2:8848
```

创建配置，默认命名规则为：应用名.properties

注意：

1.@RefreshScope 的使用可以动态刷新配置

2.nacos配置中心的配置优先级高于配置文件

### 配置中心进阶

几个概念

**命名空间：**

用于进行租户粒度的配置隔离。不同的命名空间下，可以存在相同的 Group 或 Data ID 的配置。Namespace 的常用场景之一是不同环境的配置的区分隔离，例如开发测试环境和生产环境的资源（如配置、服务）隔离等。

**配置集：**

一组相关或者不相关的配置项的集合称为配置集。在系统中，一个配置文件通常就是一个配置集，包含了系统各个方面的配置。例如，一个配置集可能包含了数据源、线程池、日志级别等配置项。

**配置集 ID：**
Nacos 中的某个配置集的 ID。配置集 ID 是组织划分配置的维度之一。Data ID 通常用于组织划分系统的配置集。一个系统或者应用可以包含多个配置集，每个配置集都可以被一个有意义的名称标识。Data ID 通常采用类 Java 包（如 com.taobao.tc.refund.log.level）的命名规则保证全局唯一性。此命名规则非强制。

**配置分组：**
Nacos 中的一组配置集，是组织配置的维度之一。通过一个有意义的字符串（如 Buy 或 Trade ）对配置集进行分组，从而区分 Data ID 相同的配置集。当您在 Nacos 上创建一个配置时，如果未填写配置分组的名称，则配置分组的名称默认采用 DEFAULT_GROUP 。配置分组的常见场景：不同的应用或组件使用了相同的配置类型，如 database_url 配置和MQ_topic 配置。