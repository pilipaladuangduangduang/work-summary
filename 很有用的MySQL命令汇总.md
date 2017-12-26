# MySQL命令汇总

标签（空格分隔）： 工作总结

---

## 很有用MySQL命令汇总：

### 查询某个字段属于哪张表

```
select * from `information_schema`.columns where column_name='字段名';
```

### 查询没有 primary key 或 unique key 的表

```
SELECT 
  t.TABLE_SCHEMA,
  t.TABLE_NAME,
  ENGINE 
FROM
  information_schema.TABLES t 
  INNER JOIN information_schema.COLUMNS c 
    ON t.TABLE_SCHEMA = c.TABLE_SCHEMA 
    AND t.TABLE_NAME = c.TABLE_NAME 
    AND t.TABLE_SCHEMA NOT IN (
      'performance_schema',
      'information_schema',
      'mysql'
    ) 
GROUP BY t.TABLE_SCHEMA,
  t.TABLE_NAME 
HAVING SUM(IF(column_key IN ('PRI', 'UNI'), 1, 0)) = 0;
```

### 查询库中的表，根据表大小来排序
```
SELECT 
  TABLE_NAME,
  table_rows,
  data_length,
  index_length,
  ROUND(
    (
      (data_length + index_length) / 1024 / 1024
    ),
    2
  ) "Size in MB" 
FROM
  information_schema.TABLES 
WHERE table_schema = "your table name" 
ORDER BY (data_length + index_length) DESC;
```

### mysqldump导出导入数据
```
mysqldump -h 192.168.x.xxx -P 3306 -uroot -proot dbname tbname > filename
mysql -uroot -proot dbname < filename
```

### 拷贝表和数据
```
CREATE TABLE table_copy LIKE table;
INSERT INTO table_copy SELECT * FROM table;
```

### MySQL插入时数据存在就更新，不存在就插入模型

```
# 表中唯一索引字段重复就更新数据
INSERT INTO z_res_book_file (project_name, project_id, screen, size, create_time, url) 
VALUES
  (#projectName#, #projectId#, #screen#, #size#, now(), #url#)
  ON DUPLICATE KEY 
  UPDATE 
    url = #url#,
    size = #size#
``` 
