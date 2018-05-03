# MySQL工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和MySQL相关的内容：

### 笛卡尔积过大

> 数据库查询两张没有关联的表时，此时的笛卡尔积很大，会占据极大的内存，禁止这样做。

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

### MySQL left join on

今天可算明白left join on 的真正用法了。

当你根据id关联查询另一张表中的数据时，如果这张表中没有这个id，如果是用inner join on 则什么数据都查不出来，如果用的是left join on只会是某一张中的数据为空，结合ifnull()函数就十分完美了~

### MySQL ifnull() 函数

这是个MySQL自带的函数，如果查询出来的数据为空，就用个默认值来替代

```
# 从hehe表中查询name，如果name为null，就用 小明来当做默认值
select ifnull(name, '小明') from hehe
```

### MySQL 两表 join 用不了索引

MySQL 两表关联字段的字符集不同时会导致 join 时用不了索引，当数据量多的时候查询就会很慢

详情参考这边文章：https://blog.csdn.net/everda/article/details/77476716
