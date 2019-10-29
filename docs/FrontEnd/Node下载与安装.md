# Node下载与安装

## 前言

Node 官网： https://nodejs.org/en/ 

Node 下载地址： https://nodejs.org/en/download/ 

## 安装

### 安装包方式安装

环境：CentOS7/Node-v13.0.0/

上传安装包到服务器上，并解压

```shell
tar -xvf node-v13.0.0-linux-x64.tar.xz
```

进入解压目录，查看当前路径

```shell
[root@localhost node-v13.0.0-linux-x64]# pwd
/usr/local/ide/node/node-v13.0.0-linux-x64
```

配置环境变量，vim /etc/profile ，配置内容如下，执行 source /etc/profile 重新加载配置文件

```
# node13
export NODE_HOME=/usr/local/ide/node/node-v13.0.0-linux-x64
export PATH=$NODE_HOME/bin:$PATH
```

### YUM 方式安装

安装 epel 工具包 

```shell
yum install -y epel-release
```

安装 node 环境

```shell
yum install -y nodejs
```

查看版本 node 环境太低，需要更新 node 版本

```shell
[root@localhost ~]# node -v
v6.17.1
[root@localhost ~]# npm -v
3.10.10
```

安装淘宝镜像(自选安装)

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

安装  n 管理包 ( n 管理包 Github 地址： https://github.com/tj/n)

```shel
# npm 安装
npm install -g n
# cpm 安装
cnpm install -g n
```

安装指定的 node 版本

```
# 格式： n 版本号。lts 表示最新版本
n lts
```

## 测试

```
node -v
npm -v
```

