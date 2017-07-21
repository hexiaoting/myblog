# 基本参数配置

## 主要参数说明

| 参数 | 默认值/可配置值 | 说明 |
| :--- | :--- | :--- |
| spark.serializer | org.apache.spark.serializer.KryoSerializer | 数据序列化 |
| spark.sql.shuffle.partitions | 200/集群cores的2-3倍 |  |
| spark.local.dir |  |  |

---

## 测试

conf/spark-defaults.conf

spark.sql.shuffle.partitions 320

---

## 结果

![](/assets/tune3_1.png)

其中Job8展开为：![](/assets/tune2_2.png)

