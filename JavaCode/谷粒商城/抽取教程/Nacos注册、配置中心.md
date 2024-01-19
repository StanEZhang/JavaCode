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

## 2. 服务配置

common模块添加公共依赖

```xml
<!--使用spring-cloud-alibaba-nacos做服务注册中心-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<!--使用spring-cloud-alibaba-nacos做配置中心-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
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

