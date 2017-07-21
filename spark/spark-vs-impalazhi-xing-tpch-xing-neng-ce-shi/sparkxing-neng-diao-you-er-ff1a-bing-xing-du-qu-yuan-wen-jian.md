# spark.read.parquet并发执行

## 优化点

在上一节《Spark性能调优一：data locality》中我们看到执行tpch query5的代码是先通过spark.read.parquet获得6个表的dataframe,  然后再进行表之间的关联。

从Spark WebUI\(http://node:4040\)上![](/assets/tune1_locality_2.png)可以看出这些parquet job是顺序执行的，读取6个表的parquet任务大约为7s，而每个parquet最多占用2s, 那是不是可以将前面这些任务并发执行呢？

当然可以。

## 解决办法

添加多线程并发读取，修改代码为：

```
  def main(args: Array[String]): Unit = {
    case class readData (fileName : String , spark : SparkSession) extends Callable[Dataset[Row]]{
      override def call(): Dataset[Row] = {
        spark.read.parquet(fileName)
      }
    }

    val spark =  SparkSession.builder()
      .appName("practice")
      .config("spark.master", "spark://10.61.2.127:7077")
      .config("spark.scheduler.mode","FAIR")
       .getOrCreate()
    import spark.implicits._
    val pool: ExecutorService = Executors.newFixedThreadPool(6)
    val list = new ArrayList[Future[Dataset[Row]]]()

    for(fileName<-"orders,lineitem,customer,supplier,region,nation".split(",")){
      val o1 = new readData("hdfs://dell127:20500/SparkParquetDoubleTimestamp100G/" + fileName,spark)
      val tmp: Future[Dataset[Row]]= pool.submit(o1)
      list.add(tmp)
    }
    val rddList = new ArrayBuffer[Dataset[Row]]()
    for (i <- 0 to 5) {
      rddList += list.get(i).get()
    }

    pool.shutdown()
    pool.awaitTermination(Long.MaxValue, TimeUnit.NANOSECONDS)
    val forders = rddList(0).filter("o_orderdate < '1995-01-01' and o_orderdate >= '1994-01-01'").select("o_custkey", "o_orderkey")
    val flineitem = rddList(1).select("l_orderkey", "l_suppkey", "l_discount", "l_extendedprice")
    val fregion = rddList(4).where("r_name = 'ASIA'").select("r_regionkey")
    val fnation = rddList(5).select("n_nationkey", "n_regionkey", "n_name")
    val fsupplier = rddList(3).select("s_nationkey", "s_suppkey")
    val fcustomer = rddList(2).select("c_custkey", "c_nationkey")

    val res = flineitem.join(forders, $"l_orderkey" === forders("o_orderkey"))
      .join(fcustomer, $"o_custkey" === fcustomer("c_custkey"))
      .join(fsupplier, $"l_suppkey" === fsupplier("s_suppkey") && $"c_nationkey" === fsupplier("s_nationkey"))
      .join(fnation, $"s_nationkey" === fnation("n_nationkey"))
      .join(fregion, $"n_regionkey" === fregion("r_regionkey"))
      .select($"n_name", ($"l_extendedprice" * (lit(1) - $"l_discount")).as("value"))
      .groupBy($"n_name")
      .agg(sum($"value").as("revenue"))
      .sort($"revenue".desc)
    println(res.collect())
  }
```

## 执行结果





