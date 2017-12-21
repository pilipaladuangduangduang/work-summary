# MySQL工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和MySQL相关的内容：

### 笛卡尔积过大

> 数据库查询两张没有关联的表时，此时的笛卡尔积很大，会占据极大的内存，禁止这样做。

### 查询某个字段属于哪张表

> MySQL中如何查询某个字段在哪张表中：先 use information_schema; 然后 select * from columns where column_name='字段名'; 这样一来就可以查询出包含该字段的表了。

### 返回新增一条数据时该数据的主键

``` mysql
// 加上last_insert_id()就可以返回新增数据的主键了
insert语句 select last_insert_id()
```

### 显示表中字段的字符集并修改

``` sql
// 1、先显示出表中所有列的字段属性，包含字段字符集
SHOW FULL COLUMNS FROM `database`.`table`;
// 2、修改你想要修改的字段字符集
ALTER TABLE `database`.`table` MODIFY `column` VARCHAR(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### MySQL中查询NULL

``` 
// 查询MySQL中为NULL的数据
select * from xxx where xxx_column is null;
``` 

### MySQL中 select count() 和 group by

今天又一个SQL想要查询出分组数据的总个数，如下：

``` 
// 查询的总条数还是分组前的数据
select count(*) from XXX group by xxx
// 查询的总条数是分组后的数据，需要先查询分组的数据封装到一个临时表对象中
select count(*) from (select xxx from XXX group by xxx) temp
``` 

### MySQL日期存储字段类型为bigint

当MySQL表中日期存储字段的类型设为bigint（相当于Java中的Long类型）时，此时在做插入或更新时间操作时不能再SQL语句中使用NOW()函数，而是在程序中使用<font color="FF2D2D">System.currentTimeMillis()</font>作为时间字段的值，否则在前端展示具体时间时会出现BUG。

### MySQL定时事件 + 存储过程实现定时建表

最近在实现一个需求，就是记录每天来自App的请求数、来自不同渠道的请求数、来自不同版本的请求数，由于数据量很大，这些数据不可能都塞到一张表里，所以需要每天都定时建表，所幸MySQL已经有这个功能了，步骤：

 1. 首先建立存储过程，在存储过程中创建表、创建表的索引等；

 2. 然后建立定时事件，在事件中调用存储过程来达到实现定时建表的功能。

具体的存储过程：

``` 
DELIMITER $$

USE `xingbook-log`$$

DROP PROCEDURE IF EXISTS `prc_app_request_channel_increment_table`$$

CREATE DEFINER=`root`@`%` PROCEDURE `prc_app_request_channel_increment_table`(in_num INT)
BEGIN
# 定义sql的变量
DECLARE  t_sql VARCHAR(4000);
# 定义索引的变量
DECLARE  t_idx_1 VARCHAR(4000);
DECLARE  t_idx_2 VARCHAR(4000);
DECLARE  t_idx_3 VARCHAR(4000);
# 定义时间的变量
DECLARE  t_date DATE;
DECLARE  t_partion VARCHAR(300);
DECLARE  table_part VARCHAR(50);
DECLARE  num INT;
SET num=0;
SET t_date= DATE_ADD(NOW(), INTERVAL 0 DAY);
WHILE num < in_num DO
SELECT DATE_FORMAT (t_date,'%Y%m%d') INTO t_partion;
SET table_part=CONCAT('app_request_channel_increment_', t_partion);
SELECT COUNT(1) FROM information_schema.tables WHERE table_schema='xingbook-log' AND table_name = table_part INTO @cnt;
IF @cnt = 0 THEN
    SET t_sql= CONCAT('create table ',table_part,
'(',
'  `data_record_time` varchar(32) DEFAULT NULL,', 
'  `channel` varchar(16) DEFAULT NULL,', 
'  `count` int(11) DEFAULT NULL', 
')ENGINE=InnoDB DEFAULT CHARSET=utf8;'
  );

