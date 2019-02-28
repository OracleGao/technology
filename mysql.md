# Mysql

## 统计数据存储的磁盘消耗情况
- 全局统计索引和数据磁盘消耗
``` sql
SELECT 
    CONCAT(ROUND(SUM(index_length) / (1024 * 1024), 2),
            ' MB') AS 'Total Index Size',
    CONCAT(ROUND(SUM(data_length) / (1024 * 1024), 2),
            ' MB') AS 'Total Data Size'
FROM
    information_schema.TABLES
WHERE
    table_schema LIKE '%test%';
```
- 按表统计索引和数据磁盘消耗
``` sql
SELECT 
    CONCAT(table_schema, '.', table_name) AS 'Table Name',
    CONCAT(ROUND(table_rows / 1000000, 2), 'M') AS 'Number of Rows',
    CONCAT(ROUND(data_length / (1024 * 1024 * 1024), 2),
            'G') AS 'Data Size',
    CONCAT(ROUND(index_length / (1024 * 1024 * 1024), 2),
            'G') AS 'Index Size',
    CONCAT(ROUND((data_length + index_length) / (1024 * 1024 * 1024),
                    2),
            'G') AS 'Total'
FROM
    information_schema.TABLES
WHERE
    table_schema LIKE '%test%';
```
### Refs
- [MYSQL查询数据库表索引的硬盘空间占用](https://www.cnblogs.com/hubing/p/3365664.html)

## 修改Mysql root密码
``` sql
use mysql; 
update user set authentication_string=password('changeit') where user='root' and host='%'; 
flush privileges;
```
