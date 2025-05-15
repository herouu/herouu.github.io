### mysql安装

```bash
docker run -itd --restart=always -p 3306:3306 \
--name mysql \
-v /home/herouu/dev/mysql/conf:/etc/mysql/conf \
-v /home/herouu/dev/mysql/data:/var/lib/mysql \
-v /home/herouu/dev/mysql/logs:/logs \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:latest
```

### mysql主从配置

* mysql master

```bash
docker run -itd --restart=always -p 3406:3306 \
--name mysql-master \
-v /root/dev/mysql/conf:/etc/mysql/conf \
-v /root/dev/mysql/data:/var/lib/mysql \
-v /root/dev/mysql/logs:/logs \
-v /root/dev/mysql/my.cnf:/etc/my.cnf \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:8.3.0
```

* master my.cnf

```text
[mysqld]
## 同一局域网内注意要唯一
server-id=100
## 开启二进制日志功能，可以随便取（关键）
log-bin=mysql-bin
```

* mysql slave

```bash
docker run -itd --restart=always -p 3416:3306 \
--name mysql-slave \
-v /root/dev/mysql-slave/conf:/etc/mysql/conf \
-v /root/dev/mysql-slave/data:/var/lib/mysql \
-v /root/dev/mysql-slave/logs:/logs \
-v /root/dev/mysql-slave/my.cnf:/etc/my.cnf \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:8.3.0
```

* slave my.cnf

```text
[mysqld]
## 设置server_id,注意要唯一
server-id=101
## relay_log配置中继日志
relay_log=mysql-relay-bin
```

```text
# master节点
show master status;

+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000003 |      158 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+

# slave节点  
# master_log_file对应File字段值,master_log_pos对应Position字段值

CHANGE MASTER TO master_host = '172.17.0.1',
master_user = 'root',
master_password = '123456',
master_port = 3406,
master_log_file = 'mysql-bin.000003', 
master_log_pos = 158,
master_connect_retry = 30;


show slave status;

# 启动、停止从节点
start slave;
stop slave      
```

### performance sql

* 查询mysql版本

```sql
select version()
```

* 查看事务隔离级别

```sql
#
1.查看当前会话隔离级别
select @@transaction_isolation;
#
2.查看系统当前隔离级别
select @@global.transaction_isolation;
```

* 按客户端IP分组,看哪个客户端的链接数最多

```sql
select client_ip, count(client_ip) as client_num
from (select substring_index(host, ':', 1) as client_ip from INFORMATION_SCHEMA.processlist) as connect_info
group by client_ip
order by client_num desc;
```

* processlist

```sql
--查看正在执行的线程，并按 Time 倒排序
select *
from information_schema.processlist
where Command != 'Sleep'
order by time desc;

--找出所有执行时间超过 5 分钟的线程，拼凑出 kill 语句，方便后面查杀
select concat('kill ', id, ';')
from information_schema.processlist
where Command != 'Sleep' and Time > 300
order by Time desc;

```

* 查看正在锁的事务

```sql
SELECT *
FROM INFORMATION_SCHEMA.INNODB_TRX
order by trx_started;
kill
trx_mysql_thread_id列
```

```bash
Waiting for table metadata lock
```

* 数据库问题排查

```sql
-- 查看锁等待时间
show
variables like 'innodb_lock_wait_timeout';  
-- 设置锁等待时间
set
innodb_lock_wait_timeout=600;
--注意global的修改对当前线程是不生效的，只有建立新的连接才生效
set
global innodb_lock_wait_timeout=600;
```

```sql
-- 模拟 
-- 事务1
begin;
update tt
set name='步骤一'
where id = 1;

-- 事务2
begin;
select *
from tt
where id = 1;
```

```sql
--排查问题核心表

-- 事务表
select *
from information_schema.innodb_trx;
-- 锁等待表
select *
from sys.innodb_lock_waits;

-- PROCESSLIST_ID与mysql thread_id关系表
select *
from performance_schema.threads;

-- mysql8锁信息表
select *
from performance_schema.data_locks;

-- 历史语句事件表
select *
from performance_schema.events_statements_history;
select *
from performance_schema.events_statements_current;

```

