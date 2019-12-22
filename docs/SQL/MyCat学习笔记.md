# MyCat学习笔记

## 前言

博客书

版本说明

```properties
linux.centos=7
mycat.linux=Mycat-server-1.6.7.4-test-20191113141017-linux
```

相关链接

- MyCat 官网：http://www.mycat.io/
- MyCat 下载地址：http://dl.mycat.io/

## 实战演练及配置说明

下载安装后，进入 `conf` 目录下进行配置

### `server.xml` 配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- - - Licensed under the Apache License, Version 2.0 (the "License"); 
	- you may not use this file except in compliance with the License. - You 
	may obtain a copy of the License at - - http://www.apache.org/licenses/LICENSE-2.0 
	- - Unless required by applicable law or agreed to in writing, software - 
	distributed under the License is distributed on an "AS IS" BASIS, - WITHOUT 
	WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. - See the 
	License for the specific language governing permissions and - limitations 
	under the License. -->
<!DOCTYPE mycat:server SYSTEM "server.dtd">
<mycat:server xmlns:mycat="http://io.mycat/">
	<system>
		<!-- 0为需要密码登陆、1为不需要密码登陆 ,默认为0，设置为1则需要指定默认账户-->
		<property name="nonePasswordLogin">0</property> 
		<property name="useHandshakeV10">1</property>
		<!-- 1为开启实时统计、0为关闭 -->
		<property name="useSqlStat">0</property>
		<!-- 1为开启全加班一致性检测、0为关闭 -->
		<property name="useGlobleTableCheck">0</property>
		<!-- SQL 执行超时 单位:秒-->
		<property name="sqlExecuteTimeout">300</property>
		<!--sequnceHandlerType：0表示本地方式，1表示数据库方式，2表示时间戳，3表示zk生成的id，4表示zk递增的id，5表示-->
		<property name="sequnceHandlerType">0</property>
		<!--必须带有MYCATSEQ_或者 mycatseq_进入序列匹配流程 注意MYCATSEQ_有空格的情况-->
		<property name="sequnceHandlerPattern">(?:(\s*next\s+value\s+for\s*MYCATSEQ_(\w+))(,|\)|\s)*)+</property>
		<!-- 子查询中存在关联查询的情况下,检查关联字段中是否有分片字段 .默认 false -->
		<property name="subqueryRelationshipCheck">false</property>
		<property name="sequenceHanlderClass">io.mycat.route.sequence.handler.HttpIncrSequenceHandler</property>
		<!--1为开启mysql压缩协议-->
		<!--<property name="useCompression">1</property>-->
		<!--设置模拟的MySQL版本号-->
		<!--<property name="fakeMySQLVersion">5.6.20</property>-->
		<!--<property name="processorBufferChunk">40960</property> -->
		<!--<property name="processors">1</property> -->
		<!--<property name="processorExecutor">32</property>  -->
        <!--默认为type 0: DirectByteBufferPool | type 1 ByteBufferArena | type 2 NettyBufferPool -->
		<property name="processorBufferPoolType">0</property>
		<!--默认是65535 64K 用于sql解析时最大文本长度 -->
		<!--<property name="maxStringLiteralLength">65535</property>-->
		<!--<property name="sequnceHandlerType">0</property>-->
		<!--<property name="backSocketNoDelay">1</property>-->
		<!--<property name="frontSocketNoDelay">1</property>-->
		<!--<property name="processorExecutor">16</property>-->
		<!--
			<property name="serverPort">8066</property> 
			<property name="managerPort">9066</property> 
			<property name="idleTimeout">300000</property> 
			<property name="bindIp">0.0.0.0</property>
			// 5 * 60 * 1000L; //连接空闲检查
			<property name="dataNodeIdleCheckPeriod">300000</property> 
			<property name="frontWriteQueueSize">4096</property> 
			<property name="processors">32</property> 
		-->
		<!--分布式事务开关，0为不过滤分布式事务，1为过滤分布式事务（如果分布式事务内只涉及全局表，则不过滤），2为不过滤分布式事务,但是记录分布式事务日志-->
		<property name="handleDistributedTransactions">0</property>
		<!--off heap for merge/order/group/limit   1开启   0关闭-->
		<property name="useOffHeapForMerge">0</property>
		<!--单位为m-->
        <property name="memoryPageSize">64k</property>
		<!--单位为k-->
		<property name="spillsFileBufferSize">1k</property>
		<property name="useStreamOutput">0</property>
		<!--单位为m-->
		<property name="systemReserveMemorySize">384m</property>
		<!--是否采用zookeeper协调切换  -->
		<property name="useZKSwitch">false</property>
		<!-- XA Recovery Log日志路径 -->
		<!--<property name="XARecoveryLogBaseDir">./</property>-->
		<!-- XA Recovery Log日志名称 -->
		<!--<property name="XARecoveryLogBaseName">tmlog</property>-->
		<!--如果为 true的话 严格遵守隔离级别,不会在仅仅只有select语句的时候在事务中切换连接-->
		<property name="strictTxIsolation">false</property>
		<property name="useZKSwitch">true</property>
	</system>
	
	<!-- 全局SQL防火墙设置 -->
	<!--白名单可以使用通配符%或着*-->
	<!--例如<host host="127.0.0.*" user="root"/>-->
	<!--例如<host host="127.0.*" user="root"/>-->
	<!--例如<host host="127.*" user="root"/>-->
	<!--例如<host host="1*7.*" user="root"/>-->
	<!--这些配置情况下对于127.0.0.1都能以root账户登录-->
	<!--
	<firewall>
	   <whitehost>
	      <host host="1*7.0.0.*" user="root"/>
	   </whitehost>
       <blacklist check="false">
       </blacklist>
	</firewall>
	-->

	<user name="root" defaultAccount="true">
		<property name="password">123456</property>
		<property name="schemas">mycat_db</property>
		<property name="defaultSchema">mycat_db</property>
		<!--No MyCAT Database selected 错误前会尝试使用该schema作为schema，不设置则为null,报错 -->
		
		<!-- 表级 DML 权限设置 -->
		<!-- 		
		<privileges check="false">
			dml四位数从左向右表示insert、update、select、delete权限设置
			<schema name="TESTDB" dml="0110" >
				<table name="tb01" dml="0000"></table>
				<table name="tb02" dml="1111"></table>
			</schema>
		</privileges>		
		 -->
	</user>

	<user name="user">
		<property name="password">user</property>
		<property name="schemas">mycat_db</property>
		<property name="readOnly">true</property>
		<property name="defaultSchema">mycat_db</property>
	</user>

