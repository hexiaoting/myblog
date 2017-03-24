# Kafka对接Postgres

---

此文是为了记录如何将kafka中的数据通过JDBC导入到postgres中。

要验证一下几个问题：

1. postgres中的表已经存在，若里面还有数据，是否会影响数据导入？
2. 数据导入是用insert还是copy，亦或是其他的命令执行？

## 开始测试

---

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

   * 用distributed模式

   ```
   ./bin/connect-distributed etc/schema-registry/connect-avro-distributed.properties
   ```

   > 用curl发送POST请求，发给端口8083\(即刚才启动的connect-distributed进程\)，设置配置信息。
   >
   > ```
   > curl -X POST -H "Content-Type: application/json" 
   >      --data '{ "name": "test-sink-aabbcc", 
   >                "config": {
   >                          "connector.class":"io.confluent.connect.jdbc.JdbcSinkConnector", 
   >                          "tasks.max":"1", 
   >                          "connection.url":"jdbc:postgresql://nobida209:9700/hewenting",
   >                          "topics":"jdbc_test",
   >                          "auto.create":"true" 
   >                          }
   >                }' 
   >      http://localhost:8083/connectors
   >
   > curl -X POST -H "Content-Type: application/json" 
   >      --data '{ "name": "test-sqlite-jdbc-autoincrement", 
   >                "config": {
   >                          "connector.class":"io.confluent.connect.jdbc.JdbcSourceConnector", 
   >                          "tasks.max":"1", 
   >                          "connection.url":"jdbc:sqlite:test1.db",
   >                          "mode":"incrementing",
   >                          "incrementing.column.name":"id",
   >                          "topic.prefix":"sink-"       #含义为:读取test1.db下的所有表，每个表对应一个topic, 如表a的topic为sink-a。
   >                          }
   >                }' 
   >      http://localhost:8083/connectors
   > ```

   同时，可以查看当前有哪些task:     

   ```
   curl -X GET http://localhost:8083/connectors/
   ```

8. 启动producer

   ```
   ./bin/kafka-avro-console-producer --broker-list localhost:9092 --topic  jdbc_test2 --property value.schema='{"type":"record","name":"myrecord","fields":[{"name":"id","type":"int"},{"name":"product", "type": "string"}, {"name":"quantity", "type": "int"}, {"name":"price", "type":"float"}]}'
   启动之后输入数据：
   {"id": 999, "product": "foo", "quantity": 100, "price": 50}
   ```

9. 启动consumer

   ```
   ./bin/kafka-avro-console-consumer --topic jdbc_test --bootstrap-server localhost:9092 --from-beginning
   SLF4J: Class path contains multiple SLF4J bindings.
   SLF4J: Found binding in [jar:file:/home/hewenting/software/confluent-3.2.0/share/java/kafka-serde-tools/slf4j-log4j12-1.7.6.jar!/org/slf4j/impl/StaticLoggerBinder.class]
   SLF4J: Found binding in [jar:file:/home/hewenting/software/confluent-3.2.0/share/java/schema-registry/slf4j-log4j12-1.7.6.jar!/org/slf4j/impl/StaticLoggerBinder.class]
   SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
   SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
   {"id":999,"product":"foo","quantity":100,"price":50.0}
   ```

10. 验证
    > 查看jdbc连接的数据库，会看到新建了表jdbc\_test,里面有一行数据。  
    > 当然，如果我们已经创建了该table，里面已经存在其他的数据亦无妨。只要将文件sink-quickstart-postgres.properties中的 auto.create=false即可。

## 各进程端口号对应

---

| 进程 | 端口 |
| :--- | :--- |
| zookeeper | 2181 |
| kafka-server | 9020 |
| schema-register | 8081 |

## Connect-jdbc导数据所采用的方式

---

> 默认的insert.mode为insert。
>
> 也可以配置为upsert。
>
> 参考链接：[https://github.com/confluentinc/kafka-connect-jdbc/blob/master/docs/sink\_connector.rst](https://github.com/confluentinc/kafka-connect-jdbc/blob/master/docs/sink_connector.rst)

## 做ETL

---

到导入数据的时候，可以只insert 启动的某些列吗？

多个topic可以导入到一个表里吗？

