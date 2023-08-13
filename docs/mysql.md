
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

* 表行数排名

```sql
SELECT TABLE_SCHEMA, TABLE_NAME, TABLE_ROWS FROM information_schema.TABLES ORDER BY TABLE_ROWS DESC LIMIT 100;
```

参考: <https://www.lanmper.cn/>

* 备份

```bash
mydumper -h XXX.XXX.XXX.XXX -P 3306 -u root -p 123456 -B database_name -T table_name -o backup/xxx

```

* 恢复

```bash
myloader -h XXX.XXX.XXX.XXX -P 3306 -u root -p 123456 -B database_name -o -d backup/xxx
```