</mycat:server>
```

* system ：表示 MyCat 系统的相关配置
* firewall ： 表示 MyCat 系统的防火墙配置
* user ： 表示连接 MyCat 用户的相关配置

### `schema.xml` 配置

```xml
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">

	<schema name="mycat_db" checkSQLschema="false" sqlMaxLimit="100">
		<!--splitTableNames 启用<table name 属性使用逗号分割配置多个表-->
		<!--name表示真实数据库表的名称；dataNode表示节点配置；rule分库分表规则:具体规则参考./rule.xml文件；rule="mod-long"是取模分片，需要在rule.xml配置节点数-->
		<table name="user" dataNode="dn1,dn2,dn3" rule="mod-long" splitTableNames ="true" primaryKey="id" autoIncrement="true"/>
	</schema>
	
	<!-- dataHost="localhost1"表示引用下面的dataHost节点名称；database="admin" 表示是真实数据库名称 -->
	<dataNode name="dn1" dataHost="localhost1" database="admin" />
	<dataNode name="dn2" dataHost="localhost2" database="admin" />
	<dataNode name="dn3" dataHost="localhost3" database="admin" />
		
	<!--
		name="localhost1" 表示 dataHost名称；
		maxCon="1000" 表示最大连接数；
		balance="0"表示不负载均衡，1表示负载均衡；
		writeType="0"表示；
		dbType="mysql"数据库类型；
		dbDriver="native"表示使用本地数据库驱动；
		switchType="1" 表示
		slaveThreshold="100" 表示
	-->
	<dataHost name="localhost1" maxCon="1000" minCon="10" balance="1" writeType="0" dbType="mysql" dbDriver="native" switchType="1" slaveThreshold="100">
		<!--heartbeat 标签代表Mycat需要对物理库心跳检测的语句-->
		<heartbeat>select user()</heartbeat>
		<!-- 写数据的节点配置 -->
		<writeHost host="localhost1" url="192.168.8.4:3306" user="root" password="mysqlMYSQL*8">
			<!-- 读数据的节点配置-->
			<readHost host="localhost4" url="192.168.8.4:3309" user="root" password="mysqlMYSQL*8"></readHost>
		</writeHost>
	</dataHost>
	
	<dataHost name="localhost2" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="mysql" dbDriver="native" switchType="1"  slaveThreshold="100">
		<!--heartbeat 标签代表Mycat需要对物理库心跳检测的语句-->
		<heartbeat>select user()</heartbeat>
		<!-- 写数据的节点配置 -->
		<writeHost host="localhost2" url="192.168.8.4:3307" user="root" password="mysqlMYSQL*8"></writeHost>
	</dataHost>
	
	<dataHost name="localhost3" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="mysql" dbDriver="native" switchType="1"  slaveThreshold="100">
		<!--heartbeat 标签代表Mycat需要对物理库心跳检测的语句-->
		<heartbeat>select user()</heartbeat>
		<!-- 写数据的节点配置 -->
		<writeHost host="localhost3" url="192.168.8.4:3308" user="root" password="mysqlMYSQL*8"></writeHost>
	</dataHost>
