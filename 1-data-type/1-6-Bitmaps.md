# Bitmaps

Bitmaps本身不是一种数据结构，实际上它就是字符串，但是它可以对字符串的位进行操作, 可以把Bitmaps想象成一个以位为单位的数组，数组的 每个单元只能存储0和1，数组的下标在Bitmaps中叫做偏移量



**实践案例**

```bash
# 设置值 setbit key offset value

# 获取值 gitbit key offset

# .获取Bitmaps指定范围值为1的个数 bitcount [start][end]



```



```bash
# Bitmaps间的运算 bitop op destkey key[key....]
# bitop是一个复合操作，它可以做多个Bitmaps的and（交集）、or（并 集）、not（非）、xor（异或）操作并将结果保存在destkey中
# 计算出2016-04-04和2016-04-03两天都访问过网站的用户数
$ bitop and unique:users:and:2016-04-04_03 unique:users:2016-04-03 unique:users:2016-04-03

```







