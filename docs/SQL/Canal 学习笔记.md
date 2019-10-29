# Canal 学习笔记

## 前言

canal github 链接：https://github.com/alibaba/canal

canal 官方文档地址：https://github.com/alibaba/canal/wiki

canal 下载地址：https://github.com/alibaba/canal/releases

数据监控 github 地址：https://github.com/chenqian56131/spring-boot-starter-canal

## MySQL主备复制原理

![MySQL主备复制原理](https://github.com/simba1949/images/blob/master/SQL/MySQL%E4%B8%BB%E5%A4%87%E5%A4%8D%E5%88%B6%E5%8E%9F%E7%90%86.png?raw=true)

- 中记录叫做二进制日志事件 binary log events，可以通过 show binlog events 进行查看)
- MySQL slave 将 master 的 binary log events 拷贝到它的中继日志(relay log)
- MySQL slave 重放 relay log 中事件，将数据变更反映它自己的数据

## canal 工作原理

- canal 模拟 MySQL slave 的交互协议，伪装自己为 MySQL slave ，向 MySQL master 发送 dump 协议
- MySQL master 收到 dump 请求，开始推送 binary log 给 slave (即 canal )
- canal 解析 binary log 对象(原始为 byte 流)

## MySQL 准备

> 备注：docker 运行的 mysql 不需要配置 binlog

### 开启 Binlog

对于自建 MySQL , 需要先开启 Binlog 写入功能，配置 binlog-format 为 ROW 模式，my.cnf 中配置如下

```
[mysqld]
log-bin=mysql-bin # 开启 binlog
binlog-format=ROW # 选择 ROW 模式
server_id=1 # 配置 MySQL replaction 需要定义，不要和 canal 的 slaveId 重复，作为唯一标识
```

### 授权

授权 canal 链接 MySQL 账号具有作为 MySQL slave 的权限, 如果已有账户可直接 grant

```sql
CREATE USER canal IDENTIFIED BY 'canal';  
GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'canal'@'%';
-- GRANT ALL PRIVILEGES ON *.* TO 'canal'@'%' ;
FLUSH PRIVILEGES;
```

## Canal-Deployer 

docker 地址：https://hub.docker.com/r/canal/canal-server

### Docker 安装

```
# 下载
docker pull canal/canal-server
# 运行
docker run -di --name canal-server -p 11111:11111 canal/canal-server
```

### Tar 包安装

**下载与安装**

下载地址：https://github.com/alibaba/canal/releases

下载并解压 canal.deployer-1.1.4.tar.gz

```
tar -zxvf canal.deployer-1.1.4.tar.gz  -C /usr/local/app/canal/deployer
```

**目录结构如下**

```
drwxr-xr-x. 2 root root   76 Oct 10 22:33 bin
drwxr-xr-x. 5 root root  123 Oct 10 22:33 conf
drwxr-xr-x. 2 root root 4096 Oct 10 22:33 lib
drwxrwxrwx. 2 root root    6 Sep  2 03:26 logs
```

### 配置

canal.properties 配置

```properties
# canal.id 唯一标识，不能重复
canal.id = 192
```

同步配置，conf/example/instance.properties 文件进行配置

```properties
## mysql serverId
canal.instance.mysql.slaveId = 1234
#position info，需要改成自己的数据库信息，主数据库的ip和port
canal.instance.master.address = 192.168.8.4:3306 
canal.instance.master.journal.name = 
canal.instance.master.position = 
canal.instance.master.timestamp = 
#canal.instance.standby.address = 
#canal.instance.standby.journal.name =
#canal.instance.standby.position = 
#canal.instance.standby.timestamp = 
#username/password，需要改成自己的数据库信息，主数据库账户密码
canal.instance.dbUsername = root  
canal.instance.dbPassword = 123456
canal.instance.defaultDatabaseName = 
canal.instance.connectionCharset = UTF-8
#table regex，.\* 表示所有数据库，.\\\\..\* 表示所有表, .\*\\\\..\* 表示所有库表
canal.instance.filter.regex = .\*\\\\..\*
# 过滤指定库表
# canal.instance.filter.regex = changgou_content.\*,changgou_goods.\*

# mq config——外部（自定义的微服务）监听，需要指定 topic 名称
canal.mq.topic=example
# dynamic topic route by schema or table regex
#canal.mq.dynamicTopic=mytest1.user,mytest2\\..*,.*\\..*
canal.mq.partition=0
# hash partition config
#canal.mq.partitionsNum=3
#canal.mq.partitionHash=test.table:id^name,.*\\..*
```

- canal.instance.connectionCharset 代表数据库的编码方式对应到 Java 中的编码类型
- 如果系统是1个 cpu，需要将 canal.instance.parser.parallel 设置为 false

#### 命令

```
# 启动命令
sh bin/startup.sh
# 关闭命令
sh bin/stop.sh
```

#### 日志

* 查看 server 日志 ：logs/canal/canal.log
* 查看 instance 的日志 ：logs/example/example.log

### 数据监控

数据监控 github 地址：https://github.com/chenqian56131/spring-boot-starter-canal

