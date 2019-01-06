### namemode初始化

```bash
hdfs namenode -f
```

### hadoop启动后的进程

```bash
jps -l
8294 org.apache.hadoop.hdfs.server.datanode.DataNode
8601 org.apache.hadoop.hdfs.server.namenode.SecondaryNameNode
8089 org.apache.hadoop.hdfs.server.namenode.NameNode
8954 org.apache.hadoop.yarn.server.resourcemanager.ResourceManager
56250 org.apache.hadoop.util.RunJar
9148 org.apache.hadoop.yarn.server.nodemanager.NodeManager

```

### hive的json操作

```
hive> select * from json_tb;
OK
{"store":{"fruit":[{"weight":8,"type":"apple"},{"weight":9,"type":"pear"}],"bicycle":{"price":19.95,"color":"red"}},"email":"amy@only_for_json_udf_test.net","owner":"amy"}
{"store":{"fruit":[{"weight":8,"type":"apple"},{"weight":9,"type":"pear"}],"bicycle":{"price":19.951,"color":"red1"}},"email":"amy@only_for_json_udf_test.net1","owner":"amy1"}
Time taken: 0.261 seconds, Fetched: 2 row(s)

```

```bash
# $表示根目录 
# 数组使用[]下标从0开始
# 深层属性使用.
# get_json_object 获取单个属性
select get_json_object(json_tb.json, '$.owner') from json_tb;
select get_json_object(json_tb.json, '$.store.fruit[0]') from json_tb;

#json_tuple 获取多个属性 只能使用顶级字段
select json_tuple(json, 'owner', 'store') from json_tb;
```

