## Hash

Redis 哈希是一种记录类型，其结构为字段值对的集合。您可以使用哈希来表示基本对象以及存储计数器分组等。

**基本命令**

- [`HSET`](https://redis.io/docs/latest/commands/hset/)：设置哈希表中一个或多个字段的值。
- [`HGET`](https://redis.io/docs/latest/commands/hget/)：返回给定字段的值。
- [`HMGET`](https://redis.io/docs/latest/commands/hmget/)：返回一个或多个给定字段的值。
- [`HINCRBY`](https://redis.io/docs/latest/commands/hincrby/)：将给定字段的值增加提供的整数。

**实践案例**

```bash
# 设置值: hset key field value
$ hset user:1 name tom
(integer) 1

# 获取值: hget key field
$ hget user:1 name
"tom"

# 删除值: hdel key field [field ...]
$ hdel user:1 name
(integer) 1

# 计算field个数: hlen key
$ hset user:1 name tom
$ hlen user:1
(integer) 1
```

```bash
# 批量设置和获取
$ hmget key field [field ...] 
$ hmset key field value [field value ...]
```

```bash
# 判断field是否存在
$ hexists key field

# 获取所有field
$ hkeys key

# 获取所有value
$ hvals key
```

**注意事项**

- 大多数 Redis 哈希命令都是 O(1)。一些命令（例如[`HKEYS`](https://redis.io/docs/latest/commands/hkeys/)、、[`HVALS`](https://redis.io/docs/latest/commands/hvals/)）[`HGETALL`](https://redis.io/docs/latest/commands/hgetall/)以及大多数与到期相关的命令都是 O(n)，其中*n*是字段值对的数量。

**内部编码**

- ziplist（压缩列表）：当哈希类型元素个数小于hash-max-ziplist-entries 配置（默认512个）、同时所有值都小于hash-max-ziplist-value配置（默认64 字节）时，Redis会使用ziplist作为哈希的内部实现，ziplist使用更加紧凑的 结构实现多个元素的连续存储，所以在节省内存方面比hashtable更加优秀。
- hashtable（哈希表）：当哈希类型无法满足ziplist的条件时，Redis会使 用hashtable作为哈希的内部实现，因为此时ziplist的读写效率会下降，而 hashtable的读写时间复杂度为O（1）

**使用场景**

- 缓存映射关系型数据库表的数据