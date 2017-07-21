# 基本参数配置

## 主要参数说明

| 参数 | 默认值/可配置值 | 说明 |
| :--- | :--- | :--- |
| spark.sql.shuffle.partitions | 200/集群cores的2-3倍 | 执行join或聚合时shuffle数据的分区数 |
| spark.sql.files.maxPartitionBytes | 13417728\(128MB\) | 读文件时一个分区的最大字节数 |
| spark.sql.files.openCostInBytes | 4194304\(4MB\) |  |
| spark.sql.broadcastTimeout | 300 |  |
| spark.sql.autoBraodCastJoinThreshold | 10485760\(10MB\) |  |
| spark.default.parallelism | local mode:本机核数                              mesos:8                                                 其他： max\(executor总核数目，2\) | 分布式shuffle的父RDD的最大分区数，如果没有父RDD，则 |
| spark.local.dir |  |  |
| spark.sql.codegen |  |  |
| spark.serialize |  |  |

---

## 测试

conf/spark-defaults.conf

spark.sql.shuffle.partitions 320

---

## 结果

![](/assets/tune3_1.png)

其中Job8展开为：![](/assets/tune2_2.png)

