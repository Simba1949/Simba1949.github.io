# Springboot项目Jar注册到Centos服务

## 前言

博客书

版本说明

```properties
linux.centos=7
springboot=2.2.0.RELEASE
```

相关链接：

* CentOS 官网：https://www.centos.org/

## 注册服务配置

### 创建

进入 `/etc/rc.d/init.d` 目录下，创建和服务名相同的文件( 文件名不可以带后缀".sh")，并授权可执行

```shell
cd /etc/rc.d/init.d
touch pangu
chmod a+x pangu
```

### 配置 `pangu` 文件

```shell
#!/bin/bash

# jar 包所在的目录
project_dir=/opt/server/pangu
history_dir="history"
project_name=pangu

# JDK 的根目录
JAVA_HOME=/usr/local/ide/jdk/jdk1.8.0_221
PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin

# 执行项
start()
{
	setsid java -jar $project_dir/pangu.jar
    echo "service start pangu success"
}

stop()
{
    ps -ef | grep ${project_name} | grep -v grep | awk '{print $2}' | xargs kill -9
	echo "service stop pangu success"
}

case "$1" in
    start)
    start
    ;;
    stop)
    stop
    ;;
    *)
    echo $"Usage: $0 {start|stop}"
    RETVAL=1
esac
exit $RETVAL
```

### 执行

```shell
# 开启 pangu 服务
service pangu start
# 关闭 pangu 服务
service pangu stop
```

