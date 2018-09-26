## mysql中的null

结论

- mysql的字段尽量不要用null，因为null并不是不需要存储空间，而且mysql很难对于null的索引优化，统计等
- 对于count操作，null字段也会有特殊处理，可能导致误解

```mysql
CREATE TABLE `student` (
  `id` int(11) DEFAULT NULL,
  `name` varchar(5) DEFAULT NULL,
  UNIQUE KEY `student_id_uindex` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

| id   | name |
| ---- | ---- |
| 1    | liu  |
| 2    | null |

```mysql
#mysql查询null不能使用=和!=
select * from student where name = null;
select * from student where name != null;
```

返回空，没有查询结果

```mysql
#mysql 查询必须使用is null 和is not null
select * from student where name is null;
select * from student where name is not null;
```

以上两句分别返回

| id   | name |
| ---- | ---- |
| 2    | null |

| id   | name |
| ---- | ---- |
| 1    | null |

------

对于student 数据内容如下

| id   | name |
| ---- | ---- |
| 0    | K    |
| 1    | L    |
| 2    | X    |
| 3    | NULL |
| 5    | NULL |
| 6    |      |
| NULL | NULL |

```mysql
#返回表的行数，不会忽略NULL的行
select count(*) from student;
```

返回 7

```mysql
#返回表的行，不会忽略NULL的行，和count(*)的结果一致，只是底层速度可能根据表结构不同而不同
#count(1) count(2) count(3) 是一样的 数值并不是表示第几列
select count(1) from student;
```

返回 7

```mysql
#返回列的数量，会忽略null，但是不会忽略0和‘’（空串）
select count(name) from student;
```

返回 4

