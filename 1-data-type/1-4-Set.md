## Set

Redis 集合是唯一字符串（成员）的无序集合。您可以使用 Redis 集合来高效地完成以下任务：

- 跟踪唯一项目（例如，跟踪访问给定博客文章的所有唯一 IP 地址）。
- 表示关系（例如，具有给定角色的所有用户的集合）。
- 执行常见的集合运算，例如交集、并集和差集。

基本命令

- [`SADD`](https://redis.io/docs/latest/commands/sadd/)向集合中添加新成员。
- [`SREM`](https://redis.io/docs/latest/commands/srem/)从集合中删除指定成员。
- [`SISMEMBER`](https://redis.io/docs/latest/commands/sismember/)测试字符串是否为集合成员。
- [`SINTER`](https://redis.io/docs/latest/commands/sinter/)返回两个或多个集合共同拥有的成员集合（即交集）。
- [`SCARD`](https://redis.io/docs/latest/commands/scard/)返回集合的大小（又称基数）。

**实践案例**

```bash
# 添加元素: sadd key element [element ...]

# 删除元素: srem key element [element ...]

# 计算元素个数: scard key

# 判断元素是否在集合中: sismember key element

# 随机从集合返回指定个数元素: srandmember key [count]

# 从集合随机弹出元素: spop key
```

```bash
# 求多个集合的交集: sinter key [key ...]

# 求多个集合的并集: suinon key [key ...]

# 求多个集合的差集: sdiff key [key ...]

# 将交集、并集、差集的结果保存 
# sinterstore destination key [key ...]
# suionstore destination key [key ...]
# sdiffstore destination key [key ...]
```

**注意事项**

- Redis 集合的最大大小为 2^32 - 1（4,294,967,295）个成员。
- 大多数集合操作（包括添加、删除和检查某个项是否为集合成员）都是 O(1)。这意味着它们非常高效。但是，对于包含数十万或更多成员的大型集合，运行[`SMEMBERS`](https://redis.io/docs/latest/commands/smembers/)命令时应谨慎。此命令是 O(n) 并在单个响应中返回整个集合。作为替代方案，请考虑[`SSCAN`](https://redis.io/docs/latest/commands/sscan/)，它允许您迭代检索集合的所有成员。

**内部编码**

- intset（整数集合）：当集合中的元素都是整数且元素个数小于set-max-intset-entries配置（默认512个）时，Redis会选用intset来作为集合的内部实现，从而减少内存的使用
- hashtable（哈希表）：当集合类型无法满足intset的条件时，Redis会使用hashtable作为集合的内部实现。

**使用场景**

- 标签：集合类型比较典型的使用场景是标签（tag）。例如一个用户可能对娱 乐、体育比较感兴趣，另一个用户可能对历史、新闻比较感兴趣，这些兴趣点就是标签。有了这些数据就可以得到喜欢同一个标签的人，以及用户的共同喜好的标签，这些数据对于用户体验以及增强用户黏度比较重要
- 生成随机数：spop/srandmember=Random item
- 社交需求: sadd+sinter=Social Graph



```

```