</mycat:schema>
```

* schema ： 表示逻辑库配置
* dataNode ：  表示数据节点配置
* dataHost ：  表示数据库的配置信息

### `rule.xml` 配置

`rule.xml` 配置文件用于分库分表分片规则的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- - - Licensed under the Apache License, Version 2.0 (the "License"); 
	- you may not use this file except in compliance with the License. - You 
	may obtain a copy of the License at - - http://www.apache.org/licenses/LICENSE-2.0 
	- - Unless required by applicable law or agreed to in writing, software - 
	distributed under the License is distributed on an "AS IS" BASIS, - WITHOUT 
	WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. - See the 
	License for the specific language governing permissions and - limitations 
	under the License. -->
<!DOCTYPE mycat:rule SYSTEM "rule.dtd">
<mycat:rule xmlns:mycat="http://io.mycat/">
	<tableRule name="rule1">
		<rule>
			<columns>id</columns>
			<algorithm>func1</algorithm>
		</rule>
	</tableRule>

	<tableRule name="sharding-by-date">
		<rule>
			<columns>createTime</columns>
			<algorithm>partbyday</algorithm>
		</rule>
	</tableRule>

	<tableRule name="rule2">
		<rule>
			<columns>user_id</columns>
			<algorithm>func1</algorithm>
		</rule>
	</tableRule>

	<tableRule name="sharding-by-intfile">
		<rule>
			<columns>sharding_id</columns>
			<algorithm>hash-int</algorithm>
		</rule>
	</tableRule>
	<tableRule name="auto-sharding-long">
		<rule>
			<columns>id</columns>
			<algorithm>rang-long</algorithm>
		</rule>
	</tableRule>
	
	<tableRule name="mod-long">
		<rule>
			<columns>id</columns>
			<algorithm>mod-long</algorithm>
		</rule>
	</tableRule>
	<tableRule name="sharding-by-murmur">
		<rule>
			<columns>id</columns>
			<algorithm>murmur</algorithm>
		</rule>
	</tableRule>
	<tableRule name="crc32slot">
		<rule>
			<columns>id</columns>
			<algorithm>crc32slot</algorithm>
		</rule>
	</tableRule>
	<tableRule name="sharding-by-month">
		<rule>
			<columns>create_time</columns>
			<algorithm>partbymonth</algorithm>
		</rule>
	</tableRule>
	<tableRule name="latest-month-calldate">
		<rule>
			<columns>calldate</columns>
			<algorithm>latestMonth</algorithm>
		</rule>
	</tableRule>

	<tableRule name="auto-sharding-rang-mod">
		<rule>
			<columns>id</columns>
			<algorithm>rang-mod</algorithm>
		</rule>
	</tableRule>

	<tableRule name="jch">
		<rule>
			<columns>id</columns>
			<algorithm>jump-consistent-hash</algorithm>
		</rule>
	</tableRule>

	<function name="murmur" class="io.mycat.route.function.PartitionByMurmurHash">
		<!-- 默认是0 -->
		<property name="seed">0</property>
		<!-- 要分片的数据库节点数量，必须指定，否则没法分片 -->
		<property name="count">3</property>
		<!-- 一个实际的数据库节点被映射为这么多虚拟节点，默认是160倍，也就是虚拟节点数是物理节点数的160倍 -->
		<property name="virtualBucketTimes">160</property>
		<!-- <property name="weightMapFile">weightMapFile</property> 节点的权重，没有指定权重的节点默认是1。以properties文件的格式填写，以从0开始到count-1的整数值也就是节点索引为key，以节点权重值为值。所有权重值必须是正整数，否则以1代替 -->
		<!-- 
			用于测试时观察各物理节点与虚拟节点的分布情况，如果指定了这个属性，会把虚拟节点的murmur hash值与物理节点的映射按行输出到这个文件，没有默认值，如果不指定，就不会输出任何东西 
			<property name="bucketMapPath">/etc/mycat/bucketMapPath</property>
		-->
	</function>

	<function name="crc32slot" class="io.mycat.route.function.PartitionByCRC32PreSlot">
		<!-- 要分片的数据库节点数量，必须指定，否则没法分片 -->
		<property name="count">3</property>
	</function>
	<function name="hash-int" class="io.mycat.route.function.PartitionByFileMap">
		<property name="mapFile">partition-hash-int.txt</property>
	</function>
	<function name="rang-long" class="io.mycat.route.function.AutoPartitionByLong">
		<property name="mapFile">autopartition-long.txt</property>
	</function>
	<function name="mod-long" class="io.mycat.route.function.PartitionByMod">
		<!-- how many data nodes -->
		<property name="count">3</property>
	</function>

	<function name="func1" class="io.mycat.route.function.PartitionByLong">
		<property name="partitionCount">8</property>
		<property name="partitionLength">128</property>
	</function>
	<function name="latestMonth" class="io.mycat.route.function.LatestMonthPartion">
		<property name="splitOneDay">24</property>
	</function>
	<function name="partbymonth" class="io.mycat.route.function.PartitionByMonth">
		<property name="dateFormat">yyyy-MM-dd</property>
		<property name="sBeginDate">2015-01-01</property>
	</function>

	<function name="partbyday" class="io.mycat.route.function.PartitionByDate">
		<property name="dateFormat">yyyy-MM-dd</property>
		<property name="sNaturalDay">0</property>
		<property name="sBeginDate">2014-01-01</property>
		<property name="sEndDate">2014-01-31</property>
		<property name="sPartionDay">10</property>
	</function>

	<function name="rang-mod" class="io.mycat.route.function.PartitionByRangeMod">
		<property name="mapFile">partition-range-mod.txt</property>
	</function>

	<function name="jump-consistent-hash" class="io.mycat.route.function.PartitionByJumpConsistentHash">
		<property name="totalBuckets">3</property>
	</function>
</mycat:rule>
```

