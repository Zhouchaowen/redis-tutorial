## Sort Set

Redis 有序集是按相关分数排序的唯一字符串（成员）的集合。当多个字符串具有相同的分数时，这些字符串将按字典顺序排序。有序集的一些用例包括：

- 排行榜。例如，您可以使用排序集轻松维护大型在线游戏中最高分的有序列表。
- 速率限制器。具体来说，您可以使用排序集构建滑动窗口速率限制器，以防止过多的 API 请求。

您可以将有序集合视为集合和哈希的混合体。与集合一样，有序集合由唯一、不重复的字符串元素组成，因此从某种意义上说，有序集合也是一个集合。

然而，虽然集合内的元素没有顺序，但有序集合中的每个元素都与一个浮点值相关联，称为*分数* （这就是为什么该类型也类似于哈希，因为每个元素都映射到一个值）。

此外，有序集合中的元素是*按顺序取出的*（因此它们不是按要求排序的，顺序是用于表示有序集合的数据结构的特性）。它们按照以下规则排序：

- 如果 B 和 A 是两个具有不同分数的元素，则如果 A.score > B.score，则 A > B。
- 如果 B 和 A 的分数完全相同，则如果 A 字符串按字典顺序大于 B 字符串，则 A > B。B 和 A 字符串不可能相等，因为有序集合仅包含唯一元素。

**基本命令**

- [`ZADD`](https://redis.io/docs/latest/commands/zadd/)将新成员和相关分数添加到排序集中。如果该成员已存在，则更新分数。
- [`ZRANGE`](https://redis.io/docs/latest/commands/zrange/)返回在给定范围内排序的排序集的成员。
- [`ZRANK`](https://redis.io/docs/latest/commands/zrank/)返回所提供成员的排名，假设排序按升序排列。
- [`ZREVRANK`](https://redis.io/docs/latest/commands/zrevrank/)返回所提供成员的排名，假设排序集按降序排列。

**实践案例**

```bash
# 添加成员 zadd key score member [score member ...]

# 计算成员个数 zcard key

# 删除成员 zrem key member [member ...]

# 返回指定排名范围的成员 
# zrange key start end [withscores]
# zrevrange key start end [withscores]
```



**注意事项**

- 有序集合相比集合提供了排序字段，但是也产生了代价，zadd的时间 复杂度为O（log（n）），sadd的时间复杂度为O（1）。
- [`ZRANGE`](https://redis.io/docs/latest/commands/zrange/)运行具有较大返回值（例如，数万或更多）的命令时要小心谨慎。此命令的时间复杂度为 O(log(n) + m)，其中*m*是返回的结果数。

**内部编码**

- ziplist（压缩列表）：当有序集合的元素个数小于zset-max-ziplist- entries配置（默认128个），同时每个元素的值都小于zset-max-ziplist-value配 置（默认64字节）时，Redis会用ziplist来作为有序集合的内部实现，ziplist 可以有效减少内存的使用。
- skiplist（跳跃表）：当ziplist条件不满足时，有序集合会使用skiplist作 为内部实现，因为此时ziplist的读写效率会下降。

**使用场景**

- 排行榜系统：按照时间、按照播 放数量、按照获得的赞数

```

```

