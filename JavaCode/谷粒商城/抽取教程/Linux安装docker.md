## 1. 卸载（可选）

如果之前安装过旧版本的Docker，可以使用下面命令卸载：

```shell
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine \
                  docker-ce
```

## 2. 安装docker

①首先需要大家虚拟机联网，安装yum工具：

```shell
yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2 --skip-broken
```

②然后更新本地镜像源：

```shell
# 设置docker镜像源
yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
sed -i 's/download.docker.com/mirrors.aliyun.com\/docker-ce/g' /etc/yum.repos.d/docker-ce.repo
 
yum makecache fast
```

③然后输入命令安装docker：

-y 的意思是告诉 yum 自动回答所有提示为“是”

```shell
yum install -y docker-ce docker-ce-cli containerd.io
```

docker-ce为社区免费版本。稍等片刻，docker即可安装成功。

## 3. 启动docker

防火墙设置

```shell
# 关闭
systemctl stop firewalld
# 禁止开机启动防火墙
systemctl disable firewalld
```

启动docker：

```shell
systemctl start docker  # 启动docker服务
 
systemctl stop docker  # 停止docker服务
 
systemctl restart docker  # 重启docker服务

systemctl enable docker # 开机自动启动docker
```

查看docker版本：

```shell
docker -v
```

## 4.配置镜像加速

**安装／升级Docker客户端**

推荐安装1.10.0以上版本的Docker客户端

**配置镜像加速器**

针对Docker客户端版本大于 1.10.0 的用户

您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://2n0e4hw0.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 5.卸载Docker

卸载依赖

```shell
sudo yum remove docker-ce docker-ce-cli containerd.io
```

删除文件夹

```shell
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

