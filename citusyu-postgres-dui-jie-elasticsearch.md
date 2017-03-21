# Citus+Postgres+ElasticSearch+zombodb

在做舆情分析时，经常用到ElasticSearch及mongedb。ES的全文检索非常牛逼，但这两者有个问题是对复杂join的查询支持的不好，同时，mongodb特别吃内存。

因此，我们提出一种新的方案是把mongodb替换掉，换为分布式的postgres，即citus+postgres或greenplum。但是全文检索仍旧用elasticsearch，因此需要考虑数据如何存储，理想的情况是数据只存储一份，即数据存到pg中，索引信息存到es中。

那么，就引出了一个pg插件zombodb，专门为此而生，且和citus适配良好。

| 组件 | 说明 |
| :--- | :--- |
| [postgres](https://github.com/postgres/postgres) | 单机RDBMS |
| [citus](https://github.com/citusdata) | Citus is a distributed database that scales across commodity servers using transparent sharding and replication |
| [zombodb](https://github.com/zombodb/zombodb) | ZomboDB is a Postgres extension that enables efficient full-text searching via the use of indexes backed by Elasticsearch. |
| [elasticsearch](https://github.com/elastic/elasticsearch) | Elasticsearch is a distributed RESTful search engine built for the cloud |



## 搭建citus+postgres+zombodb

---

> citus是将pg分布式化的一个插件，主要功能是将pg上的表切分为多个分片，每个分片分散到多个worker\(一个pg进程\)上，同时可以为该表配置副本个数以保证数据的可靠性。
>
> citus的存在的问题包括：
>
> 1. master存在单点瓶颈的问题，没有standy，若配上pg的HA，那standy是只读模式；
> 2. 支持的join有限，复杂点的查询如包括子链接、子查询之类的就直接报错不支持；而且性能比较差，除了colocate的之外，其他的join执行都会把两个表的数据拉到master节点上，再在master节点执行join；
> 3. 开源版本不支持数据的rebalance;
> 4. 不支持节点的动态上下线。

搭建环境步骤：

1. 下载、安装postgres

   > 安装的版本为9.3，9.4，9.5均可，因为后续要用到zombodb插件，对pg的版本有要求。

   第一步：下载[postgres](https://www.postgresql.org/ftp/source/)

   ```
   wget https://ftp.postgresql.org/pub/source/v9.5.0/postgresql-9.5.0.tar.bz2
   tar -zxvf postgresql-9.5.0.tar.bz2
   ```

   第二步：编译安装

       cd postgresql-9.5.0
       ./configure --prefix=`pwd`/build      #安装到当前build目录下
       make -j 24
       make install
       export PATH=`pwd`/build/bin:$PATH     #把新安装的bin加入到PATH中

       pg_ctl init -D `pwd`/build/data
       修改配置文件`pwd`/build/data/pg_hba.conf:添加一行host all all 0.0.0.0/0 trust这样其他节点才可以访问到当前节点的pg
       修改配置文件`pwd`/build/data/postgresql.conf:修改listen_addresss='*',port=9999 #可以指定为其他端口号
       pg_ctl start -D `pwd`/build/data -l `pwd`/build/logfile

   第三步：测试

   ```
   psql -p 9999 -d template1  #成功
   template1=#                #支持\l查看所有数据库, \d查看当前数据库的所有表

   创建新的数据库
   create database test;      #成功，执行\l多了一个数据库，\c test切换数据库
   ```

2. 下载、安装citus

   > 要装6.0以上版本，也是为了适配zombodb插件

   第一步：下载citus

   ```
   wget https://github.com/citusdata/citus/archive/master.zip
   unzip master.zip
   ```

   第二步：编译安装

   ```
   ./configure         #执行pg_config确认postgres的信息是否正确
   make
   make install
   vim $PGDIR/data/postgresql.conf 设置shared_preload_libraries = 'citus'

   #cluster搭建，参考citus集群搭建链接：https://docs.citusdata.com/en/v6.1/installation/single_machine_rhel.html
   将上述处理过之后的pg拷贝到其他节点作为worker，重启所有的postgres进程
   psql -p 9999
   执行CREATE EXTENSION citus;

   #在master节点增加worker:
   SELECT * from master_add_node('$WORKERNODE_IP', 9999);
   ```

   第三步：测试\(所有的请求都发给master\)

   ```
   #查看cluster的worker情况：
   psql -p 9999 -c "select * from master_get_active_worker_nodes();"

   #创建分布式表
   create table t(id int, name text);
   select master_create_distributed_table('t', 'id'); #表示将表't'安装'id'进行分片，默认分片个数32个，副本为1.
   ```

3. 下载安装zombodb

   > 可以[下载源码](https://github.com/zombodb/zombodb/releases/tag/v3.1.10)编译安装，可以直接下载rpm包。本文以[rpm](https://github.com/zombodb/zombodb/releases/download/v3.1.10/zombodb_centos6_pg95-3.1.10-1.x86_64.rpm)包为例。  
   > 注：若下载源码编译安装，则要注意libcurl得是7.42.2以上版本。

   第一步：下载编译安装\(citus集群的每个postgres都要安装如下执行\)

   ```
   wget https://github.com/zombodb/zombodb/releases/download/v3.1.10/zombodb_centos6_pg95-3.1.10-1.x86_64.rpm
   rpm -Uvh zombodb_centos6_pg95-3.1.10-1.x86_64.rpm

   #修改pg的配置文件postgresql.conf
   增加这行：local_preload_libraries = 'zombodb.so'

   #重新启动postgres
   pg_ctl restart -D $PG_DATADIR -l logfile

   #新建extension
   $ psql -p 9999 -d $DATABASE_NAME -c "CREATE EXTENSION zombodb;"
   ```

   第二步：测试（参照zombodb给的[tutorial](https://github.com/zombodb/zombodb/blob/master/TUTORIAL.md)）  
   注：要安装elastic search且添加plugin及更新配置后才行。

### 

### 遗留问题

---

* [ ] zombodb的tutorial中给的zdb\_tally为何在我的环境中返回空？
  ```
  SELECT * FROM zdb_tally('products', 'keywords', '^.*', '', 5000, 'term');
  ```
* [ ] drop index为何es上的index没有drop掉，这会导致在es上残留很多垃圾数据？
* [ ] zombodb对于中文分词只能是单字，无法识别词组。得用到es的其他analyzer如ik\_smart，其中zombodb对接es的原理是怎样的?



