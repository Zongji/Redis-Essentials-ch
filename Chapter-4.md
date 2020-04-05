# 命令

## Pub/Sub  发布订阅

## 事务

## 管道

## 脚本
### Lua 基础语法

### 当Redis遇到Lua

## 其他命令
### INFO


## 数据类型优化
### String
下面是String的几种编码类型:
- int 当string为64位有符号整型数字的时候使用
- embstr string长度小于40字节时使用
- raw string长度大于40字节时使用
这些编码方式是不可配置的。以下redis-cli例子显示不同的编码是被如何选择的：
```sh
127.0.0.1:6379> SET str1 12345
OK
127.0.0.1:6379> OBJECT ENCODING str1
"int"
127.0.0.1:6379> SET str2 "An embstr is small"
OK
127.0.0.1:6379> OBJECT ENCODING str2
"embstr"
127.0.0.1:6379> SET str3 "A raw encoded String is anything greater than 39
bytes"
OK
127.0.0.1:6379> OBJECT ENCODING str3
"raw"
```
### List
List有几种编码形式：
- ziplist  压缩链表， 当list里面元素个数少于配置`list-max-ziplist-entries`值，且list中每个元素的字节数少于配置`list-max-ziplist-value`值时，redis使用的list编码方式
- linkedlist  链表。超过上述两个限制时使用。
```
127.0.0.1:6379> LPUSH list1 a b
(integer) 2
127.0.0.1:6379> OBJECT ENCODING list1
"ziplist"
127.0.0.1:6379> LPUSH list2 a b c d
(integer) 4
127.0.0.1:6379> OBJECT ENCODING list2
"linkedlist"
127.0.0.1:6379> LPUSH list3 "only one element"
(integer) 1
127.0.0.1:6379> OBJECT ENCODING list3
"linkedlist"
```

### Set
set的几种编码形式：
- inset 当元素都为整数，且Set基数（cardinality）小于配置项`set-max-intset-entries`的值
- hashtable 当任何一个元素不是整数，或者Set基数（cardinality）大于配置项`set-max-intset-entries`的值
```
127.0.0.1:6379> SADD set1 1 2
(integer) 2
127.0.0.1:6379> OBJECT ENCODING set1
"intset"
127.0.0.1:6379> SADD set2 1 2 3 4 5
(integer) 5
127.0.0.1:6379> OBJECT ENCODING set2
"hashtable"
127.0.0.1:6379> SADD set3 a
(integer) 1
127.0.0.1:6379> OBJECT ENCODING set3
"hashtable"
```
### Hash
hash的几种编码方式：
- ziplist hash中键值对个数小于配置项`hash-max-ziplist-entries`的值，且每个键值对的字节长度小于`hash-max-ziplist-entries`配置项的值
- hashtable hash的长度或者value字节长度分别超过了`hash-max-ziplist-entries`和`hash-max-ziplist-value`的的值
```
127.0.0.1:6379> HMSET myhash1 a 1 b 2
OK
127.0.0.1:6379> OBJECT ENCODING myhash1
"ziplist"
127.0.0.1:6379> HMSET myhash2 a 1 b 2 c 3 d 4 e 5 f 6
OK
127.0.0.1:6379> OBJECT ENCODING myhash2
"hashtable"
127.0.0.1:6379> HMSET myhash3 a 1 b 2 c 3 d 4 e 5 f 6
OK
127.0.0.1:6379> OBJECT ENCODING myhash3
"hashtable"
```

### Sorted Set 有序集合
有序集合的几种编码方式：
-ziplist 集合中键值对个数小于`set-max-ziplist-entries`指定的值，且每个value字节长度小于`zset-maxziplist-value`指定的值的时候使用
-skiplist和hashtable  集合中键值对个数或者某个value的字节长度分别超过了`set-max-ziplistentries`和`zset-max-ziplist-value`指定的值时候使用
```
127.0.0.1:6379> ZADD zset1 1 a
(integer) 1
127.0.0.1:6379> OBJECT ENCODING zset1
"ziplist"
127.0.0.1:6379> ZADD zset2 1 abcdefghij
(integer) 1
127.0.0.1:6379> OBJECT ENCODING zset2
"skiplist"
127.0.0.1:6379> ZADD zset3 1 a 2 b 3 c 4 d
(integer) 4
127.0.0.1:6379> OBJECT ENCODING zset3
"skiplist"
```






## 内存使用率

## 总结


