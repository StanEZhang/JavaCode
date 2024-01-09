# 1.拉取镜像

```shell
docker pull mysql:5.7
```

# 2.创建MySQL实例

```shell
docker run \
-p 3306:3306 \
--name mysql \
--restart=always \
-v /mydata/mysql/log:/var/log/mysql \
-v /mydata/mysql/data:/var/lib/mysql \
-v /mydata/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7

-v [宿主机目录]:[容器内目录]
-v [宿主机文件]:[容器内文件]
```

## 配置端口映射：

将容器的3306端口映射到宿主机的3306端口

-p 3306:3306

## 容器名：

--name mysql

## 配置MySQL数据卷挂载：

-v /mydata/mysql/log:/var/log/mysql(日志文件挂载)

将容器中的日志文件夹挂载到主机对应的/mydata/mysql/log文件夹中

-v /mydata/mysql/data:/var/lib/mysql(数据文件挂载)

将容器中的数据文件夹挂载到主机对应的/mydata/mysql/data文件夹中

-v /mydata/mysql/conf:/etc/mysql(配置文件挂载)

将容器的配置文件夹挂载到主机对应的/mydata/mysql/conf文件夹中

注(这里所提的主机指的是当前的linux主机)

## 配置用户：

设置初始化root用户的密码

-e MYSQL_ROOT_PASSWORD=root

## 指定镜像资源：

-d mysql:5.7

-d : 以后台方式运行实例

mysql:5.7 ：指定用这个镜像来创建运行实例

# 3.mysql配置文件my.conf

```shell
vi /mydata/mysql/conf/my.cnf
```



```shell
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
init_connect='SET collation_connection = utf8_unicode_ci' init_connect='SET NAMES utf8' character-set-server=utf8
collation-server=utf8_unicode_ci
skip-character-set-client-handshake
skip-name-resolve
```



到这一步时候Navicat已经可以连接了。



# *通过容器的 mysql 命令行工具连接

```shell
docker exec -it mysql mysql -uroot -proot
```

这样就在连接上了mysql，可以执行sql语句了。

**设置 root 远程访问**

下面这两行代码我没执行，没什么影响

```shell
grant all privileges on *.* to 'root'@'%' identified by 'root' with grant option;
flush privileges;
```



# *进入容器文件系统

```shell
docker exec -it mysql /bin/bash
# 查看配置文件
cd /etc/mysql
ls
cat my.conf
```



