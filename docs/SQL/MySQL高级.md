# MySQL 高级

## 数据库优化

https://dev.mysql.com/doc/refman/8.0/en/optimize-overview.html

## 索引

索引：是帮助 MySQL 高效获取数据的数据结构。

|    索引    |   InnoDB引擎    | MyISAM引擎 | Memory引擎 |
| :--------: | :-------------: | :--------: | :--------: |
| BTREE索引  |      支持       |    支持    |    支持    |
|  HASH索引  |     不支持      |   不支持   |    支持    |
| R-tree索引 |     不支持      |    支持    |   不支持   |
| Full-text  | 5.6版本之后支持 |    支持    |   不支持   |

索引如果没有特别指明，都是指 B+ 树（多路搜索树，并不一定是二叉的）结构组织的索引。其中聚集索引、复合索引、前缀索引、唯一索引默认都是使用 B+Tree 索引，通称为索引

### BTREE结构

BTree 又叫做多路平衡搜索树，一颗 m 叉的 BTree 特性如下：

* 树中每个节点最多包含 m 个孩子
* 除根节点与叶子结点外，每一个节点至少有 [ceil(m/2)] 个孩子
* 若根节点不是叶子节点，则至少有两个孩子
* 所有的叶子节点都在同一层
* 每个非叶子节点由 n 个 key 与 n+1 个指针组成，其中 [ceil(m/2)-1] <= n <= m-1

### 索引分类

1. 单列索引：即一个索引只包含单个列，一个表可以有多个单列索引
2. 唯一索引：索引列的值必须是唯一的，但允许有空值
3. 复合索引：即一个索引包含多个列

### 索引语法

```sql
-- 创建表时，可以创建索引
-- 单独创建索引语法
create [unique|fulltext|spatial] index index_name on table_name(table_column, ....);
-- 查看索引
show index from table_name;
-- 删除索引
drop index index_name on table_name;


-- alter 命令添加表索引
-- 添加主键索引
alter table table_name add primary key(table_column);
-- 添加唯一索引
alter table table_name add unique index_name(table_column);
-- 添加普通索引
alter table table_name add index index_name(table_column);
-- 添加全文索引
alter table table_name add fulltext index_name(table_column);


-- 创建复合索引时： alter table table_name add index index_name(table_column1, table_column2, table_column3)
-- 相当于对 table_column1 创建索引
-- 相当于对 table_column1, table_column2 创建索引
-- 相当于对 table_column1, table_column2, table_column3 创建索引
```

### 索引设计原则

* 查询频次较高，且数据量比较大的表建立索引；
* 经常在 where 后面的
* 使用唯一索引，区分度越高，使用索引的效率越高
* 利用最左前缀法则

### 索引的使用

避免索引失效

1. 全值匹配，对索引中所有列都指定具体的值；
2. 最左前缀法则，如果是复合索引，要遵守最左前缀法则。指的是查询从索引的最左前列开始，并且不跳过索引中的其他列；和索引列的顺序没有关系，但是必须有对应的索引列；
3. 范围查询右边的列，不能使用索引；
4. 不要在列上进行运算操作，索引将失效；
5. 字符串不加单引号，造成索引失效；
6. 禁用 select * ；
7. 使用 or 分割开的条件，如果 or 前的条件中的列有索引，而后面的列中没有索引，那么涉及的索引都不会被用到；
8. 以 % 开头的 like 模糊查询，索引失效（除非使用覆盖索引，会走索引）
9. 如果 MySQL 评估使用索引比全表更慢，则不使用索引；
10. is null， is not null 有时索引失效；MySQL会根据数据量比例决定是否走索引；
11. in  走索引， not in  不走索引；
12. 尽量使用复合索引，少走单列索引；创建多个单列索引，MySQL会根据最有的单列索引查询，不会走全部索引；

## 视图

视图（view）是一种虚拟存在的表，视图并不在数据库中实际存在，行和列数据来自自定义视图的查询中使用的表，并且是在使用视图时动态生成的。通俗里讲，视图就是一条 select 语句执行后返回的结果集。

视图用于查看数据，不建议更新；

