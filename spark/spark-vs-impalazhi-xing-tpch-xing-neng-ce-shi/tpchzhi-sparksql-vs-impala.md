# SparkSQL和Impala 执行TPCH查询性能对比

# 测试环境

* 物理机硬件及软件环境

| node | node127 | node123 | node124 | node125 | node126 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| cores | 40 | 40 | 40 | 40 | 40 |
| memory | 128G | 128G | 128G | 128G | 128G |
| network | 千兆网 | 千兆网 | 千兆网 | 千兆网 | 千兆网 |
| disk\(数据盘\) | 3T \* 2 | 3T \* 2 | 3T \* 2 | 3T \* 2 | 3T \* 2 |
| OS | CentOS 7.3.1611 | CentOS 7.3.1611 | CentOS 7.3.1611 | CentOS 7.3.1611 | CentOS 7.3.1611 |
| Hadoop 2.6.5      \( only  HDFS\) | namenode | datanode | datanode | datanode | datanode |
| Spark2.1.1 | Master | worker | worker | worker | worker |
| Impala2.9 | catalogd +          statestored | impalad 一个实例 | impalad | impalad | impalad |

* 软件说明
  * HDFS的副本个数为1

# 数据准备

> 本次的测试数据是由TPCH生成的100G数据。TPCH由8个表组成，共有22个查询请求。相关源码及脚本可见另一个github库[SQL-TPCH-TEST](https://github.com/hexiaoting/SQL-TPC-Test) 见目录[tpch\_2\_17\_0](https://github.com/hexiaoting/SQL-TPC-Test/tree/master/tpch_2_17_0)，生成数据的脚本见文件bin/**tpch\_dbgen\_dqgen.sh**
>
> TPCH各表在不同数据规模集下的大小和行数如下所示：
>
> ![](/assets/TPCH.png)

1. 首先生成csv格式的数据
2. 其次将csv数据上传至HDFS上，见文件bin/[upload\_2\_hdfs.sh](https://github.com/hexiaoting/SQL-TPC-Test/blob/master/bin/upload_2_hdfs.sh)
3. 最后将csv格式转为parquet格式存储至HDFS上。这个步骤是利用Spark本身的dataframe.write.parquet的接口实现的。见库中的目录[SparkCsvToParquet](https://github.com/hexiaoting/SQL-TPC-Test/tree/master/SparkCsvToParquet)，编译打包“sbt clean package”, 然后执行脚本运行[impala\_tpch\_csv\_to\_parqeut.sh](https://github.com/hexiaoting/SQL-TPC-Test/blob/master/bin/impala_tpch_csv_to_parqeut.sh)

> 注意：生成的表中把decima类型转为double, 把date类型转为timestamp这样sparksql和impala才可以读取同一份parquet数据作对比。同时，在Impala中把char类型转为String类型，这样可以提升性能。

# 构建表执行查询

## 表的构建

* SparkSQL: 不需要，直接读取parquet文件生成Dataframe直接处理，要用spark-sql接口生成表也行，但是性能没有直接写DataFrame好
* Impala: 见脚本：bin/impala\_tpch\_ddl\_spark\_parquet.sql

> 执行语法：
>
> ```
> create external table $TABLE ($COLUMN $TYPE) stored as parquet location "hdfs://namenode:20500/$DIR"
> ```
>
> 创建外部表的好处是再删除表时不会删除location指定的文件。

## TPCH Query

* 最原始的22个查询请求见bin/tpch\_queries.sql
* SparkSQL：由于用SparkSQL的./bin/spark-sql.sh接口比用DataFrame实现的性能差，因此，本次测试针对TPCH的22个查询全部重新实现DataFrame：见目录[tpch-spark](https://github.com/hexiaoting/SQL-TPC-Test/tree/master/tpch-spark)\(这是在github上开源的一个库\). 
* Impala：直接执行sql语句，相应的22个sql查询请求见目录[tpch-impala](https://github.com/hexiaoting/SQL-TPC-Test/tree/master/tpch-impala). 通过脚本bin/impala\_tpch\_query.sh执行。

> 注意：Impala和SparkSQL之前之前要统计信息：
>
> | impala | compute stats $TABLE |
> | :--- | :--- |
> | sparksql | ANALYZE TABLE $TABLE COMPUTE STATISTICS NOSCAN; |

执行结果：

| Tpch100G-Query | Impala\(s\) | SparkSQL\(s\) |
| :--- | :--- | :--- |
| 1 | 22.65 | 31.47 |
| 2 | 1.82 | 23.73 |
| 3 | 29.76 | 71.34 |
| 4 | 2.90 | 24.38 |
| 5 | 5.66 | 83.31 |
| 6 | 1.97 | 55.01 |
| 7 | 32.26 | 83.61 |
| 8 | 6.16 | 82.76 |
| 9 | 26.87 | 95.66 |
| 10 | 8.04 | 44.92 |
| 11 | 1.50 | 25.16 |
| 12 | 3.52 | 21.99 |
| 13 | 19.66 | 28.50 |
| 14 | 3.82 | 45.98 |
| 15 | 5.01 | 91.80 |
| 16 | 7.80 | 20.73 |
| 17 | 5.72 | 67.97 |
| 18 | 28.74 | 45.65 |
| 19 | 11.14 | 24.49 |
| 20 | 4.35 | 65.35 |
| 21 | 42.82 | 112.79 |
| 22 | 5.01 | 15.83 |

![](/assets/impalaSparkSQL测试对比图.png)

从以上测试结果可以暗处Impala的性能更优，平均比Spark快乐2-3倍左右，对于更实时的查询，性能优势更加明显。当然，本次的Spark执行还没有调整相关参数，相信调整相关参数优化之后性能会有一定程序的提升。

通过初步观察服务器的CPU，磁盘以及网络来看，Impala执行查询消耗的资源远低于Spark.

