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
   1. ```
      ./bin/zookeeper-server-start -daemon etc/kafka/zookeeper.properties
      ```
   2. 对应端口号2181
3. 启动kafka