### `sequence_conf.properties` 配置

```properties
#default global sequence
#HISIDS表示历史id，
GLOBAL.HISIDS=
# 最小id
GLOBAL.MINID=10001
# 最大id
GLOBAL.MAXID=20000
# 当前id
GLOBAL.CURID=10000

# 序列可以自定义，只能自定义前缀和对应的数值
COMPANY.HISIDS=
COMPANY.MINID=1001
COMPANY.MAXID=2000
COMPANY.CURID=1000

CUSTOMER.HISIDS=
CUSTOMER.MINID=1001
CUSTOMER.MAXID=2000
CUSTOMER.CURID=1000

ORDER.HISIDS=
ORDER.MINID=1001
ORDER.MAXID=2000
ORDER.CURID=1000

HOTNEWS.HISIDS=
HOTNEWS.MINID=1001
HOTNEWS.MAXID=2000
HOTNEWS.CURID=1000

USERID.HISIDS=
USERID.MINID=0
USERID.MAXID=20000
USERID.CURID=0
```

### `sequence_db_conf.properties` 配置

配置哪些节点使用哪些序列规则

```properties
#sequence stored in datanode
GLOBAL=dn1,dn2,dn3
COMPANY=dn1
CUSTOMER=dn1
ORDERS=dn1
USERID=dn1,dn2,dn3
```

