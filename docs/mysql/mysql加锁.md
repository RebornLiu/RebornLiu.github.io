## mysql加锁逻辑

1. MVVC
2. ICP
3. 聚簇索引和二级索引
4. 隔离级别
5. 两阶段加锁2PL

### 死锁

1. 并发进行delete and insert。



   使用RR隔离级别，count列有非唯一索引，进行先删除后插入

   | count |
   | ----- |
   | 1     |
   | 2     |
   | 3     |

   ```mysql
   #session1
   start transaction;
   delete from table where count=4;#hold gap lock (3,正无穷)
   #session2
   start transaction;
   delete from table where count=5;#hold gap lock (3,正无穷)
   #session1
   insert into table value(4);#wait session2 release gap lock
   #session2
   insert into table value(5);#wait session1 release gap lock
   ```



   1. gap lock是可重入的，因此两个事务的delete可以获取相同的gap lock。

      > It is also worth noting here that conflicting locks can be held on a gap by different transactions.

   2. gap lock之在RR隔离级别下为了防止幻读，因此它阻塞的是insert操作，对于其他操作都是兼容的。

2. 官方例子

   | count |
   | ----- |
   | 1     |



   ```mysql
   
   ```

   myslq控制台提示deadlock。因为session2等待session1释放s锁，session1等待session2释放x锁。这里可以看到，加锁和时间关系挺大？？，官方文档解释如下

   > Deadlock occurs here because client A needs an *X* lock to delete the row. However, that lock request annot be granted because client B already has a request for an *X* lock and is waiting for client A to release its *S* lock. Nor can the *S* lock held by A be upgraded to an *X* lock because of the prior request by B for an *X* lock. As a result, `InnoDB` generates an error for one of the clients and releases its locks