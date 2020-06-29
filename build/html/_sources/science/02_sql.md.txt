## 数据库真题

### SQL慢查询的解决思路

#### 慢SQL的系统表现

* 1，数据库CPU负载高。一般是查询语句中有很多计算逻辑，导致数据库cpu负载。

* 2，IO负载高导致服务器卡住。这个一般和全表查询没索引有关系。

* 3，查询语句正常，索引正常但是还是慢。如果表面上索引正常，但是查询慢，需要看看是否索引没有生效。

#### 开启SQL慢查询的日志

    * 在 MySQL 的配置文件 my.cnf 的 [mysqld] 项下配置慢查询日志开启
        - [mysqld]slow_query_log=1
        - slow_query_log_file=/var/log/mysql/log-slow-queries.log
        - long_query_time=2
#### 总结
    - 1. 打开慢日志查询，确定是否有SQL语句占用了过多资源，如果是，在不改变业务原意的前提下，对insert、group by、order by、join等语句进行优化。
    - 2. 考虑调整MySQL的系统参数： innodb_buffer_pool_size、innodb_log_file_size、table_cache等。
    - 3. 确定是否是因为高并发引起行锁的超时问题。
    - 4. 如果数据量过大，需要考虑进一步的分库分表






