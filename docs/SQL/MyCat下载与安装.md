# MyCat下载与安装

## 前言

博客书

版本说明

```properties
linux.centos=7
windows=10
mycat.linux=Mycat-server-1.6.7.4-test-20191113141017-linux
mycat.win=Mycat-server-1.6.7.3-release-20190927161129-win
```

相关链接

* MyCat 官网：http://www.mycat.io/
* MyCat 下载地址：http://dl.mycat.io/

## 下载与安装

### Linux 

#### 下载并解压

下载并上传 Linux 服务器中，进行解压，解压即安装

```shell
tar -zxvf Mycat-server-1.6.7.4-test-20191113141017-linux.tar.gz
```

#### 简易 `conf/server.xml` 配置

编辑解压目录下 `conf/server.xml` 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mycat:server SYSTEM "server.dtd">
<mycat:server xmlns:mycat="http://io.mycat/">
	<system>
        <!-- 0为需要密码登陆、1为不需要密码登陆 ,默认为0，设置为1则需要指定默认账户-->
		<property name="nonePasswordLogin">0</property> 
		<property name="useHandshakeV10">1</property>
		<property name="useSqlStat">0</property>
		<property name="useGlobleTableCheck">0</property>
		<property name="sqlExecuteTimeout">300</property>
        <property name="sequnceHandlerType">5</property>
        <property name="sequnceHandlerPattern">(?:(\s*next\s+value\s+for\s*MYCATSEQ_(\w+))(,|\)|\s)*)+</property>
		<property name="subqueryRelationshipCheck">false</property>
        <property name="sequenceHanlderClass">io.mycat.route.sequence.handler.HttpIncrSequenceHandler</property>
        <property name="processorBufferPoolType">0</property>
        <property name="handleDistributedTransactions">0</property>
        <property name="useOffHeapForMerge">0</property>
        <property name="memoryPageSize">64k</property>
        <property name="spillsFileBufferSize">1k</property>
        <property name="useStreamOutput">0</property>
        <property name="systemReserveMemorySize">384m</property>
        <property name="useZKSwitch">false</property>
        <property name="strictTxIsolation">false</property>
        <property name="useZKSwitch">true</property>
	</system>
    <user name="root" defaultAccount="true">
		<property name="password">123456</property>
		<property name="schemas">mycat_db</property>
		<property name="defaultSchema">mycat_db</property>
    </user>
</mycat:server>
```

* system ：关于MyCAT 系统配置
* user ：关于连接 MyCAT 的配置
* firewall ：关于防火墙配置

#### 简易 `conf/schema.xml` 配置

编辑解压目录下 `conf/schema.xml` 配置文件

```xml
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">

	<schema name="mycat_db" checkSQLschema="false" sqlMaxLimit="100">
		<table name="address" dataNode="dn1,dn2,dn3" rule="mod-long" primaryKey="id"/>
	</schema>
	
	<dataNode name="dn1" dataHost="db_3306" database="db1" />
	<dataNode name="dn2" dataHost="db_3307" database="db2" />
	<dataNode name="dn3" dataHost="db_3308" database="db3" />
	
	<dataHost name="db_3306" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="mysql" dbDriver="native" switchType="1"  slaveThreshold="100">
		<heartbeat>select user()</heartbeat>
		<writeHost host="hostM1" url="192.168.8.4:3306" user="root" password="mysqlMYSQL*8"></writeHost>
	</dataHost>
	<dataHost name="db_3307" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="mysql" dbDriver="native" switchType="1"  slaveThreshold="100">
		<heartbeat>select user()</heartbeat>
		<writeHost host="hostM2" url="192.168.8.4:3307" user="root" password="mysqlMYSQL*8"></writeHost>
	</dataHost>
	<dataHost name="db_3308" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="mysql" dbDriver="native" switchType="1"  slaveThreshold="100">
		<heartbeat>select user()</heartbeat>
		<writeHost host="hostM3" url="192.168.8.4:3308" user="root" password="mysqlMYSQL*8"></writeHost>
	</dataHost>
</mycat:schema>
```

* schema ：关于逻辑库的配置
* dataNode ：关于数据节点配置
* dataHost ：关于远程数据库的连接配置

#### 命令

```shell
# 启动
./mycat start
# 停止
./mycat stop
# 前台运行
./mycat console
# 重启服务
./mycat restart
# 查看启动状态
./mycat status
```

### Windows-10

#### 解压即安装

#### 配置

简易配置如上

#### 启动

双击 `bin/startup_nowrap.bat` 即运行（如果出现闪退，使用cmd命令运行，排查异常），默认端口为 `8066` 端口，端口可通过配置 `conf/server.xml` 配置