视图相对于普通表的优势

1. 简单：使用视图的用户无需关心后面对应的表的结构、关联条件和筛选条件；
2. 安全：使用视图的用户只能访问他们被允许查询的结果集，对表的权限管理并不能限制某个行某个列，但是视图可以简单实现；
3. 数据独立：一旦视图的结构确定了，可以屏蔽表结构变化对用户的影响，源表增加列对视图没有影响；

### 创建视图语法

```sql
-- 创建视图
create  view vies_name as select_statement
-- 创建或修改视图
create  or replace view vies_name as select_statement
-- 查看视图
show tables ;
-- 删除视图
drop view if exists view_name;
```

## 存储引擎

存储引擎是基于表设计的，而不是基于库设计的；

Oracle 、SqlServer 等数据库只有一种存储引擎，MySQL 提供了插件式的存储引擎架构。

```sql
-- 查询MySQL 数据库支持的存储引擎
show engines;
-- show engines 的结果：MEMORY、MRG_MYISAM、CSV、FEDERATED、PERFORMANCE_SCHEMA、MyISAM、InnoDB、BLACKHOLE、ARCHIVE
-- 其中 InnoDB 是支持事务、行级锁、外键
```

### 存储引擎的选择

在选择存储引擎时，应该根据应用系统的特点选择合适的存储引擎。对于复杂的应用系统，还可以根据实际情况选择多种存储引擎进行组合。

以下几种常用的存储弓|擎的使用环境。

* `InnoDB` ：是 `Mysql` 的默认存储引擎，用于事务处理应用程序，支持外键。如果应用对事务的完整性有比较高的要求，在并发条件下要求数据的一致性 ，数据操作除了插入和查询以外，还包含很多的更新、删除操作，那么 `InnoDB` 存储引擎是比较合适的选择。`InnoDB` 存储引擎除了有效的降低由于删除和更新导致的锁定，还可以确保事务的完整提交和回滚，对于类似于计费系统或者财务系统等对数据准确性要求比较高的系统， `InnoDB` 是最合适的选择。
* `MyISAM` ：如果应用是以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完整性、发性要求不是很高，那么选择这个存储引擎是非常合适的。
* `MEMORY` ： 将所有数据保存在内存中，在需要快速定位记录和其他类似数据环境下，可以提供几块的访问。MEMORY的缺陷就是对表的大小有限制，太大的表无法缓存在内存中，其次是要确保表的数据可以恢复，数据库异常终止后表中的数据是可以恢复的。MEMORY表通常用于更新不太频繁的小表，用以快速得到访问结果。
* `MERGE` ：用于将一系列等同的 `MyISAM` 表以逻辑方式组合在一 起，并作为-一个对象弓|用他们。`MERGE` 表的优点在于可以突破对单个 `MyISAM` 表的大小限制，并且通过将不同的表分布在多个磁盘上，可以有效的改善MERGE表的访问效率。这对于存储诸如数据仓储等 `VLDB` 环境十分合适。

### 查看 SQL 执行效率

```sql
#  查看当前连接的信息（7个_）
show status like 'Com_______'
# 查看全部连接的信息（7个_）
show global status like 'Com_______'
```

### 定位低效率的SQL语句

1. 慢查询日志：通过慢查询日志定位那些执行效率较低的 SQL 语句，用 `--log-slow-queries[=file_name]` 选项启动时，`mysqld` 写一个包含所有执行时间超过 `long_query_time` 秒的 SQL 语句的日志文件。
2. `show processlist` ：慢查询日志在查询结果以后才记录，所以在应用反映执行效率出现问题的时候查询慢查询日志不能定位问题，可以使用 `show processlist` 命令查看当前 `MySQL` 在进行的线程，包括线程的状态、是否锁表等，可以实时的查看 `SQL` 的执行情况，同时对一些锁表操作进行优化；

### explain 分析执行计划

```sql
explain select * from t_user where id = 1;
```