```sql
-- 长事务造成的Lock wait timeout exceeded
SELECT dl.*,
       trx.trx_query,
       ilw.blocking_pid,
       ilw.sql_kill_blocking_connection,
       t2.THREAD_ID block_thread_id
FROM `performance_schema`.`data_locks` dl
         INNER JOIN `performance_schema`.`threads` t ON t.thread_id = dl.thread_id
         LEFT JOIN `information_schema`.`innodb_trx` trx ON t.processlist_id = trx.trx_mysql_thread_id
         LEFT JOIN `sys`.`innodb_lock_waits` ilw ON ilw.waiting_pid = t.processlist_id
         LEFT JOIN `performance_schema`.`threads` t2 ON t2.processlist_id = ilw.blocking_pid
WHERE trx.trx_state = 'LOCK WAIT'
  AND dl.object_schema = 'schema_name'
ORDER BY t.processlist_time DESC;

-- 查询长事务sql 
select *
from performance_schema.events_statements_history
where thread_id = #{block_thread_id};

```

```sql
-- 在另外一个会话（连接）里面，查询这个超过10秒未提交事务的详细信息：

SELECT t.trx_mysql_thread_id,
       t.trx_state,
       t.trx_tables_in_use,
       t.trx_tables_locked,
       t.trx_query,
       t.trx_rows_locked,
       t.trx_rows_modified,
       t.trx_lock_structs,
       t.trx_started,
       t.trx_isolation_level,
       p.TIME,
       p.USER,
       p.HOST,
       p.db,
       p.command
FROM information_schema.innodb_trx t
         INNER JOIN information_schema.PROCESSLIST p ON t.trx_mysql_thread_id = p.id
WHERE t.trx_state = 'RUNNING'
  AND p.TIME > 10
  AND p.command = 'Sleep';
```

* 修改字符集
    * 修改库字符集

       ```sql
      SELECT
          concat( 'alter database ', schema_name, ' default character set utf8mb4 collate utf8mb4_0900_ai_ci;' )
      FROM
          information_schema.schemata
      WHERE
          schema_name IN ( 'dbName' )
          AND lower( default_collation_name ) IN ( 'utf8mb4_unicode_ci' );
      ```

    * 修改表字符集

      ```sql
      SELECT
          concat( 'alter table ', table_schema, '.', table_name, ' default character set utf8mb4 collate = utf8mb4_0900_ai_ci;' )
      FROM
          information_schema.TABLES
      WHERE
          table_schema IN ( 'dbName' )
          AND table_type = 'BASE TABLE'
          AND lower( table_collation ) IN ( 'utf8mb4_unicode_ci' );
      ```

    * 修改表中字段字符集

      ```sql
      SELECT
          concat(
          concat( 'alter table ', t1.table_schema, '.', t1.table_name ),
          concat(
          ' modify ','`',t1.column_name,'` ',
          t1.data_type,
          IF
          ( t1.data_type IN ( 'varchar', 'char' ), concat( '(', t1.character_maximum_length, ')' ), '' ),
          ' character set utf8mb4 collate utf8mb4_0900_ai_ci',
          IF
          ( t1.is_nullable = 'NO', ' not null', ' null' ),
          ' comment ',
          '''',
          t1.column_comment,
          ''';'
          )) alter_sql
      FROM
          information_schema.COLUMNS t1
      WHERE
          lower( t1.collation_name ) IN ( 'utf8mb4_unicode_ci' )
          AND t1.table_schema IN (
          'dbName'
          )
      ```

* 表行数排名

```sql
SELECT TABLE_SCHEMA, TABLE_NAME, TABLE_ROWS
FROM information_schema.TABLES
ORDER BY TABLE_ROWS DESC LIMIT 100;
```

参考: <https://www.lanmper.cn/>

* 计算表的行平均大小

```sql
--avg_row_length 单位字节,B
SELECT table_name,
       table_rows,
       avg_row_length,
       (data_length / table_rows) AS actual_avg_row_length
FROM information_schema.TABLES
WHERE table_schema = 'table_schema'
ORDER BY table_rows DESC
```

