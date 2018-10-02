## mysql的常见优化

### in

通常in可以替换为exsits，用于优化速度，not in 替换为not exsists。in也可以替换为union，in的索引是range的，union之后单个select可以降到ref级别，但是union需要使用临时表，应该是具体情况而定。

### order   by

order by对于索引的使用在explain中表现在extra字段

1. using index  使用索引
2. using filesort 不使用索引，而是内存或者disk排序，mysql有一部分内存用于排序，如果未排序的结果级可以全部放在内存，那么不需要disk的临时空间，否则就需要disk了。

```mysql
#对于排序 顺序不同的情况 无法使用索引
select * from table order by key_part1 asc, key_part2 desc
```

```mysql
#查询的所有字段，索引使用情况不一定，因为没有使用覆盖索引，当优化器认为全表扫描更快，就不会使用索引
select * from table order by index_key
```



### group by

group by对于索引的使用在explain中的表现在extra字段，如果使用是 using index。如果不能使用索引就需要临时表



### full table scan 发生的情景

1. 小表
2. 查询条件没有使用索引字段
3. 索引命中绝大部分数据，导致直接进行全表扫描
4. 索引分离度很低（类似第三条）



### index condition  pushdown （IPC）

ipc在explain中extra字段表现为using index condition。ipc减少了回表查询的数据量

```mysql
#table(col1, col2, col3, col4)  index(col1, col2)
select * from table where col1='value' and col2 like '%part%';
```

没有ipc的处理流程

1. 根据col1索引获取主键索引
2. 根据主键索引获取整行数据，然后判断col2是否满足要求

ipc处理流程

1. 根据col1索引定位数据，
2. col2位于索引中，因为在索引数据中检查col2是否满足要求，如果满足要求再用对应的主键id去获取行
