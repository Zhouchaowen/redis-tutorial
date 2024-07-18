## String

> 默认情况下，单个 Redis 字符串最大可为 512 MB。

**基础用法**

```
set key value [ex seconds] [px milliseconds] [nx|xx]
```

- ex seconds：为键设置秒级过期时间。 

- px milliseconds：为键设置毫秒级过期时间。 

- nx：键必须不存在，才可以设置成功，用于添加。 

- xx：与nx相反，键必须存在，才可以设置成功，用于更新

**实践案例**

```bash
# SET：存储一个字符串值。
$ SET bike:1 Deimos
 OK
 
# GET：检索字符串值。 
$ GET bike:1
 "Deimos"
```

```bash
$ mset bike:1 "Deimos" bike:2 "Ares" bike:3 "Vanth"
OK

# MGET：在单个操作中检索多个字符串值。
$ mget bike:1 bike:2 bike:3
1) "Deimos"
2) "Ares"
3) "Vanth"
```

```bash
$ set total_crashes 0
OK

# INCRBY：以原子方式递增（并在传递负数时递减）存储在给定键处的计数器。 
# INCRBYFLOAT: 用于浮点计数器命令。
$ incr total_crashes
(integer) 1

$ incrby total_crashes 10
(integer) 11
```

SETNX：仅当键尚不存在时， 才会存储字符串值。对于实现[锁](https://redis.io/docs/latest/develop/use/patterns/distributed-locks/)很有用。 

**注意事项**

> 大多数字符串操作都是 O(1)，这意味着它们非常高效。但是，请注意 SUBSTR、GETRANGE 和 SETRANGE 命令，它们的时间复杂度可能是 O(n)。在处理大字符串时，这些随机访问字符串命令可能会导致性能问题。

**内部编码**

- int：8个字节的长整型。 
- embstr：小于等于39个字节的字符串。 
- raw：大于39个字节的字符串。

```bash
# 查看编码方式
$ set key 8653
OK

$ object encoding key
"int"
```

**应用场景**

- 缓存：Redis具有支撑高并发的特性，所以缓存通常能起到加速读写和降低后端压力, 命名规则: test:user
- 计数：incr total_crashes
- 共享Session: 负载均衡场景分布式服务会将用户的访问均衡到不同服务器
- 限速：SET key value EX 60 NX