将 starter-canal 安装到本地 maven 仓库中（温馨提示：可以通过 idea 导入项目后，执行 clean compile package install 即可安装到 maven 仓库中）

### 入门项目

#### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>changgou-service</artifactId>
        <groupId>com.changgou</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>changgou-service-canal</artifactId>

    <dependencies>
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.xpand</groupId>
            <artifactId>starter-canal</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>
</project>
```

#### application.yml

```yaml
server:
  port: 18083
spring:
  application:
    name: canal
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:7001/eureka
  instance:
    prefer-ip-address: true
feign:
  hystrix:
    enabled: true

# canal 配置
canal:
  client:
    instances:
      # example 是 canal 配置的 topic 名
      example:
        host: 192.168.8.5
        port: 11111
```

#### 启动类

```java
package com.changgou;

import com.xpand.starter.canal.annotation.EnableCanalClient;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

/**
 * @EnableCanalClient 开启 canal 客户端
 * 
 * @Author Theodore
 * @Date 2019/10/11 15:17
 */
@EnableCanalClient
@EnableEurekaClient
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
public class CanalApplication {
    public static void main(String[] args) {
        SpringApplication.run(CanalApplication.class, args);
    }
}
```

#### 监听类

```java
package com.changgou.canal;

import com.alibaba.otter.canal.protocol.CanalEntry;
import com.xpand.starter.canal.annotation.*;

import java.util.List;

/**
 * @CanalEventListener 监听 canal
 *
 * @Author Theodore
 * @Date 2019/10/11 15:25
 */
@CanalEventListener
public class CanalDataEventListener {
    /**
     * @InsertListenPoint : 增加数据监听，只有增加后的数据：
     * rowData.getAfterColumnsList() : 适用于增加、修改操作
     * rowData.getBeforeColumnsList() : 适用于删除、修改操作
     * @param eventType ：当前操作的类型，插入数据操作
     * @param rowData ：发生变更的一行数据记录
     */
    @InsertListenPoint
    public void onEventInsert(CanalEntry.EventType eventType, CanalEntry.RowData rowData){
        for (CanalEntry.Column column : rowData.getAfterColumnsList()) {
            System.out.println("列名：" + column.getName() + "\t插入后对应的值：" + column.getValue());
        }
        // 获取数据后可以对数据做下一步处理
    }

    /**
     * @UpdateListenPoint 修改数据监听，
     * @param eventType 当前操作的类型，插入数据操作
     * @param rowData 发生变更的一行数据记录
     */
    @UpdateListenPoint
    public void onEventUpdate(CanalEntry.EventType eventType, CanalEntry.RowData rowData){
        List<CanalEntry.Column> beforeColumnsList = rowData.getBeforeColumnsList();
        for (CanalEntry.Column column : beforeColumnsList) {
            System.out.println("列名：" + column.getName() + "\t修改前对应的值：" + column.getValue());
        }
        List<CanalEntry.Column> afterColumnsList = rowData.getAfterColumnsList();
        for (CanalEntry.Column column : afterColumnsList) {
            System.out.println("列名：" + column.getName() + "\t修改后对应的值：" + column.getValue());
        }
    }

    /**
     * @DeleteListenPoint 删除数据监听
     * @param eventType
     * @param rowData
     */
    @DeleteListenPoint
    public void onEventDelete(CanalEntry.EventType eventType, CanalEntry.RowData rowData){
        String name = eventType.getValueDescriptor().getName();
        System.out.println("name: " + name);


        List<CanalEntry.Column> beforeColumnsList = rowData.getBeforeColumnsList();
        for (CanalEntry.Column column : beforeColumnsList) {
            System.out.println("列名：" + column.getName() + "\t删除前对应的值：" + column.getValue());
        }
    }

    /**
     * 自定义监听
     * 
     * eventType = {CanalEntry.EventType.DELETE, CanalEntry.EventType.UPDATE} // 监听操作的类型
     * schema = {"changgou_content"} // 指定监听的schema，mysql 没有schema之分，相当于 mysql数据库
     * table = {"tb_content"} // 指定监听哪些表
     * destination = "example" // 监听的 topic
     * @param eventType
     * @param rowData
     */
    @ListenPoint(
            eventType = {CanalEntry.EventType.DELETE, CanalEntry.EventType.UPDATE},
            schema = {"changgou_content"},
            table = {"tb_content"},
            destination = "example"
    )
    public void onEventDiy(CanalEntry.EventType eventType, CanalEntry.RowData rowData){
        List<CanalEntry.Column> beforeColumnsList = rowData.getBeforeColumnsList();
        for (CanalEntry.Column column : beforeColumnsList) {
            System.out.println("列名：" + column.getName() + "\t修改前对应的值：" + column.getValue());
        }
        List<CanalEntry.Column> afterColumnsList = rowData.getAfterColumnsList();
        for (CanalEntry.Column column : afterColumnsList) {
            System.out.println("列名：" + column.getName() + "\t修改后对应的值：" + column.getValue());
        }
    }
}
```





