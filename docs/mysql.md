
* 按客户端IP分组,看哪个客户端的链接数最多

```sql
select client_ip,count(client_ip) as client_num from (select substring_index(host,':' ,1) as client_ip from INFORMATION_SCHEMA.processlist ) as connect_info group by client_ip order by client_num desc;
```

* processlist

```sql
# 查看正在执行的线程，并按 Time 倒排序
select * from information_schema.processlist where Command != 'Sleep' order by time desc;

# 找出所有执行时间超过 5 分钟的线程，拼凑出 kill 语句，方便后面查杀
select concat('kill ', id, ';') from information_schema.processlist where Command != 'Sleep' and Time > 300 order by Time desc;

```

* 查看正在锁的事务

```sql
SELECT * FROM INFORMATION_SCHEMA.INNODB_TRX order by trx_started;
kill trx_mysql_thread_id列
```

```bash
Waiting for table metadata lock
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
SELECT TABLE_SCHEMA, TABLE_NAME, TABLE_ROWS FROM information_schema.TABLES ORDER BY TABLE_ROWS DESC LIMIT 100;
```

参考: <https://www.lanmper.cn/>

* 计算表的行平均大小

```sql
# avg_row_length 单位字节,B
SELECT
 table_name,
 table_rows,
 avg_row_length,
 ( data_length / table_rows ) AS actual_avg_row_length 
FROM
 information_schema.TABLES 
WHERE
 table_schema = 'table_schema' 
ORDER BY
 table_rows DESC
```

* 计算单表的性能瓶颈数据量

```text

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
                        "column": ["*"],
                        "connection": [
                            {
                                "jdbcUrl": ["jdbc:mysql://192.168.1.130:3306/cloud-demo?useSSL=false&useUnicode=true&characterEncoding=utf8&yearIsDateType=false&zeroDateTimeBehavior=convertToNull&tinyInt1isBit=false&rewriteBatchedStatements=true"],
                                "table": ["sys_menu"]
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
                        "column": ["*"],
                        "connection": [
                            {
                                "jdbcUrl": "jdbc:mysql://192.168.1.130:3306/cloud-demo-bak?useSSL=false&useUnicode=true&characterEncoding=utf8&yearIsDateType=false&zeroDateTimeBehavior=convertToNull&tinyInt1isBit=false&rewriteBatchedStatements=true",
                                "table": ["sys_menu"]
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