| id   | select_type | table  | partitions | type  | possible_keys | key     | key_len | ref   | rows | filtered | Extra |
| ---- | ----------- | ------ | ---------- | ----- | ------------- | ------- | ------- | ----- | ---- | -------- | ----- |
| 1    | SIMPLE      | t_user |            | const | PRIMARY       | PRIMARY | 4       | const | 1    | 100      |       |

explain 字段含义

|     字段      |                             含义                             |
| :-----------: | :----------------------------------------------------------: |
|      id       | 选择标识符，select 的查询序列号；如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行 |
|  select_type  | 表示 select 的类型：<br> simple（简单查询，查询中不包含子查询或者 union）、<br/>primary（查询中包含任何复杂的子部分，最外层查询标记为 primary）、<br/>subquery（在 select 或者 where 列表中包含子查询）、<br/>derived （在 from 列表中包含的子查询，被标记为 derived，MySQL会递归执行这些子查询）<br>union（若第二个 select 出现在 union 之后，则标记为 union；若 union 包含在 from 子句的子查询中，外层select 将被标记为 derived）、<br/>union result (从 union 表获取结果的 select) |
|     table     |                        输出结果集的表                        |
|  partitions   |                          匹配的分区                          |
|     type      | 表示表的连接类型，性能有好到差（null ——> system ——> const ——> eq_ref ——> ref_or_null ——> index_merge ——> index_subquery ——> range ——> index ——> all） |
| possible_keys |                  表示查询时，可能使用的索引                  |
|      key      |                      表示实际使用的索引                      |
|    key_len    |                        索引字段的长度                        |
|      ref      |                        列与索引的比较                        |
|     rows      |                   扫描行的数量(估算的行数)                   |
|   filtered    |                    按表条件过滤的行百分比                    |
|     Extra     |                     执行情况的说明和描述                     |

explain extra 字段说明

* using index : 使用覆盖索引的时候会出现；
* using where : 在查找使用索引的情况下，需要回表去查询所需要的数据；
* using index condition : 找到使用了索引，但是需要回表查询数据；
* using index ; using where : 查询使用了索引，但是需要的数据都在索引列中能找到，所以不需要回表查询；

### show profile 分析 SQL

`show profile` 能够在做 SQL 优化时帮助我们了解时间都耗费到哪里去了；通过  proflie 可以更清楚的了解 SQL 执行的过程；

```sql
-- 查看 mysql 是否支持 profile，yes 表示支持
select @@have_profiling;
-- show profile 默认是关闭的，可以通过 set 语句在 session 级别开启 profiling
select @@profiling; -- 0 表示关闭
set profiling = 1; -- 开启 profiling 


-- 
show profiles;
-- Query_ID 是通过 show profiles 查询出的结果集中的一个；
show profile all for query Query_ID;
```

### trace 分析优化器执行计划

通过 trace 文件能够进一步了解为什么优化器选择 A 计划，而不是选择 B 计划；

打开 trace ，设置格式为 JSON，并设置 trace 最大能够使用的内存大小，避免解析过程中因为默认内存过小而不能够完整展示；

```sql
set optimizer_trace="enabled=on", end_markers_in_json=on;
set optimizer_trace_max_mem_size=1000000;
```

执行分析

```sql
-- 首先需要执行sql
 select * from t_user_bill;
-- 紧接着执行 trace 分析语句
 select * from information_schema.optimizer_trace;
```

## SQL优化

* 大批量数据插入——使用主键顺序插入；
* 减少单行插入，尽可能多行插入，减少客户端 与数据库的连接次数；
* 尽量减少额外的排序，通过索引直接返回有序数据，where 和 order by 使用相同索引并且 order by 的顺序和索引顺序相同，并且 order by 的字段都是升序或者降序，否则肯定需要额外的排序操作；
* 如果查询包含 group by语句，但是用户想要避免排序结果的消耗，则可以执行 order by null 禁止排序；
* 尽可能使用多表查询替换子查询
* 建议使用 union 替换 or

MySQL 两种排序方式

* 第一种是通过对返回数据进行排序，也就是通常说的 `filesort` 排序，所有不通过索引直接返回排序结果的排序都叫 `filesort` 排序
* 第二种是通过有序索引顺序扫描直接返回有序数据，这种情况即为 `usering index` ，不需要额外排序，操作效率高；

