# Redis Learning Notes

[参考文档：菜鸟教材](https://www.runoob.com/redis/redis-tutorial.html)、 [Redis在线测试](https://try.redis.io/)、 [Redis命令参考](http://doc.redisfans.com/)

## 简介
1. 高性能key-value数据库
2. 与其他key-value缓存产品有三个特点：
   1. Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
   2. Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
   3. Redis支持数据的备份，即master-slave模式的数据备份。
3. 优势：速度快、数据类型丰富、原子性、丰富的特性
4. Redis的不同之处：1) 有更为复杂的数据结构并且提供原子性操作。 2) 运行在内存中但是可以持久化到磁盘。

## Redis数据类型
1. 支持五种：string, hash, list, set and zset(sorted set)
   1. string
      1. 最基本的类型，与memcached一摸一样的类型，一个key对应一个value
      2. 二进制安全，意思可以包含任何数据，例如：jpg图片或者序列化对象，最大能存储512MB
        ```shell
            SET ian "伊安"
            OK
            GET ian
            "伊安"
        ```
    2. Hash
       1. hash是一个键值对集合，是一个string类型的field和value的映射表，hash特别适合用于存储对象
       2. 实例：
        ```shell
        redis 127.0.0.1:6379> DEL runoob
        redis 127.0.0.1:6379> HMSET runoob field1 "Hello" field2 "World"
        "OK"
        redis 127.0.0.1:6379> HGET runoob field1
        "Hello"
        redis 127.0.0.1:6379> HGET runoob field2
        "World"
        ```
        实例中我们使用了 Redis `HMSET`, `HGET` 命令，`HMSET`设置了两个`field=>value`对,`HGET`获取对应`field`对应的`value`。每个 hash 可以存储 2<sup>32</sup>-1 键值对（40多亿）。
    3. List
       1. Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。
       2. 实例：
        ```shell
        redis 127.0.0.1:6379> DEL runoob
        redis 127.0.0.1:6379> lpush runoob redis
        (integer) 1
        redis 127.0.0.1:6379> lpush runoob mongodb
        (integer) 2
        redis 127.0.0.1:6379> lpush runoob rabitmq
        (integer) 3
        redis 127.0.0.1:6379> lrange runoob 0 10
        1) "rabitmq"
        2) "mongodb"
        3) "redis"
        ```
    4. Set
       1. string类型的无序集合，集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)
       2. 实例：
        ```shell
        redis 127.0.0.1:6379> DEL runoob
        redis 127.0.0.1:6379> sadd runoob redis
        (integer) 1
        redis 127.0.0.1:6379> sadd runoob mongodb
        (integer) 1
        redis 127.0.0.1:6379> sadd runoob rabitmq
        (integer) 1
        redis 127.0.0.1:6379> sadd runoob rabitmq
        (integer) 0
        redis 127.0.0.1:6379> smembers runoob

        1) "redis"
        2) "rabitmq"
        3) "mongodb"
        ```
        以上实例中 rabitmq 添加了两次，但根据集合内元素的唯一性，第二次插入的元素将被忽略。
    5. zset
       1. 每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。
       2. zadd命令：添加元素到集合，元素在集合中存在则更新对应score：`zadd key score member`
       3. 实例：
        ```shell
        redis 127.0.0.1:6379> DEL runoob
        redis 127.0.0.1:6379> zadd runoob 0 redis
        (integer) 1
        redis 127.0.0.1:6379> zadd runoob 0 mongodb
        (integer) 1
        redis 127.0.0.1:6379> zadd runoob 0 rabitmq
        (integer) 1
        redis 127.0.0.1:6379> zadd runoob 0 rabitmq
        (integer) 0
        redis 127.0.0.1:6379> > ZRANGEBYSCORE runoob 0 1000
        1) "mongodb"
        2) "rabitmq"
        3) "redis"
        ```
## [Redis命令](https://www.runoob.com/redis/redis-commands.html)
1. 连接命名：Redis客户端基本语法：`$ redis-cli`, 远程服务上执行命令：`$ redis-cli -h host -p port -a password`。(如果出现中文乱码：`$ redis-cli -h host -p port -a password`)
2. Redis事务：可以一次执行多个命令，并且有三个重要保证：
   1. 批量操作在发送 EXEC 命令前被放入队列缓存。
   2. 收到 EXEC 命令后进入事务执行，事务中任意命令执行失败，其余的命令依然被执行。
   3. 在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中。
3. 一个事务从开始到执行会经历以下三个阶段：
   1. 开始事务。
   2. 命令入队。
   3. 执行事务。
4. 以`MULTI`开始一个事务，然后将多命令入队到事务中，最后由`EXEC`命令出发事务，一并执行事务中的所有命令。
5. 单个Redis命令的执行是原子性的，但是Redis没有在事务上增加任何维持原子性的机制，所有Redis事务的执行并不是原子性的。事务可以理解为一个打包的批量执行脚本，但批量指令并非原子化的操作，中间某条指令的失败不会导致前面已做指令的回滚，也不会造成后续的指令不做。
6. Redis脚本使用lua解释器来执行。

## [Redis高级编程](https://www.runoob.com/redis/redis-partitioning.html)
1. Redis管道技术：基于服务端-客户端以及请求/响应协议的TCP服务，可以在服务端未响应的时候，客户端可以继续想服务端发送请求，并最终一次性读取所有服务端的响应。
2. Redis分区：分区是分割数据到多个Redis实例的处理过程，因此每个实例只保存key的一个子集。有范围分区和hash分区两种。

