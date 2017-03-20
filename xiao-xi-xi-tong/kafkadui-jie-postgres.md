# Kafka对接Postgres

此文是为了记录如何将kafka中的数据通过JDBC导入到postgres中。

要验证一下几个问题：

1. postgres中的表已经存在，若里面还有数据，是否会影响数据导入？
2. 数据导入是用insert还是copy，亦或是其他的命令执行？

## 环境准备及系统启动

1. 环境准备
   > 由于kafka 开源社区提供了confluent-platform，其中内置了很多种connect,其中就包括kafka-connect-jdbc-sink/source。所以我们可以直接拿来用，当前的最新版是confluent-3.2.0。  
   > kafka0.9.0.1对应的confluent版本不支持connect-sink,只支持connect-source。 直到confluent2.0.1\(对应kafka0.10.0.1\)才发布
2. 启动zookeeper
   ```
   [node1]$ ./bin/zookeeper-server-start -daemon etc/kafka/zookeeper.properties
   ```
3. 启动kafka
   ```
   [node1]$ ./bin/kafka-server-start -daemon etc/kafka/server.properties
   ```
4. 启动schema-register
   ```
   [node1]$ ./bin/schema-registry-start -daemon etc/schema-registry/schema-registry.properties
   ```
5. 查看当前topics
   ```
   [node1]$ ./bin/kafka-topics --list --zookeeper localhost:2181
   __confluent.support.metrics
   __consumer_offsets
   _schemas
   test
   ```
6. 创建topic
   ```
   [node1]$ bin/kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic jdbc_test
   Created topic "jdbc_test".
   ```
7. 启动connnect-jdbc-sink
   * 配置参数
     ```
     [node1]$ cat etc/kafka-connect-jdbc/sink-quickstart-postgres.properties
     name=hwt-jdbc-pg-sink
     connector.class=io.confluent.connect.jdbc.JdbcSinkConnector
     tasks.max=1

     # The topics to consume from - required for sink connectors like this one
     topics=jdbc_test

     # Configuration specific to the JDBC sink connector.
     connection.url=jdbc:postgresql://nobida209:9700/hewenting
     connection.user=hewenting
     #connection.password=hewenting
     auto.create=true
     ```
   * 启动进程

   ```
   ./bin/connect-standalone etc/schema-registry/connect-avro-standalone.properties etc/kafka-connect-jdbc/sink-quickstart-postgres.properties
   ```
8. 启动producer
9. 启动consumer
10.  * 配置环境
11. 1

zookeeper 2181

kafka 9020

schema-register 8081



