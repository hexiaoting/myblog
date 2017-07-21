# 基本参数配置

## 主要参数说明

| 参数 | 默认值/可配置值 | 说明 |
| :--- | :--- | :--- |
| spark.serializer | org.apache.spark.serializer.KryoSerializer | 数据序列化 |
| spark.sql.shuffle.partitions | 200/集群cores的2-3倍 |  |
| spark.default.parallelism | local mode:本机核数                              mesos:8                                                 其他： max\(executor总核数目，2\) | 分布式shuffle的父RDD的最大分区数，如果没有父RDD，则 |
| spark.local.dir |  |  |
| spark.sql.autoBroadCastJoinThreshold | 10MB | 广播表的阈值 |
| spark.sql.codegen |  |  |

---

## 测试

conf/spark-defaults.conf

spark.sql.shuffle.partitions 320

---

## 结果

![](/assets/tune3_1.png)

其中Job8展开为：![](/assets/tune2_2.png)