# 建索引   
SET t_idx_1 = CONCAT('create index app_request_channel_increment_data_record_time_idx_', t_partion, ' on app_request_channel_increment_', t_partion, ' (data_record_time)');
SET t_idx_2 = CONCAT('create index app_request_channel_increment_channel_idx_', t_partion, ' on app_request_channel_increment_', t_partion, ' (channel)');
SET t_idx_3 = CONCAT('create index app_request_channel_increment_count_idx_', t_partion, ' on app_request_channel_increment_', t_partion, ' (count)');
SET @sql=t_sql;
PREPARE s1 FROM @sql;
EXECUTE s1;
DEALLOCATE PREPARE s1;
SET @sql=t_idx_1;
PREPARE s1 FROM @sql;
EXECUTE s1;
DEALLOCATE PREPARE s1;
SET @sql=t_idx_2;
PREPARE s1 FROM @sql;
EXECUTE s1;
DEALLOCATE PREPARE s1;
SET @sql=t_idx_3;
PREPARE s1 FROM @sql;
EXECUTE s1;
DEALLOCATE PREPARE s1;
END IF;
SET num=num+1;
SELECT DATE_ADD(NOW(), INTERVAL num DAY) INTO t_date;
END WHILE;
END$$

DELIMITER ;
``` 

具体的定时事件：

``` 
# 具体事件来调用（CALL）存储过程
CREATE DEFINER=`root`@`%` EVENT `prc_app_request_channel_increment_table定时任务` ON SCHEDULE EVERY 5 DAY STARTS '2016-05-26 10:53:48' ON COMPLETION NOT PRESERVE ENABLE DO CALL prc_app_request_channel_increment_table(7)

``` 

### MySQL中FIND_IN_SET函数

``` 
// 查询name列中包含nie的数据
WHERE FIND_IN_SET('nie', name)
``` 

### MySQL的行级锁

只有MySQL的InnoDB引擎才支持~

``` 
<!-- where中有主键才会锁行,否则会锁表，id对应的数据不存在不会锁表 -->
select id from z_group_topic where is_deleted = 0 and id = #resId# for update
``` 

### 关于数据库update的原子操作

 > update table set col = col + 1 这条SQL语句已经是原子操作了。

### MySQL中union之取并集操作

 > 之前不知道MySQL早已提供了这个功能，对两个数据集做并集操作都是在程序中实现的，在程序中实现需要查询两次MySQL，相比之下MySQL的unino只需要一次就OK。不过有一点需要注意，进行union操作的两个SQL语句中的<font color="FF2D2D">查询的列要完全一样</font>。
 
### MySQL中union和union all的区别

 > union会去除重复的数据，union all不会去除重复的数据。
 
### MySQL插入时数据存在就更新，不存在就插入模型

肯定会有插入数据如果这数据存在就更新，不存在则插入的业务场景，恰好MySQL支持这一点：

```
# 这里project_id和screen是一个联合主键，当project_id+screen冲突的时候，就更新url，size的值
INSERT INTO z_res_book_file (project_name, project_id, screen, size, create_time, url) 
VALUES
  (#projectName#, #projectId#, #screen#, #size#, now(), #url#)
  ON DUPLICATE KEY 
  UPDATE 
    url = #url#,
    size = #size#
``` 

### MySQL left join on

今天可算明白left join on 的真正用法了。

当你根据id关联查询另一张表中的数据时，如果这张表中没有这个id，如果是用inner join on 则什么数据都查不出来，如果用的是left join on只会是某一张中的数据为空，结合ifnull()函数就十分完美了~

### MySQL ifnull() 函数

这是个MySQL自带的函数，如果查询出来的数据为空，就用个默认值来替代

```
# 从hehe表中查询name，如果name为null，就用 小明来当做默认值
select ifnull(name, '小明') from hehe
```

### 导出导入数据
```
mysqldump -h 192.168.x.xxx -P 3306 -uroot -proot dbname tbname > sqlname
mysql -uroot -proot dbname < sqlname
```

### 拷贝表和数据
```
CREATE TABLE table_copy LIKE table;
INSERT INTO table_copy SELECT * FROM table;
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


