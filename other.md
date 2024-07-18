Redis客户端执行一条命令分为如下4个部分:

- 发送命令
- 命令排队 
- 命令执行
- 返回结果







```
慢查询
Redis提供了slowlog-log-slower-than和slowlog-max-len配置来解决这两个 问题。从字面意思就可以看出，slowlog-log-slower-than就是那个预设阀值， 它的单位是微秒（1秒=1000毫秒=1000000微秒），默认值是10000，假如执 行了一条“很慢”的命令（例如keys*），如果它的执行时间超过了10000微 秒，那么它将被记录在慢查询日志中。

如果slowlog-log-slower-than=0会记录所有的命令，slowlog-log-slower- than<0对于任何命令都不会进行记录。

下面使用config set命令将slowlog-log-slower- than设置为20000微秒，slowlog-max-len设置为1000：
config set slowlog-log-slower-than 20000 
config set slowlog-max-len 1000 
config rewrite

获取慢查询日志
slowlog get [n]

慢查询日志重置
slowlog reset

slowlog-max-len配置建议：线上建议调大慢查询列表，可设置为1000以上

slowlog-log-slower-than配置建议：如果命令执行时间在1毫秒以上，那么Redis最多可支撑OPS不到 1000。因此对于高OPS场景的Redis建议设置为1毫秒

慢查询只记录命令执行时间，并不包括命令排队和网络传输时间。因 此客户端执行命令的时间会大于命令实际执行时间。因为命令执行排队机 制，慢查询会导致其他命令级联阻塞，因此当客户端出现请求超时，需要检 查该时间点是否有对应的慢查询，从而分析出是否为慢查询导致的命令级联 阻塞

由于慢查询日志是一个先进先出的队列，也就是说如果慢查询比较多 的情况下，可能会丢失部分慢查询命令，为了防止这种情况发生，可以定期 执行slow get命令将慢查询日志持久化到其他存储中（例如MySQL），然后 可以制作可视化界面进行查询

```



```
pipeline

原生批量命令是原子的，Pipeline是非原子的
原生批量命令是一个命令对应多个key，Pipeline支持多个命令
原生批量命令是Redis服务端支持实现的，而Pipeline需要服务端和客户 端的共同实现
```



```
事务

Redis提供了简单的事务功能，将一组需要一起执行的命令放到multi和 exec两个命令之间。multi命令代表事务开始，exec命令代表事务结束，它们 之间的命令是原子顺序执行的

事务中的错误处理机制
命令错误
运行时错误
```



```
lua

创作可复用的多命令原子执行操作
```



```
发布订阅

Redis提供了基于“发布/订阅”模式的消息机制，此种模式下，消息发布 者和订阅者不进行直接通信，发布者客户端向指定的频道（channel）发布消 息，订阅该频道的每个客户端都可以收到该消息

发布消息
publish channel message

订阅消息
subscribe channel [channel ...]

取消订阅
unsubscribe [channel [channel ...]]

查询订阅
pubsub channels [pattern]

```



