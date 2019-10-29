# FastDFS & Nginx 分布式文件系统安装教程

## 前言

版本说明：

```
Nginx ：nginx-1.17.4.tar.gz
FastDFS ：V5.12
libfastcommon ：V1.0.40
FastDFS-nginx-module ：V1.20
```

链接地址：

* FastDFS 下载地址：https://sourceforge.net/projects/fastdfs/
* FastDFS 的 github 下载地址：https://github.com/happyfish100/fastdfs/releases
* libfastcommon 的下载地址：https://sourceforge.net/projects/libfastcommon/
* libfastcommon 的 github 下载地址：https://github.com/happyfish100/libfastcommon/releases
* FastDFS-nginx-module 的下载地址：https://sourceforge.net/projects/fastdfs/files/FastDFS%20Nginx%20Module%20Source%20Code/
* FastDFS-nginx-module 的 github 下载地址：https://github.com/happyfish100/fastdfs-nginx-module/releases
* Nginx 下载地址：http://nginx.org/en/download.html
* Nginx 的 github 下载地址：https://github.com/nginx/nginx

## 安装

### 安装依赖环境

```shell
# FastDFS 编译依赖 gcc 环境
yum install -y gcc-c++
yum install -y perl
# FastDFS 依赖 libevent 库。4 版本前用 libevent，5 直接安装 libfastcommon 即可。
yum install -y libevent
```

暴力安装

```shell
yum -y install zlib zlib-devel pcre pcre-devel gcc gcc-c++ openssl openssl-devel libevent libevent-devel perl unzip net-tools wget lrzsz
```

### tracker 编译安装

#### 安装 libfastcommon

libfastcommon是FastDFS官方提供的，libfastcommon包含了FastDFS运行所需要的一些基础库。

 下载地址：https://sourceforge.net/projects/libfastcommon/ 

```shell
# 可以通过上面下载链接下载上传，或者通过 wget 方式下载
wget https://codeload.github.com/happyfish100/libfastcommon/tar.gz/V1.0.40
tar -zxvf V1.0.40
```

 进入解压目录，进行编译，安装 

```shell
# 进入解压目录
cd libfastcommon-1.0.40
# 编译
./make.sh
# 安装
./make.sh install
```

#### 编译安装 tracker

下载地址：https://github.com/happyfish100/fastdfs/releases

下载 FastDFS.tar.gz 文件，并解压

```shell
wget https://codeload.github.com/happyfish100/fastdfs/tar.gz/V5.12
tar -zxvf V5.12
```

 进入解压的目录，进行编译，安装 

```shell
# 进入解压目录
cd fastdfs-5.12
# 编译
./make.sh
# 安装
./make.sh install
# 安装成功后，将安装目录下的 conf 下的文件拷贝到/etc/fdfs/下
cd conf/
cp * /etc/fdfs/
```

#### 配置 tracker

修改 /etc/fdfs/tracker.conf 

```properties
# 配置 tracker.conf 配置文件
# 启用配置文件
# false for enabled
# true for disabled
disabled=false
# tracker服务器端口（默认22122）
port=22122
# 存储日志和数据的根目录，可以根据默认创建目录，也可以修改为自己的存储路径
base_path=/home/fastdfs/tracker/bash_path
# store_path#, based 0, if store_path0 not exists, it's value is base_path
# the paths must be exist
store_path0=/home/fastdfs/tracker/store_path0
```

#### 启动 tracker

```shell
# 启动
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start
service fdfs_trackerd start
# 停止
service fdfs_trackerd stop
```

####  设置开启启动 

```shell
vi /etc/rc.d/rc.local
# 在 rc.local 配置文件添加如下命令即可
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start
```

### storage 编译安装

#### 安装 libfastcommon

同 tracker 编译安装是的**安装 libfastcommon**。（如果是一台虚拟机安装Tracker和Storage，这里可以省略）

#### 编译安装 storage

同 **编译安装 tracker** 。（如果是一台虚拟机安装Tracker和Storage，这里可以省略）

#### 配置 storage

 修改 /etc/fdfs/storage.conf 

```properties
# 端口，storage 默认端口为 23000
port=23000
# 存储日志和数据的根目录，可以根据默认创建目录，也可以修改为自己的存储路径
base_path=/home/fastdfs/storage/base_path

# 配置 store 挂载磁盘数量
store_path_count=2

#如果有多个挂载磁盘则定义多个store_path，如下
store_path0=/home/fastdfs/storage/00
store_path1=/home/fastdfs/storage/01

# 配置tracker服务器ip，如果有多个可以配置多个
tracker_server=192.168.8.46:22122
```

#### 启动 storage

```shell
# 创建软连接
ln -s /usr/bin/fdfs_storaged /usr/local/bin/
# 启动
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf start
service fdfs_storaged start
# 停止
service fdfs_storaged stop
systemctl stop fdfs_storaged
```

####  设置开启启动 

```shell
vi /etc/rc.d/rc.local
# 在 rc.local 配置文件添加如下命令即可
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf start
```