### 命令

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

### 连接

使用 `native` 连接 `MyCat` ，和连接 `MySQL` 没有区别，`MyCat` 默认端口为 8066；

序列号的语法符合标准SQL规范，其格式为： `next value for MYCATSEQ_XXX` ，示例如下：

```sql
-- id插入的值使用next value for MYCATSEQ_GLOBAL，其中GLOBAL对应的是sequence_conf.properties文件配置的前缀
insert into user(id, username, password) values(next value for MYCATSEQ_GLOBAL, '李白', '123456');
```

## 全局序列号——主键生成策略

### 本地文件方式`0`

#### 原理

此方式 MyCAT 将 sequence 配置到文件中，当使用到 sequence 中的配置后，MyCAT 会更下
classpath 中的 sequence_conf.properties 文件中 sequence 当前的值。

#### 配置方式

`server.xml` 配置

```xml
<system>
    <property name="sequnceHandlerType">0</property>
</system>
```

* 注：sequnceHandlerType 需要配置为 0，表示使用本地文件方式。

`sequence_conf.properties` 配置

```properties
# HISIDS 表示使用过的历史分段(一般无特殊需要可不配置)
GLOBAL_SEQ.HISIDS=
# MINID 表示最小 ID 值
GLOBAL_SEQ.MINID=1001
# MAXID 表示最大 ID 值
GLOBAL_SEQ.MAXID=1000000000
# CURID 表示当前 ID 值
GLOBAL_SEQ.CURID=1000
```

#### 使用示例

```sql
insert into table1(id,name) values(next value for MYCATSEQ_GLOBAL,'test');
```

#### 优缺点

缺点：当 MyCAT 重新发布后，配置文件中的 sequence 会恢复到初始值。
优点：本地加载，读取速度较快。

### 数据库方式`1`

#### 原理

在数据库中建立一张表，存放 sequence 名称(name)，sequence 当前值(current_value)，步长(increment int 类型每次读取多少个 sequence，假设为 K)等信息；

Sequence 获取步骤：

1. 当初次使用该 sequence 时，根据传入的 sequence 名称，从数据库这张表中读取 current_value，和
   increment 到 MyCat 中，并将数据库中的 current_value 设置为原 current_value 值+increment 值。
2. MyCat 将读取到 current_value+increment 作为本次要使用的 sequence 值，下次使用时，自动加 1，当
   使用 increment 次后，执行步骤 1)相同的操作。
3. MyCat 负责维护这张表，用到哪些 sequence，只需要在这张表中插入一条记录即可。若某次读取的
   sequence 没有用完，系统就停掉了，则这次读取的 sequence 剩余值不会再使用。

#### 配置方式

`server.xml` 配置

```xml
<system>
    <property name="sequnceHandlerType">1</property>
</system>
```

* 注：sequnceHandlerType 需要配置为 1，表示使用数据库方式生成 sequence。

数据库配置

序列表 `MYCAT_SEQUENCE` 和下面三个函数需要创建在同一个节点上

1. 创建 MYCAT_SEQUENCE 表，用于存放 sequence 

   ```sql
   -- name 表示 sequence 名称
   -- current_value 表示当前 value
   -- increment 表示增长步长
   CREATE TABLE MYCAT_SEQUENCE (
       name VARCHAR(50) NOT NULL COMMENT 'sequence 名称',
       current_value INT NOT NULL COMMENT '当前 value',
       increment INT NOT NULL DEFAULT 100 COMMENT '增长步长', 
       PRIMARY KEY(name)
   ) ENGINE=InnoDB;
   
   -- 插入
   INSERT INTO MYCAT_SEQUENCE(name,current_value,increment) VALUES ('GLOBAL', 20, 1);
   ```

