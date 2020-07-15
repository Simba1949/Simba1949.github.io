# DataX学习笔记

## 前言

版本说明

```properties
mysql=8.0.21
mysql-connector-java=8.0.19
python=2.7.18
```

相关链接：

* DataX Github 地址：https://github.com/alibaba/DataX
* DataX 详细介绍：https://github.com/alibaba/DataX/blob/master/introduction.md
* DataX 官方快速上手教程：https://github.com/alibaba/DataX/blob/master/userGuid.md

## DataX 官方介绍

DataX 详细介绍：https://github.com/alibaba/DataX/blob/master/introduction.md

## DataX 框架设计

![DataxX架构设计](img/DataxX架构设计.png)

`DataX` 本身作为离线数据同步框架，采用 `Framework + plugin` 架构构建。将数据源读取和写入抽象成为 `Reader/Writer` 插件，纳入到整个同步框架中。

- **Reader：** `Reader` 为数据采集模块，负责采集数据源的数据，将数据发送给 `Framework` 。
- **Writer：** `Writer` 为数据写入模块，负责不断向 `Framework`  取数据，并将数据写入到目的端。
- **Framework：** `Framework` 用于连接 `reader`  和 `writer`  ，作为两者的数据传输通道，并处理缓冲，流控，并发，数据转换等核心技术问题。

## DataX 核心架构

![DataX核心架构](img/DataX核心架构.png)

### 核心模块介绍：

1. `DataX` 完成单个数据同步的作业，我们称之为 `Job` ，``DataX` 接受到一个 `Job` 之后，将启动一个进程来完成整个作业同步过程。`DataX Job` 模块是单个作业的中枢管理节点，承担了数据清理、子任务切分(将单一作业计算转化为多个子 `Task` )、`TaskGroup` 管理等功能。
2. `DataX Job` 启动后，会根据不同的源端切分策略，将 `Job` 切分成多个小的 `Task` (子任务)，以便于并发执行。`Task` 便是 `DataX` 作业的最小单元，每一个 `Task` 都会负责一部分数据的同步工作。
3. 切分多个 `Task` 之后，``DataX Job` 会调用 `Scheduler` 模块，根据配置的并发数据量，将拆分成的 `Task` 重新组合，组装成 `TaskGroup` (任务组)。每一个 `TaskGroup` 负责以一定的并发运行完毕分配好的所有 `Task` ，默认单个任务组的并发数量为 5。
4. 每一个 Task 都由 `TaskGroup` 负责启动，`Task` 启动后，会固定启动 `Reader—>Channel—>Writer` 的线程来完成任务同步工作。
5. `DataX` 作业运行起来之后， `Job` 监控并等待多个 `TaskGroup` 模块任务完成，等待所有 `TaskGroup` 任务完成后 `Job` 成功退出。否则，异常退出，进程退出值非 0。

## 工具下载

推荐在 `DataX` 官方仓库直接下载工具包，下载源码编译会有问题；官方推荐使用 `python2.6.X`，测试使用 `python2.7.18`

## 数据迁移实践

1. 初始化 `job.json` 配置文件；
2. 编辑 `job.json` 配置文件；
3. 启动 `DataX` 工具；

### 测试数据库

```sql
CREATE TABLE `t_user`  (
  `id` int(0) NOT NULL,
  `name` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;

INSERT INTO `t_user` VALUES (1, 'li-bai');
INSERT INTO `t_user` VALUES (2, 'du-fu');
INSERT INTO `t_user` VALUES (3, 'bai-ju-yi');
INSERT INTO `t_user` VALUES (4, 'wang-wei');
INSERT INTO `t_user` VALUES (5, 'li-zhong-sheng');
```

### 初始化 job.json 配置文件

```shell
# python datax.py -r {YOUR_READER} -w {YOUR_WRITER}
python datax.py -r MysqlReader -w MysqlWriter 
```

### 编辑 job.json 配置文件

```json
{
  "job": {
    "content": [
      {
        "reader": {
          "name": "mysqlreader",
          "parameter": {
            "column": ["id", "`name`"],
            "connection": [
              {
                "jdbcUrl": ["jdbc:mysql://127.0.0.1:3306/db_datax_1?serverTimezone=UTC"],
                "table": ["t_user"]
              }
            ],
            "password": "123456",
            "username": "root"
          }
        },
        "writer": {
          "name": "mysqlwriter",
          "parameter": {
            "column": ["id", "`name`"],
            "connection": [
              {
                "jdbcUrl": "jdbc:mysql://127.0.0.1:3306/db_datax_2?serverTimezone=UTC&useUnicode=true&characterEncoding=utf8",
                "table": ["t_user"]
              }
            ],
            "password": "123456",
            "username": "root",
            "writeMode": "insert"
          }
        }
      }
    ],
    "setting": {
      "speed": {
        "channel": "5"
      }
    }
  }
}
```

配置文件说明：在官方 `Github` 主要的 `Support Data Channels` 项下表格的文档中；

注意事项：

1. 数据库版本问题，测试使用数据是 `MySQL 8.0.21`，需要使用8的驱动，需要更换插件的驱动版本号；
2. `column` 必须指定不能为空，不能配置常量值，而且需要注意是否是数据库关键字，关键字需要使用 \`\` 括起来；
3. `MySQL 8.0.21` 版本连接需要设置时区 `serverTimezone=UTC` ；
4. `characterEncoding` 中不支持 `utf8mb4` ，可以使用 `utf8` ；
5. `writeMode` 是必选项，模板中没有生成，但是存在默认值（`insert`）；

### 启动 DataX 工具

```shell
python datax.py job.json
```

## DataX 受限约束

```
Q: MysqlWriter 执行 postSql 语句报错，那么数据导入到目标数据库了吗?
A: DataX 导入过程存在三块逻辑，pre操作、导入操作、post操作，其中任意一环报错，DataX 作业报错。由于 DataX 不能保证在同一个事务完成上述几个操作，因此有可能数据已经落入到目标端。

Q: 按照上述说法，那么有部分脏数据导入数据库，如果影响到线上数据库怎么办?
A: 目前有两种解法，第一种配置 pre 语句，该 sql 可以清理当天导入数据， DataX 每次导入时候可以把上次清理干净并导入完整数据。第二种，向临时表导入数据，完成后再 rename 到线上表。

Q: 上面第二种方法可以避免对线上数据造成影响，那我具体怎样操作?
A: 可以配置临时表导入
```



