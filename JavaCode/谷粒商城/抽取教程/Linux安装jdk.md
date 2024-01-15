# 一、查看Linux系统是否有自带的jdk

1、输入：java -version，查看当前是否有jdk版本

2、发现有输入：rpm -qa | grep java 检测jdk的安装包

3、接着进行一个个删除包，输入：rpm -e --nodeps +包名

4、最后再次：rpm -qa | grep java检查是否删除完即可

# 二、官网下载jdk

官网下载地址：

https://www.oracle.com/java/technologies/downloads/#java8

![[Pasted image 20240115160244.png]]

# 三、上传jdk安装到linux服务器

这里我是用的是FinalShell直接拖动进来即可，路径为/opt目录

# 四、解压jdk

```shell
# 进入/opt
cd /opt
ls
tar -xvf jdk-8u341-linux-x64.tar.gz
```

# 五、配置环境变量

```shell
vim /etc/profile
```

用vim /etc/profile进入文件，按i进入编辑状态，加入下边这段配置，esc退出编辑，:wq保存退出。

```shell
export JAVA_HOME=/opt/jdk1.8.0_341
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

# 六、重新加载配置

```shell
source /etc/profile
```

注意空格

七、测试

```shell
java -verison
javac
```

