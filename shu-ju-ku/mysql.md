# MySQL

MySQL是OLTP型的数据库产品。

### 部署模式

数据库4大部署模式：

1. 嵌入式（sqlite就是一个文件，嵌在应用中）
2. 单机（最经典的CS结构，以下，对于应用来说，都是CS结构）
3. 复制集群（主从复制）
4. Sharding集群（分片）

#### 单机部署

MySQL一般都在使用5.x系列，包括阿里云上的RDS，也是基于5.x定制的。

本地使用Docker起一个实例：

```text
docker run -p 3306:3306 -p 33060:33060 -e MYSQL_ROOT_PASSWORD=root mysql:5.7
```

客户端工具：

1. Sequel Pro \(GUI\)
2. mycli \(CMD\): 有自动补全，登录命令`mycli -u root -h localhost -P 3306 -p root`

#### 复制集群

binlog是MySQL的操作日志。复制集群是基于binlog的主从复制。

单机部署简单实用，但有单点问题，即，节点挂了，服务也会停止。

复制集群解决单点问题，主节点挂了，从节点至少还能可读，服务降级，也不至于服务完全终止。如果有主从切换功能，服务经过短暂异常，还能恢复。

#### Shard​ing集群

mysql实例，有容量上限。一般如果单表超过500万行记录（TODO：500万这个数字需要考据），需要考虑拆分表。

分库分表有如下策略：

1. 
sharding是水平扩展。

### SQL



### 索引

索引的目的是为了加速查询。

索引是数据的冗余存储。

### 安全

SQL注入，考虑参数化。

