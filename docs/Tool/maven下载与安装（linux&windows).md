# maven下载与安装(linux&windows)

## 前言

博客书

版本说明

```properties
maven=3.6.2
linux=7.7.1908
windows=10
```

相关网址

* maven 官网：https://maven.apache.org/
* maven 下载地址：https://maven.apache.org/download.cgi
* maven 中央仓库地址：https://mvnrepository.com

## Linux 下 TAR 包安装

**前提：依赖于 Java 环境，需要先按照配置 Java 环境**

上传并解压

```shell
tar -zxvf apache-maven-3.6.2-bin.tar.gz
```

在 apache-maven-3.6.2/conf 目录下的 settings.xml 配置文件，配置 maven 仓库地址

```
<localRepository>/usr/local/ide/maven/repository</localRepository>
```

配置 maven 系统环境变量

编辑` /etc/profile` ，在` /etc/profile` 最后一行添加如下内容，执行 `source /etc/profile` 重新加载系统环境变量。

```
# maven MAVEN_HOME 是 maven 安装（解压）目录
export MAVEN_HOME=/usr/local/ide/maven/apache-maven-3.6.2
export PATH=$MAVEN_HOME/bin:$PATH
```

测试

```
# 查看 maven 版本，显示即安装成功
mvn -v
```

## Windows 下安装

解压 maven ，在 `apache-maven-3.6.2/conf` 目录下的 `settings.xml` 配置文件，配置 maven 仓库地址

```
<localRepository>D:\Maven\Repository</localRepository>
```

配置 windows 系统环境变量

右击我的电脑——>属性——>高级系统设置——>环境变量

新建

```
# 变量名固定，变量值是maven解压安装目录
变量名： MAVEN_HOME
变量值： D:\Maven\apache-maven-3.6.1
```

编辑 path，在最后一行如下内容

```
# 第一种方式，直接找到 maven 安装目录下的 bin目录
D:\Maven\apache-maven-3.6.1\bin
# 第二种方式，通过配合的 MAVEN_HOME 变量配置
%MAVEN_HOME%\bin
```





