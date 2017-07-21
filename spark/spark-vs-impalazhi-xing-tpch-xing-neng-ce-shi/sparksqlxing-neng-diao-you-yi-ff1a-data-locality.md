# 数据局部性

```
    在分布式环境中，若计算节点和存储节点共享一群服务器，那么分配任务时，若能把计算任务分配到对应要处理的数据的存储节点，则计算任务从本地读取数据，而不需要从远端节点通过网络传输数据，这就减小了网络传输开销。当网络成为瓶颈时，将影响系统性能。

    本节针对Spark执行TPCH查询时出现的数据非局部性问题，找出其原因并给出解决方案。
```

## 环境

启动HDFS和Spark Standalone mode![](/assets/集群测试环境.png)

## 查询Tpch-query5

执行代码：

```
 def main(args: Array[String]): Unit = {
  val spark = SparkSession
    .builder()
    .appName("TpchQuery5")
    .getOrCreate()
  import spark.implicits._
  val lineitem = spark.read.parquet("hdfs://dell127:20500/SparkParquetDoubleTimestamp100G/lineitem")
  val orders = spark.read.parquet("hdfs://dell127:20500/SparkParquetDoubleTimestamp100G/orders")
  val customer = spark.read.parquet("hdfs://dell127:20500/SparkParquetDoubleTimestamp100G/customer")
  val supplier = spark.read.parquet("hdfs://dell127:20500/SparkParquetDoubleTimestamp100G/supplier")
  val region = spark.read.parquet("hdfs://dell127:20500/SparkParquetDoubleTimestamp100G/region")
  val nation = spark.read.parquet("hdfs://dell127:20500/SparkParquetDoubleTimestamp100G/nation")

  val forders = orders.filter($"o_orderdate" < "1995-01-01" and $"o_orderdate" >= "1994-01-01").select("o_custkey", "o_orderkey")
  val fregion = region.filter($"r_name" === "ASIA").select($"r_regionkey")
  val fnation = nation.select("n_nationkey", "n_regionkey", "n_name")
  val fsupplier = supplier.select("s_nationkey", "s_suppkey")
  val fcustomer = customer.select("c_custkey", "c_nationkey")
  val flineitem = lineitem.select("l_orderkey", "l_suppkey", "l_discount", "l_extendedprice")

   val res = flineitem.join(forders, $"l_orderkey" === forders("o_orderkey"))
     .join(fcustomer, $"o_custkey" === fcustomer("c_custkey"))
     .join(fsupplier, $"l_suppkey" === fsupplier("s_suppkey") && $"c_nationkey" === fsupplier("s_nationkey"))
     .join(fnation, $"s_nationkey" === fnation("n_nationkey"))
     .join(fregion, $"n_regionkey" === fregion("r_regionkey"))
     .select($"n_name", ($"l_extendedprice" * (lit(1) -  $"l_discount")).as("value"))
     .groupBy($"n_name")
     .agg(sum($"value").as("revenue"))
     .sort($"revenue".desc)
    res.collect().foreach(println)
 }
```

## 结果

* 执行时间：1.5m![](/assets/tune1_locality_2.png)

```
   整个查询被分为了10个任务，0-5个任务分别读取6个parquet表，
   6和7是对两个最小的表region和nation进行broadcast,
   第8个查询是做这些表的关联，第9个任务是执行最后的聚合排序。
```

* 查询树

![](/assets/tune1_locality_plantree.png)后续还有两个小表的broadcast exchange...

* stages的tasks

## 原因

spark在分配tasks时，

## 解决办法



