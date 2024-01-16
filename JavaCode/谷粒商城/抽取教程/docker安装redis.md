# 拉取镜像

```shell
docker pull redis:5.0.9

# 中止当前命令
ctrl + c
```

# 挂载配置文件

挂载位置（可自己选择）：

linux下redis.conf文件位置：/mydata/redis/redis.conf

linux下redis的data文件位置：/mydata/redis/data

```shell
mkdir -p /mydata/redis/data
```

然后上传redis.conf文件到宿主机linux上。

redis.conf可以在github源码文件中找到：https://github.com/redis/redis/blob/5.0/redis.conf

```shell
# bind 127.0.0.1 # 这行要注释掉，解除本地连接限制
protected-mode no # 默认yes，如果设置为yes，则只允许在本机的回环连接，其他机器无法连接。
daemonize no # 默认no 为不守护进程模式，docker部署不需要改为yes，docker run -d本身就是后台启动，不然会冲突
requirepass 123456 # 设置密码
appendonly yes # 持久化
```



这里暂时用网上找到的用一下，以后再研究具体的配置细节与含义。

**允许远程访问，将bind 127.0.0.1注释，protected-mode设置为no**

```shell
# bind 192.168.1.100 10.0.0.1
# bind 127.0.0.1 ::1
#bind 127.0.0.1

protected-mode no

port 6379

tcp-backlog 511

#requirepass 000415

timeout 0

tcp-keepalive 300

daemonize no

supervised no

pidfile /var/run/redis_6379.pid

loglevel notice

logfile ""

databases 30

always-show-logo yes

save 900 1
save 300 10
save 60 10000

stop-writes-on-bgsave-error yes

rdbcompression yes

rdbchecksum yes

dbfilename dump.rdb

dir ./

replica-serve-stale-data yes

replica-read-only yes

repl-diskless-sync no

repl-disable-tcp-nodelay no

replica-priority 100

lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
replica-lazy-flush no

appendonly yes

appendfilename "appendonly.aof"

no-appendfsync-on-rewrite no

auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

aof-load-truncated yes

aof-use-rdb-preamble yes

lua-time-limit 5000

slowlog-max-len 128

notify-keyspace-events ""

hash-max-ziplist-entries 512
hash-max-ziplist-value 64

list-max-ziplist-size -2

list-compress-depth 0

set-max-intset-entries 512

zset-max-ziplist-entries 128
zset-max-ziplist-value 64

hll-sparse-max-bytes 3000

stream-node-max-bytes 4096
stream-node-max-entries 100

activerehashing yes

hz 10

dynamic-hz yes

aof-rewrite-incremental-fsync yes

rdb-save-incremental-fsync yes
```

# 启动redis容器

```shell
docker run \
--restart=always \
--log-opt max-size=100m \
--log-opt max-file=2 \
--requirepass zhb819294zhb. \
-p 6379:6379 \
--name redis \
-v /mydata/redis/redis.conf:/etc/redis/redis.conf \
-v /mydata/redis/data:/data \
-d redis:5.0.9 \
redis-server /etc/redis/redis.conf
```

--restart=always 总是开机启动

--log 是日志方面的

-p 6379:6379 端口号

--name 给这个容器取一个名字

-v 数据卷挂载

-d redis 表示后台启动redis

redis-server /etc/redis/redis.conf 以配置文件启动redis，加载容器内的conf文件，最终找到的是挂载的目录 /etc/redis/redis.conf 也就是liunx下的/mydata/redis/redis.conf

–appendonly yes 开启redis 持久化

–requirepass 123456 设置密码

# 测试

查看启动状态

```shell
docker ps -a |grep redis # 通过docker ps指令查看启动状态，是否成功.
```

查看容器运行日志

```shell
docker logs --since 30m redis
```



# 删除redis容器

```shell
docker ps -a
docker stop redis
docker rm redis
```

# 删除redis镜像

```shell
docker images
docker rmi 739b59b96069 # 镜像redis id
```

# 删除挂载目录