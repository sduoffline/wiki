# 非关系型数据库

## 前言

NoSQL的分类：

| 存储类型 | 代表方案 | 说明 |
| :--- | :--- | :--- |
| 列存储 | HBase, Cassandra, Hypertable | 以列簇式存储，将同一列数据存在一起。适用于数据压缩，对一个或几个字段进行查询的效率很高 |
| 文档存储 | MongoDB, CouchDB | 文档存储一般用类似json的格式存储，存储的内容是文档型的。这样也就有机会对某些字段建立索引，实现关系数据库的某些功能 |
| key-value存储 | Tokyo Cabinet/Tyrant, Berkeley DB, MemcacheDB, Redis | 可以通过key快速查询到其value。一般来说，存储不管value的格式，照单全收。 |
| 图存储 | Neo4J, InfoGrid, Infinite Graph | 图形关系的最佳存储。使用传统关系数据库来解决的话性能低下，而且设计使用不方便 |
| 对象数据库 | db4o, Versant | 通过类似面向对象语言的语法操作数据库，通过对象的方式存取数据 |
| XML 数据库 | Berkeley DB XML, BaseX | 高效的存储XML数据，并支持XML的内部查询语法，比如XQuery,Xpath |
