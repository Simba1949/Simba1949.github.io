# CentOS8安装RabbitMQ

## 前言

版本说明

```properties
centos=8
rabbitmq=3.8.4
```

参考链接

* 推荐更新镜像仓库 ： https://blog.csdn.net/SIMBA1949/article/details/86658042
* RabbitMQ 和 Erlang 版本选择说明 ： https://www.rabbitmq.com/which-erlang.html](https://www.rabbitmq.com/which-erlang.html)
* Erlang packagecloud下载链接 ： https://packagecloud.io/rabbitmq/erlang
* RabbitMQ packagecloud下载链接 ： https://packagecloud.io/rabbitmq/rabbitmq-server
* RabbitMQ docker 镜像地址：https://registry.hub.docker.com/_/rabbitmq/
* rabbitmq badrpc 解决方案参考链接：https://blog.csdn.net/jiguquan3839/article/details/91346261

## Docker 安装

RabbitMQ docker 镜像地址：https://registry.hub.docker.com/_/rabbitmq/

```shell
# 下载与安装
docker pull rabbitmq
# 运行 RabbitMQ 镜像
docker run -di --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq
# 启动 web 界面（其中rabbitmq为运行RabbitMQ镜像时的name属性值）
docker exec -it rabbitmq rabbitmq-plugins enable rabbitmq_management
# 默认用户：guest 默认密码：guest
```

## 前置依赖安装

### Erlang 安装

**packagecloud 安装方式安装（推荐）**

```shell
# erlang 脚本
curl -s https://packagecloud.io/install/repositories/rabbitmq/erlang/script.rpm.sh | sudo bash
# 安装 erlang，查看 erlang 版本号（erl -version），进入 erlang 命令行(erl)需要等待一些时间
yum install -y erlang
```

**YUM 方式**

参考链接：https://www.erlang-solutions.com/resources/download.html

```shell
# Adding repository entry
wget https://packages.erlang-solutions.com/erlang-solutions-2.0-1.noarch.rpm
rpm -Uvh erlang-solutions-2.0-1.noarch.rpm

# Alternatively: adding the repository entry manually
rpm --import https://packages.erlang-solutions.com/rpm/erlang_solutions.asc

# /etc/yum.repos.d/erlang_solutions.repo ，配置内容如下（一般不需要配置）
[erlang-solutions]
name=CentOS $releasever - $basearch - Erlang Solutions
baseurl=https://packages.erlang-solutions.com/rpm/centos/$releasever/$basearch
gpgcheck=1
gpgkey=https://packages.erlang-solutions.com/rpm/erlang_solutions.asc
enabled=1

# Adding repository with dependencies
# centos8
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

# Installing Erlang
sudo yum install erlang -y
```

No package erlang available.
请参考：https://blog.csdn.net/SIMBA1949/article/details/86655266

### Socat 和 logrotate下载与安装

文件下载地址：http://www.dest-unreach.org/socat/

```shell
sudo yum install socat logrotate -y 
```

## packagecloud 安装方式安装（推荐）

rabbitmq badrpc 解决方案参考链接：https://blog.csdn.net/jiguquan3839/article/details/91346261

```shell
# 需要先安装 erlang
# RabbitMQ 脚本
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
# 安装 RabbitMQ
yum install -y rabbitmq-server
# 需要修改 /etc/hosts 文件，参考 rabbitmq badrpc 解决方案参考链接，添加内容如下：li-bai 是主机名
192.168.8.10 li-bai

# 启动 RabbitMQ web界面
rabbitmq-plugins enable rabbitmq_management 
# 启动 RabbitMQ 
service rabbitmq-server start

# 添加一个 rabbitmq 用户
rabbitmqctl add_user 'simba' '123456'
# 列出所有的虚拟主机
rabbitmqctl list_vhosts
# 授权给用户(/表示查询出来的虚拟主机，simba表示上面设置的用户名)
# First ".*" for read permission on every entity
# Second ".*" for write permission on every entity
# Third ".*" for configure permission on every enti
rabbitmqctl set_permissions -p "/" "simba" ".*" ".*" ".*"
# 列出虚拟主机上所有权限(/表示查询出来的虚拟主机，)
rabbitmqctl list_permissions -p /
# 设置为管理员（simba表示上面设置的用户名）
rabbitmqctl set_user_tags simba administrator
# 进入 RabbitMQ web管理界面，并登陆
# 访问 ip:15672 即可，账户：simba，密码：123456
```

## RPM 方式安装

```shell
# 需要先安装 erlang
# 下载 rpm 包 
wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.4/rabbitmq-server-3.8.4-1.el7.noarch.rpm
# 安装
yum install -y rabbitmq-server-3.8.4-1.el7.noarch.rpm

# 需要修改 /etc/hosts 文件，参考 rabbitmq badrpc 解决方案参考链接，添加内容如下：li-bai 是主机名
192.168.8.10 li-bai


# 启动 RabbitMQ web界面
rabbitmq-plugins enable rabbitmq_management 
# 启动 RabbitMQ 
service rabbitmq-server start

# 添加一个 rabbitmq 用户
rabbitmqctl add_user 'simba' '123456'
# 列出所有的虚拟主机
rabbitmqctl list_vhosts
# 授权给用户(/表示查询出来的虚拟主机，simba表示上面设置的用户名)
# First ".*" for read permission on every entity
# Second ".*" for write permission on every entity
# Third ".*" for configure permission on every enti
rabbitmqctl set_permissions -p "/" "simba" ".*" ".*" ".*"
# 列出虚拟主机上所有权限(/表示查询出来的虚拟主机，)
rabbitmqctl list_permissions -p /
# 设置为管理员（simba表示上面设置的用户名）
rabbitmqctl set_user_tags simba administrator
# 进入 RabbitMQ web管理界面，并登陆
# 访问 ip:15672 即可，账户：simba，密码：123456
```



