## mysql连接释放时锁也释放

测试例子如下

```mysql
CREATE TABLE `student` (
  `id` int(11) DEFAULT NULL,
  `name` varchar(5) DEFAULT NULL,
  UNIQUE KEY `student_id_uindex` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8

insert into student value（1， "liu");
```



```mysql
//事务1
set autocommit=0；
select * from student where id=1 for update;
```

```mysql
//事务2
set autocommit=0;
select * from student where id=1 for update;
```

事务1先执行，事务2后执行，此时事务2等待锁。此时推出事务1的链接，事务2马上获取到了锁。

所以如果使用mysql作为分布式锁的时候 ，持有锁的client端down机的时候，不会导致锁一直无法释放。可以说zk和mysql的分布式锁都是状态保持的，而redis的分布式锁没有保持状态，为了避免client端down机导致的锁无法释放，需要设置一个超时时间