2. 创建相关 function

   * 命令行创建

     ```sql
     –- 获取当前 sequence 的值 (返回当前值,增量)
     DROP FUNCTION IF EXISTS mycat_seq_currval;
     
     DELIMITER $$
     CREATE FUNCTION mycat_seq_currval(seq_name VARCHAR(50)) 
     RETURNS varchar(64) CHARSET utf8
     DETERMINISTIC
     BEGIN
     DECLARE retval VARCHAR(64);
     SET retval="-999999999,null";
     SELECT concat(CAST(current_value AS CHAR),",",CAST(increment AS CHAR)) INTO retval FROM
     MYCAT_SEQUENCE WHERE name = seq_name;
     RETURN retval;
     END $$
     
     –- 设置 sequence 值
     DROP FUNCTION IF EXISTS mycat_seq_setval;
     
     DELIMITER $$
     CREATE FUNCTION mycat_seq_setval(seq_name VARCHAR(50),value INTEGER) 
     RETURNS varchar(64) CHARSET utf8
     DETERMINISTIC
     BEGIN
     UPDATE MYCAT_SEQUENCE
     SET current_value = value
     WHERE name = seq_name;
     RETURN mycat_seq_currval(seq_name);
     END $$
     
     –- 获取下一个 sequence 值
     DROP FUNCTION IF EXISTS mycat_seq_nextval;
     
     DELIMITER $$
     CREATE FUNCTION mycat_seq_nextval(seq_name VARCHAR(50)) 
     RETURNS varchar(64) CHARSET utf8
     DETERMINISTIC
     BEGIN
     UPDATE MYCAT_SEQUENCE
     SET current_value = current_value + increment 
     WHERE name = seq_name;
     RETURN mycat_seq_currval(seq_name);
     END $$
     ```

   * 使用Navicat创建语法

     ```sql
     –- 获取当前 sequence 的值 (返回当前值,增量)
     DROP FUNCTION IF EXISTS mycat_seq_currval;
     CREATE FUNCTION mycat_seq_currval(seq_name VARCHAR(50)) RETURNS varchar(64) CHARSET utf8
     DETERMINISTIC
     BEGIN
     	DECLARE retval VARCHAR(64);
     	SET retval="-999999999,null";
     	SELECT concat(CAST(current_value AS CHAR),",",CAST(increment AS CHAR)) INTO retval 
     	FROM MYCAT_SEQUENCE WHERE name = seq_name;
     	RETURN retval;
     END
     
     –- 设置 sequence 值
     DROP FUNCTION IF EXISTS mycat_seq_setval;
     CREATE FUNCTION mycat_seq_setval(seq_name VARCHAR(50),value INTEGER) RETURNS varchar(64) CHARSET utf8
     DETERMINISTIC
     BEGIN
     	UPDATE MYCAT_SEQUENCE
     	SET current_value = value
     	WHERE name = seq_name;
     	RETURN mycat_seq_currval(seq_name);
     END
     
     –- 获取下一个 sequence 值
     DROP FUNCTION IF EXISTS mycat_seq_nextval;
     CREATE FUNCTION mycat_seq_nextval(seq_name VARCHAR(50)) 
     RETURNS varchar(64) CHARSET utf8
     DETERMINISTIC
     BEGIN
     	UPDATE MYCAT_SEQUENCE
     	SET current_value = current_value + increment 
     	WHERE name = seq_name;
     	RETURN mycat_seq_currval(seq_name);
     END
     ```

3. 配置 `server.xml`

   ```xml
   <system>
       <!--在 server.xml 中配置数据库方式1-->
    	<property name="sequnceHandlerType">1</property>
       <!--其他配置略-->
   </system>
   ```

