## List

Redis 列表是通过链接列表实现的。这意味着即使列表中有数百万个元素，在列表头部或尾部添加新元素的操作也是*在恒定时间内*执行的。使用命令将新元素添加到包含十个元素的列表头部的速度[`LPUSH`](https://redis.io/docs/latest/commands/lpush/)与将元素添加到包含一千万个元素的列表头部的速度相同。

缺点是什么？在使用数组实现的列表中，*通过索引*访问元素非常快（常量时间索引访问），而在通过链接列表实现的列表中则不那么快（其中操作需要的工作量与所访问元素的索引成比例）。

Redis 列表是字符串值的链接列表，通常用于：

- 实现堆栈和队列。
- 为后台工作系统构建队列管理。

**基本命令**

- [`LPUSH`](https://redis.io/docs/latest/commands/lpush/)在列表的头部添加一个新元素；[`RPUSH`](https://redis.io/docs/latest/commands/rpush/)添加到尾部。
- [`LPOP`](https://redis.io/docs/latest/commands/lpop/)从列表头部移除并返回一个元素；[`RPOP`](https://redis.io/docs/latest/commands/rpop/)执行相同操作，但从列表尾部移除。
- [`LLEN`](https://redis.io/docs/latest/commands/llen/)返回列表的长度。
- [`LMOVE`](https://redis.io/docs/latest/commands/lmove/)原子地将元素从一个列表移动到另一个列表。
- [`LRANGE`](https://redis.io/docs/latest/commands/lrange/)从列表中提取一定范围的元素。
- [`LTRIM`](https://redis.io/docs/latest/commands/ltrim/)将列表缩减为指定的元素范围。

**阻塞命令**

- [`BLPOP`](https://redis.io/docs/latest/commands/blpop/)从列表头部移除并返回一个元素。如果列表为空，则命令将阻塞，直到有元素可用或达到指定的超时时间。
- [`BLMOVE`](https://redis.io/docs/latest/commands/blmove/)原子地将元素从源列表移动到目标列表。如果源列表为空，则命令将阻塞，直到有新元素可用。

**实践案例**

```bash
# 从右边插入元素: rpush key value [value ...]
$ rpush listkey c b a
(integer) 3

# 从左边插入元素: lpush key value [value ...]

# 向某个元素前或者后插入元素: linsert key before|after pivot value

```

```bash
# 获取指定范围内的元素列表: lrange key start end
# 索引下标从左到右分别是0到N-1，但是从右到左分别是-1到-N

# 获取列表指定索引下标的元素: lindex key index

# 获取列表长度: llen key
```

```bash
# 从列表左侧弹出元素: lpop key

# 从列表右侧弹出: rpop key

# 删除指定元素: lrem key count value  注意count的含义

# 按照索引范围修剪列表: ltrim key start end
```

```bash
# 阻塞式弹出
# blpop key [key ...] timeout
# brpop key [key ...] timeout
```

**注意事项**

- Redis 列表的最大长度为 2^32 - 1（4,294,967,295）个元素。
- 访问列表头部或尾部的列表操作是 O(1)，这意味着它们非常高效。但是，操作列表内元素的命令通常是 O(n)。这些命令的示例包括[`LINDEX`](https://redis.io/docs/latest/commands/lindex/)、[`LINSERT`](https://redis.io/docs/latest/commands/linsert/)和[`LSET`](https://redis.io/docs/latest/commands/lset/)。运行这些命令时要小心谨慎，尤其是在操作大型列表时。

**内部编码**

- ziplist（压缩列表）
- linkedlist（链表）：当列表类型无法满足ziplist的条件时，Redis会使用linkedlist作为列表的内部实现
- [quicklist](https://matt.sh/redis-quicklist): Redis3.2版本

**使用场景**

- 消息队列: Redis的lpush+brpop命令组合即可实现阻塞队列，生产 者客户端使用lpush从列表左侧插入元素，多个消费者客户端使用brpop命令阻塞式的“抢”列表尾部的元素，多个客户端保证了消费的负载均衡和高可用性

- 文章列表：

  - 每篇文章使用哈希结构存储，例如每篇文章有3个属性title、timestamp、content：

    ```bash
    hmset acticle:1 title xx timestamp 1476536196 content xxxx
    hmset acticle:k title yy timestamp 1476512536 content yyyy
    ```

  - 向用户文章列表添加文章，user：{id}：articles作为用户文章列表的键：

    ```bash
    lpush user:1:acticles article:1 article3
    lpush user:k:acticles article:5
    ```

  - 分页获取用户文章列表，例如下面伪代码获取用户id=1的前10篇:

    ```
    articles = lrange user:1:articles 0 9
    for article in {articles} 
    	hgetall {article}
    ```

  - 第一，如果每次分 页获取的文章个数较多，需要执行多次hgetall操作，此时可以考虑使用Pipeline（第3章会介绍）批量获取，或者考虑将文章数据序列化为字符串类 型，使用mget批量获取。第二，分页获取文章列表时，lrange命令在列表两 端性能较好，但是如果列表较大，获取列表中间范围的元素性能会变差，此 时可以考虑将列表做二级拆分，或者使用Redis3.2的quicklist内部编码实现， 它结合ziplist和linkedlist的特点，获取列表中间范围的元素时也可以高效完成

- 口诀 
  - lpush+lpop=Stack（栈） 
  - lpush+rpop=Queue（队列） 
  - lpsh+ltrim=Capped Collection（有限集合） 
  - lpush+brpop=Message Queue（消息队列）