# HypeLogLog

HyperLogLog并不是一种新的数据结构（实际类型为字符串类型），而是一种基数算法，通过HyperLogLog可以利用极小的内存空间完成独立总数的统计，数据集可以是IP、Email、ID等



**实践案例**

```bash
# 添加 pfadd key element [element …]


# 计算独立用户数 pfcount key [key …]

```

**注意事项**

- HyperLogLog内存占用量非常小，但是存在错误率，开发者在进行数据结构选型时只需要确认如下两条即可：
  - 只为了计算独立总数，不需要获取单条数据。
  - 可以容忍一定误差率，毕竟HyperLogLog在内存的占用量上有很大的优势