5. 配置 `schema.xml` ，将序列表添加到逻辑库中

   ```xml
   <schema name="mycat_db" checkSQLschema="false" sqlMaxLimit="100">
       <table name="user" dataNode="dn1,dn2,dn3" rule="mod-long" splitTableNames ="true" primaryKey="id" autoIncrement="true"/>
       <!---->
       <table name="MYCAT_SEQUENCE" dataNode="dn1" primaryKey="name"/>
   </schema>
   ```

5. 配置 `sequence_db_conf.properties`，定义序列表存储在哪个节点上

   ```properties
   #sequence stored in datanode
   GLOBAL=dn1
   ```

#### 使用示例

```sql
insert into table1(id,name) values(next value for MYCATSEQ_GLOBAL,"test")；
```

### 本地时间戳方式`2`

#### 原理

ID= 64位二进制 (42(毫秒)+5(机器 ID)+5(业务编码)+12(重复累加)，换算成十进制为 18 位数的 long 类型，每毫秒可以并发 12 位二进制的累加。

#### 注意

***由于时间戳长度为18为数，需要保证主键长度大于等于18***

#### 配置方式

`server.xml` 配置

```xml
<system>
    <property name="sequnceHandlerType">2</property>
</system>
```

* 注：sequnceHandlerType 需要配置为 2，表示使用数据库方式生成 sequence。

`schema.xml` 配置

```xml
<!--必须添加 autoIncrement="true" -->
<table name="user" dataNode="dn1,dn2,dn3" rule="mod-long" splitTableNames ="true" primaryKey="id" autoIncrement="true" />
```

`sequence_time_conf.properties` 配置

```properties
# 多个个 mycat 节点下每个 mycat 配置的 WORKID，DATAACENTERID 不同，组成唯一标识，总共支持 32*32=1024 种组合。
# 任意整数(0-31)
WORKID=0
# 任意整数(0-31)
DATAACENTERID=0
```

#### 使用示例

```sql
insert into user(username, password) values("tu1", 'tp1');
```

### 分布式`Zookeeper` ID 生成器`3`

#### 原理

ID 结构：long 64 位，ID 最大可占 63 位，一共 63 位可以承受单机房单机器单线程 1000*(2^6)=640000 的并发。

* current time millis(微秒时间戳 38 位,可以使用 17 年)
* clusterId（机房或者 ZKid，通过配置文件配置 5位）
* instanceId（实例 ID，可以通过 ZK 或者配置文件获取，5 位）|threadId（线程 ID，9 位）
* increment(自增,6 位)

无悲观锁，无强竞争，吞吐量更高；

配置文件：`sequence_distributed_conf.properties`配置 INSTANCEID=ZK 就是从 ZK 上获取InstanceID。

#### 配置方式

`server.xml` 配置

```xml
<system>
    <property name="sequnceHandlerType">3</property>
</system>
```

* 注：sequnceHandlerType 需要配置为 3，表示使用数据库方式生成 sequence。

`myid.properties` 配置 ZK

Zk 的连接信息统一在 `myid.properties` 的 zkURL 属性中配置。

#### 使用示例

```sql
insert into user(username, password) values("zu0", 'zp0');
```

### `Zookeeper` 递增方式`4`

#### 配置方式

`server.xml` 配置

```xml
<system>
    <property name="sequnceHandlerType">4</property>
</system>
```

* 注：sequnceHandlerType 需要配置为 4，表示使用数据库方式生成 sequence。

`myid.properties` 配置 ZK

Zk 的连接信息统一在 `myid.properties` 的 zkURL 属性中配置。

zookeeper 实现递增序列号

* 配置文件：sequence_conf.properties
* 只要配置好 ZK 地址和表名的如下属性
* TABLE.MINID 某线程当前区间内最小值
* TABLE.MAXID 某线程当前区间内最大值
* TABLE.CURID 某线程当前区间内当前值
* 文件配置的 MAXID 以及 MINID 决定每次取得区间，这个对于每个线程或者进程都有效
* 文件中的这三个属性配置只对第一个进程的第一个线程有效，其他线程和进程会动态读取 ZK

#### 使用示例

```sql
insert into user(id, username, password) values(next value for mycatseq_GLOBAL,"Zu2", 'Zp2');
```