* 计算单表的性能瓶颈数据量

```vim

B+Tree中的叶子节点存放的是数据，而一个数据页只有16K，
我们「假设：数据页中页目录，页头，页尾加起来总共占用1KB，剩余15KB全部用了存放数据」

1.将B+tree的高度定义为N  
2.非叶子节点的数据页存储数量为X，也就是有X个数据页的页号  
3.叶子节点的数据页存储数据为Y  

根据以上定义，B+tree存储的数据总量：

「M = (X ^ (N-1)) * Y」  

主键类型会影响行数，「假设主键类型为bigint类型，占8个字节，
而在InnoDB源码中页号  （FIL_PAGE_OFFSET）被设置为4字节」。
则此时非叶子节点能存储的数据量为:

「X = 15 * 1024 / (8+4) = 1280」  

假设叶子节点中存储的数据，每条的大小都为1KB，即每个数据页存储15条数据  

「Y  = 15」  

「三层B+tree的数据量（N=3）」  
「M = (X ^ (N-1)) * Y  = (1280 ^ (3-1)) * 15 = 24579000条」  

实际上表每条数据大小，可以根据上节【计算表的行平均大小】的sql进行统计，
假如每条实际大小为130B  
则：  

「Y  = 15 * 1024 / 130 ≈ 118.15」

「三层B+tree的数据量（N=3）」  

「M = (X ^ (N-1)) * Y  = (1280 ^ (3-1)) * 118.15 = 193576960条 ≈ 1.9亿条」
```

参考： <https://cloud.tencent.com/developer/article/2123136>

* 主从同步

[Mysql主从同步的实现原理与配置实战](https://zhuanlan.zhihu.com/p/89796383)

* 优化
    * 分区
    * 分库分表
    * 读写分离

      [【sql】MySQL-ProxySQL中间件的使用](https://zhuanlan.zhihu.com/p/110733834)

4.冷热分离

[技术分享：数据冷热分离](https://juejin.cn/post/6844903960474714125)

5.分布式数据库

[TiDB](tidb.net/book)

* 离线数据同步

1.DataX,需提前备份表结构

```json

{
  "job": {
    "content": [
      {
        "reader": {
          "name": "mysqlreader",
          "parameter": {
            "column": [
              "*"
            ],
            "connection": [
              {
                "jdbcUrl": [
                  "jdbc:mysql://192.168.1.130:3306/cloud-demo?useSSL=false&useUnicode=true&characterEncoding=utf8&yearIsDateType=false&zeroDateTimeBehavior=convertToNull&tinyInt1isBit=false&rewriteBatchedStatements=true"
                ],
                "table": [
                  "sys_menu"
                ]
              }
            ],
            "password": "123456",
            "username": "root",
            "where": ""
          }
        },
        "writer": {
          "name": "mysqlwriter",
          "parameter": {
            "column": [
              "*"
            ],
            "connection": [
              {
                "jdbcUrl": "jdbc:mysql://192.168.1.130:3306/cloud-demo-bak?useSSL=false&useUnicode=true&characterEncoding=utf8&yearIsDateType=false&zeroDateTimeBehavior=convertToNull&tinyInt1isBit=false&rewriteBatchedStatements=true",
                "table": [
                  "sys_menu"
                ]
              }
            ],
            "password": "123456",
            "preSql": [],
            "session": [],
            "username": "root",
            "writeMode": "replace"
          }
        }
      }
    ],
    "setting": {
      "speed": {
        "channel": "1"
      }
    }
  }
}
```

```python
python bin/datax.py job/bak.json
```

2.mydumper-myloader

备份

```bash
mydumper -h XXX.XXX.XXX.XXX -P 3306 -u root -p 123456 -B database_name -T table_name -o backup/xxx

```

恢复

```bash
myloader -h XXX.XXX.XXX.XXX -P 3306 -u root -p 123456 -B database_name -o -d backup/xxx
```

* 实时数据同步/CDC（Change Data Capture）

    1. Canal
    2. Flink CDC

* 运维工具

<https://github.com/hcymysql>

mysqlstat 工具挺好使
