# Citus+Postgres+ElasticSearch+zombodb

在做舆情分析时，经常用到elasticSearch及mongedb。ES的全文检索非常牛逼，但这两者有个问题是对复杂join的查询支持的不好，同时，mongodb特别吃内存。

因此，我们提出一种新的方案是把mongodb替换掉，换为分布式的postgres，即citus+postgres或greenplum。但是全文检索仍旧用elasticsearch，因此需要考虑数据如何存储，理想的情况是数据只存储一份，即数据存到pg中，索引信息存到es中。

那么，就引出了一个pg插件zombodb，专门为此而生，且和citus适配良好。

| 组件 | 说明 |
| :--- | :--- |
| [postgres](https://github.com/postgres/postgres) | 单机RDBMS |
| [citus](https://github.com/citusdata) | Citus is a distributed database that scales across commodity servers using transparent sharding and replication |
| [zombodb](https://github.com/zombodb/zombodb) | ZomboDB is a Postgres extension that enables efficient full-text searching via the use of indexes backed by Elasticsearch. |
| elasticsearch |  |



