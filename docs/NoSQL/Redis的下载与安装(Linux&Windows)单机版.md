# Redis的下载与安装(Linux&Windows)单机版

## 前言

博客书：点击

版本说明

```properties
linux.centos=7.7.1908
linux.redis=5.0.5

windows=10
windows.redis=3.2.100
```

相关链接

* Redis 官网：https://redis.io/
* Redis 官网下载地址：https://redis.io/download
* Redis windows 下载地址：https://github.com/MicrosoftArchive/redis/releases
* Redis DockerHub 网址：https://hub.docker.com/_/redis
* Redis Client 网址： https://github.com/caoxinyu/RedisClient/releases

## Docker 下载安装

DockerHub 地址：https://hub.docker.com/_/redis

```shell
# 拉取
docker pull redis
# 运行
## 无密码运行
docker run -d --name redis-0 -p 6379:6379  redis
## 有密码运行
docker run -d --name redis-0 -p 6379:6379 redis --requirepass redisPassword
```

## Linux 下载安装

### 环境准备

```shell
yum install -y gcc-c++
```

Redis 官网下载地址：https://redis.io/download

### 下载

```shell
mkdir -p /usr/local/redis
# 上传文件
cd /usr/local/redis
tar -zxvf redis-5.0.5.tar.gz
cd redis-5.0.5
```

### 编译与安装

```shell
cd /usr/local/redis/redis-5.0.5
# 编译
make
#安装成功之后 在redis目录下多出来一个bin目录
make PREFIX=/usr/local/redis install
```

### 启动

```shell
# 前台启动
cd /usr/local/redis/bin
./redis-server
# 后台启动
## 将 redis-5.0.5 目录下的 redis.conf 文件复制到 /usr/local/redis/bin 下
## 修改 /usr/local/redis/bin 目录下的 redis.conf 设置为后台启动, 修改 daemonize 值改成 yes 即可
## 后台启动
./redis-server redis.conf
```

### Redis-cli 启动验证

```shell
# 方式一，默认连接的本机6379端口
./redis-cli
# 方式二，连接指定主机 指定端口号
./redis-cli -h 192.168.8.50 -p 6379
```

## Windows 下载安装

Redis windows 下载地址：https://github.com/MicrosoftArchive/redis/releases

zip 文件解压即安装，

msi 文件安装即可，可参考：https://blog.csdn.net/SIMBA1949/article/details/79845239