### MySQL 并发参数设置

* `max_connections` ：空允许连接 `MySQL` 数据库的最大连接数量；一般 500~1000
* `back_log` ：控制 MySQL 监听 TCP 端口时设置的积压请求栈大小；默认 50 + (`max_connections`/5)
* `table_open_cache` ：控制所有 SQL 语句执行线程可以打开表缓存的数量；
* `thread_cache_size` ：为了加快连接数据库的速度，MySQL 会缓存一定数量的客户服务线程以备重用，通过 `thread_cahce_size` 可以控制 MySQL 缓存客户服务线程的数量；
* `innodb_lock_wait_timeout` ：该参数是用来设置 `innodb` 事务等待行锁的时间，默认为 50ms；

## MySQL 锁问题

### 锁分类

从对数据操作的粒度分：

1. 表锁：操作时，会锁定整个表；
2. 行锁：操作时，会锁定当前操作行；

从对数据操作的类型分：

1. 读锁（共享锁）：针对同一份数据，多个读操作可以同时进行而不会互相影响；
2. 写锁（排它锁）：当前操作没有完成之前，他会阻断其他写锁和读锁；

MySQL 三种锁特性：

| 锁类型 | 特点                                                         |
| :----: | ------------------------------------------------------------ |
| 表级锁 | 偏向MyISAM存储引擎，开销小，加锁快；不会出现死锁；锁定粒度大，发生锁冲突的概率最高，并发度最低； |
| 行级锁 | 偏向InnoDB存储引擎，开销大，加锁慢；会出现死锁；锁定粒度小，发生锁冲突概率最低，并发度最高； |
| 页面锁 | 开销和加锁时间介于表锁和行锁之间；会出现死锁；锁定粒度介于表锁和行锁之间，并发度一般； |

### MyISAM 表锁

MyISAM 存储引擎只支持表锁；

MyISAM 在执行查询语句之前，会自动给涉及的所有表加读锁，再执行更新操作（update、delete、insert）前会自动给涉及的表加写锁，这个过程不需要用户干预，因此，用户一般不需要直接使用 `lock table` 命令给 MyISAM 的表显示加锁；

```sql
-- 显示加锁
-- 加读锁
lock table table_name read;
-- 加写锁
lock table table_name write;
-- 解锁
unlock tables;
-- 查看锁的争用情况
show open tables;
```

### InnoDB行锁

InnoDB 无索引行锁升级为表锁，如果不通过索引条件检索数据，那么 InnoDB 将对表中的所有记录加锁；

查看当前表的索引：`show index from table_name;`

InnoDB 行锁争用情况 `show status like 'innodb_row_lock%'`

* `Innodb_row_lock_current_waits` ：当前正在等待锁的数量
* `Innodb_row_lock_time` ：从系统启动到现在锁定总时间
* `Innodb_row_lock_time_avg` ：每次等待所花平均时长
* `Innodb_row_lock_time_max` ：从系统启动到现在等待最长的一次所花的时间
* `Innodb_row_lock_waits` ：系统启动后到现在总等待次数

#### 间隙锁

当我们用范围条件，而不是使用相等条件检索数据，并请求共享锁或者排他锁时，InnoDB 会给符合条件的已有数据进行加锁；对于键值在条件范围内但并不存在的记录，叫做间隙（GAP），InnoDB 会对这个间隙加锁，这种锁机制就是间隙锁（Next-Key锁）

## SQL语句的执行顺序

```sql
SELECT DISTINCT <column_list>
FROM <table_name_1>
<join_type> JOIN <table_name_2> ON <join_condition>
WHERE <where_codition>
GROUP BY <group_by_list>
HAVING <having_condition>
ORDER BY <order_by_condition>
LIMIT {[offset,] row_count | row_count OFFSET offset_num}
```

1. FROM <table_name_1>
2. ON <join_condition>
3. <join_type> JOIN <table_name_2>
4. WHERE <where_codition>
5. GROUP BY <group_by_list>
6. SELECT DISTINCT <column_list>
7. ORDER BY <order_by_condition>
8. LIMIT {[offset,] row_count | row_count OFFSET offset_num}

