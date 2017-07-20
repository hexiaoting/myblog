

# 测试环境

* 硬件及软件环境

| node | node127 | node123 | node124 | node125 | node126 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| cores | 40 | 40 | 40 | 40 | 40 |
| memory | 128G | 128G | 128G | 128G | 128G |
| network | 千兆网 | 千兆网 | 千兆网 | 千兆网 | 千兆网 |
| disk\(数据盘\) | 3T \* 2 | 3T \* 2 | 3T \* 2 | 3T \* 2 | 3T \* 2 |
| OS  | CentOS 7.3 | CentOS 7.3 | CentOS 7.3 | CentOS 7.3 | CentOS 7.3 |
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

表的构建

* SparkSQL: 不需要，直接读取parquet文件生成Dataframe直接处理，要用spark-sql接口生成表也行，但是性能没有直接写DataFrame好
* Impala:





