# 数据库表

1. Function：存储用户相关的function定义及symbol等信息
2. MUdflib：主要存储so文件的content, 用base64编码

# 执行流程

1. 用户接口：create function my\_add\(int, int\) returns int location '/tmp/sum.so' symbol=\`addudf\`
2. create function实现逻辑：



![](/assets/import.png)