## MySQL 使用正则表达式

```sql
-- select * from table_name from name regexp 正则表达式;
select * from table_name from name regexp '2$';
```

## MySQL 常用函数

数值函数

| 函数名称        | 作用                                                         |
| --------------- | ------------------------------------------------------------ |
| ABS             | 求绝对值；                                                   |
| SQRT            | 求二次方根；                                                 |
| MOD             | 求余数；                                                     |
| CEIL 和 CEILING | 两个函数功能相同，都是返回不小于参数的最小整数，即向上取整； |
| FLOOR           | 向下取整，返回值转化为一个BIGINT；                           |
| RAND            | 生成一个0~1之间的随机数，传入整数参数是，用来产生重复序列；  |
| SIGN            | 返回参数的符号；                                             |
| POW 和 POWER    | 两个函数功能相同，都是所传参数的次方的结果值；               |
| SIN             | 求正弦值；                                                   |
| ASIN            | 求反正弦值，与函数 SIN 互为反函数；                          |
| COS             | 求余弦值；                                                   |
| ACOS            | 求反余弦值，与函数 COS 互为反函数；                          |
| TAN             | 求正切值；                                                   |
| ATAN            | 求反正切值，与函数 TAN 互为反函数；                          |
| COT             | 求余切值；                                                   |

字符串函数

| 函数名称  | 作用                                                         |
| --------- | ------------------------------------------------------------ |
| LENGTH    | 计算字符串长度函数，返回字符串的字节长度；                   |
| CONCAT    | 合并字符串函数，返回结果为连接参数产生的字符串，参数可以是一个或者多个字符串； |
| INSERT    | 替换字符串函数；                                             |
| LOWER     | 将字符串中的字母转换为小写；                                 |
| UPPER     | 将字符串中的字母转换为大写；                                 |
| LEFT      | 从左侧截取字符串，返回字符串左边的若干个字符；               |
| RIGHT     | 从右侧截取字符串，返回字符串右边的若干字符；                 |
| TRIM      | 删除字符串左右两侧的空格；                                   |
| REPLACE   | 字符串替换函数，返回替换后的新字符串；                       |
| SUBSTRING | 截取字符串，返回从指定位置开始的指定长度的字符串；           |
| REVERSE   | 字符串反转函数，返回与原始字符串顺序相反的字符串；           |

聚合函数

| 函数名称 | 作用                               |
| -------- | ---------------------------------- |
| MAX      | 查询指定列的最大值；               |
| MIN      | 查询指定列的最小值；               |
| COUNT    | 统计查询结果的行数；               |
| SUM      | 求和，返回指定列的总和；           |
| AVG      | 求平均值，返回指定列数据的平均值； |

## MySQL 工具的使用

### mysqlbinlog

由于服务器生成的二进制日志文件以二进制格式保存，所以如果想要检查这些文本的格式，需要使用 mysqlbinlog 日志管理工具；

```sql
mysqlbinlog [options] log-file1 log-files2 ...

选项：
  -d, --database=name ：指定数据库名称，只列出指定的数据库相关操作；
  -o, --offset=# ： 忽略掉日志中的前n行命令；
  -r, --result-life=name ：将输出的文本格式日志输出到指定文件；
  -s, --short-form ：显示简单格式，省略掉一些信息；
  --start-datatime=date1 --stop-datetime=date2 ：指定日期间隔内的所有日志；
  --start-position=pos1 --stop-position=pos ：指定位置间隔内的所有日志；
```

mysqldump

mysqldump 客户端工具用来备份数据库或者在不同数据库之间进行数据迁移。

```sql
-- 将数据导入到 dump.file 文件中
mysqldump [options] db_name [tables] > dump.file
mysqldump [options] --database db1 db2... > dump.file
mysqldump [options] --all-databases > dump.file
```

连接选项

```
-u, --user=           指定用户名
-p, --password=       指定密码
-h, --host=			 指定服务器IP或者域名
-P, --port= 		 指定连接端口
```