## 测试

### 通过 fdfs_test 程序

FastDFS 安装成功可通过 /usr/bin/fdfs_test 测试上传和下载等操作

### 配置 client.conf

修改 /etc/fdfs/client.conf 文件

```properties
# 配置存储日志和数据的根目录
base_path=/home/fastdfs/client/base_path

# 配置 tracker_server 地址
tracker_server=192.168.8.46:22122
```

### 上传文件测试

```shell
# 上传文件格式，由于文档在浏览器直接下载，建议使用图片之类的测试
/usr/bin/fdfs_test 客户端配置文件地址 upload 上传文件
/usr/bin/fdfs_test  /etc/fdfs/client.conf upload Docsify和Github部署博客书.md
```

如图示

上传 md 文件测试

![图片](https://github.com/simba1949/images/blob/master/HighConcurrency&HighAvailability&Distributed/fastdfs-client%E6%B5%8B%E8%AF%95.png?raw=true)

上传图片测试

![](https://github.com/simba1949/images/blob/master/HighConcurrency&HighAvailability&Distributed/fastdfs-client%E5%9B%BE%E7%89%87%E6%B5%8B%E8%AF%95.png?raw=true)

## 整合 Nginx

### 安装 FastDFS-nginx-module

 下载地址：https://github.com/happyfish100/fastdfs-nginx-module/releases 

```shell
wget https://codeload.github.com/happyfish100/fastdfs-nginx-module/tar.gz/V1.20
tar -zxvf V1.20
```

 进入 fastdfs-nginx-module/src/ 目录下，编辑 config 配置文件 

```
cd /usr/local/app/fastdfs/fastdfs-nginx-module-1.20/src
vim config

# 
将： CORE_INCS="$CORE_INCS /usr/lib/include"
修改为 ：CORE_INCS="$CORE_INCS /usr/include"

CORE_INCS="$CORE_INCS /usr/local/include"
```

 复制 fastdfs-nginx-module 源码中的配置文件到 /etc/fdfs 目录并进行配置 

```shell
cp src/mod_fastdfs.conf /etc/fdfs/
```

 配置 /etc/fdfs/mod_fastdfs.conf 文件 

```properties
#修改以下配置
base_path=/tmp
# tracker服务IP和端口，多个就配置多行
tracker_server=192.168.8.36:22122
# storage服务端口
storage_server_port=23000
# 组名
group_name=group1
# 访问链接前缀加上组名
url_have_group_name=true
# storage 数量
store_path_count=2
# 文件存储路径
store_path0=/home/fastdfs/storage/00
store_path1=/home/fastdfs/storage/01
```

### 安装 Nginx 依赖

```shell
yum -y install gcc pcre pcre-devel zlib zlib-devel openssl openssl-devel
```

### 下载与安装 Nginx

```shell
# 下载 nginx
wget http://nginx.org/download/nginx-1.17.4.tar.gz
# 解压
tar -zxvf nginx-1.17.4.tar.gz
# 配置，添加 FastDFS-nginx-module
cd nginx-1.17.4
./configure \
 --prefix=/usr/local/nginx \
 --pid-path=/var/run/nginx/nginx.pid \
 --lock-path=/var/lock/nginx.lock \
 --error-log-path=/var/log/nginx/error.log \
 --http-log-path=/var/log/nginx/access.log \
 --with-http_gzip_static_module \
 --http-client-body-temp-path=/var/temp/nginx/client \
 --http-proxy-temp-path=/var/temp/nginx/proxy \
 --http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
 --http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
 --http-scgi-temp-path=/var/temp/nginx/scgi \
 --add-module=/usr/local/app/fastdfs/fastdfs-nginx-module-1.20/src
# 安装
make && make install
```

> 注意
>
> ```
> --prefix=/usr/local/nginx ：是安装目录 
> --add-module : 是 FastDFS-nginx-module 解压目录
> ```

### 配置 Nginx

 创建 nginx/client 目录 

```
mkdir -p /var/temp/nginx/client
```

 配置 nginx 配置文件 

```
# 添加一个，用于 nginx 转发到 fastdfs 服务器上
location ~/group([0-9])/M([0-9]*) {
	ngx_fastdfs_module;
}
```

 需要释放 Nginx 监听的端口，启动 Nginx 

```shell
# 释放 Nginx 端口
firewall-cmd --zone=public --add-port=80/tcp --permanent
# 释放 tracker 端口
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --zone=public --add-port=22122/tcp --permanent
# 释放 storage 端口
firewall-cmd --zone=public --add-port=23000/tcp --permanent
firewall-cmd --reload
```

### Nginx 命令

```shell
cd /usr/local/nginx/sbin
# 启动
./nginx
#
```

## 通过 URL 访问测试

访问上传图片是返回的请求地址

![](https://github.com/simba1949/images/blob/master/HighConcurrency&HighAvailability&Distributed/fastdfs-nginx%E8%AE%BF%E9%97%AE%E6%B5%8B%E8%AF%95.png?raw=true)







