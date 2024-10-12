### 简介

全名Remote dictionary server, 是一个开源的**基于内存**的数据存储系统。

它可以用作**数据库缓存和消息队列**等各种场景
    
最热门的**NoSQL**数据库之一。

传统数据库随着业务的扩大，MySQL由于数据库的性能瓶颈（磁盘I/O），性能下降，因此出现了Redis这种基于内存的数据库。

Redis中的数据以**键值对**的形式存储, 键区分大小写，默认存储为字符串，内部为二进制存储（因此要显示中文需启动时 --raw）

### 基础命令

**SET命令：**
设置键值对

**GET命令:**
获得Value

**DEL命令：**
删除键

**EXISTS命令：**
判断键是否存在

**KEYS命令：**
查看数据库中都有哪些键
后面可跟pattern模式匹配

```Redis
>> KEYS *  # 返回所有keys
>> KEYS *me # 返回所有以me结尾的键
```

**FLUSHALL命令：**
删除所有键

**TTL命令：**
查看一个键的过期时间

**EXPIRE命令：**
设置一个键的过期时间, 单位为秒

**SETEX命令：**
设置带过期时间的键值对

```Redis
>> SETEX name 5 "Asuna"
```

**SETNX命令：**
只有在键不存在时才设置键的值

### 数据类型

#### 五种基本数据类型

##### 字符串String

Redis中存储默认为字符串

```Redis
>> SET name "Asuna"
OK

>> GET name
"Asuna"
```

##### 列表List

类似于数组，一般用于存储和操作一组有顺序的数据

使用RPUSH和LPUSH来将元素添加到列表的右侧和左侧

```Redis
LPUSH letter a # 向letter列表中左侧添加一个元素a
# 当列表不存在时会自动创建
LPUSH letter b c d e # 可一次添加多个元素
```

使用LRANGE命令获取列表内容

```Redis
>> LRANGE letter 0 -1 # 获得从起始位置0 到 结束位置-1的列表元素
```

使用LPOP和RPOP命令来从列表首部和尾部删除元素，会返回被删除的元素

```Redis
>> LPOP letter
"e"
>> LPOP letter 2
# 也可一次删除多个元素，后加数字表示本次删除的元素数量
"d"
"c"
```

使用LLEN命令查看列表长度

```Redis
>> LLEN letter
(integer) 2
```

使用LTRIM命令删除列表指定范围以外的元素, 只保留索引位置起始到结束的元素

```Redis
>> LTRIM letter 1 3 # 只保留索引位置1到3的元素
```

##### 集合Set

无序集合，内部元素不可重复

使用SADD命令向Set中添加一个元素

```Redis
SADD SetName member 
# Adds one or more members to a set. Creates the key if it
# doesn't exist. 
```

使用SMEMBER命令查看Set集合中的元素

```Redis
SMEMBER SetName
# Returns all members of a set.
```

使用SISMEMBER命令来判断一个元素是否属于Set

```Redis
SISMEMBER SetName member
# Determines whether a member belongs to a set.
```

使用SREM命令来删除集合中的元素

```Redis
SISMEMBER SetName member
# Determines whether a member belongs to a set.
```

集合间的交集并集操作：SINTER，SUNOIN, SDIFF

##### 有序集合SortedSet

特点：有序集合中的元素都会关联一个浮点数的分数，根据这个分数来进行排序。元素成员唯一，但分数可以重复

使用ZADD命令来向集合中添加元素

```Redis
ZADD SetName score member
```

使用ZRANGE命令来查看集合中的元素

```Redis
ZRANGE SetName startIndex endIndex (withScores)
```

使用ZSCORE命令查看某个成员的分数

```Redis
ZSCORE SetName member
```

使用ZRANK，ZREVRANK命令查看成员的ASC和DESC排名

```Redis
ZRANK SetName member
```

使用ZREM来删除集合中的元素

```Redis
ZREM SetName member
```

##### 哈希Hash

键值对的集合 

使用HSET 来向哈希集合中添加键值对

```Redis
HSET SetName key value
```

使用HGET来获取指定key的value

```Redis
HGET SetName key
```

使用HGETALL 来获取全部键值对

```Redis
HGETALL SetName
```

使用HDEL 来删除键值对

```Redis
HDEL SetName key
```

使用HEXISTS 命令来查看是否存在某key

```Redis
使用HEXISTS SetName key
```

使用HLEN 命令获取键值对数量，HKEYS获取所有键


#### 五种高级数据类型

Redis提供发布、订阅的功能

PUBLISH channel message 发布消息到指定频道
SUBSCRIBE chennel 命令订阅频道，以接受消息

局限性：消息无法持久化，无法记录历史消息

##### 消息队列Stream

用于解决订阅功能的局限性

使用XADD命令向Stream中添加消息

```Redis
XADD StreamName messageID filed value
-- messageID的格式 12312-0， 第一个整数表示时间戳，第二个整数表示序列号
-- 可使用* 自动生成ID
```

使用XREAD读取消息

##### 地理空间Geospatial

存储地理信息的数据结构，支持地理位置的各种计算，两个位置间的距离，获取经纬度

使用GEOADD添加地理位置信息

```Redis
GEOADD SetName 经度 纬度 cityName
```

使用GEOPOS 命令来获取位置的经纬度

```Redis
GEOPOS SetName cityName
```

使用GEODIST来计算两个地方的直线距离

```Redis
GEODIST SetName city1 city2
```

使用GEOSEARCH来范围搜索

```Redis
GEOSEARCH setName FROMMEMBER cityName BYRADIUS 300 KM
```

##### HyperLogLog

用来做基数统计的算法（去重后的数量统计），原理：牺牲一定精度的随机算法

PFADD添加，PFCOUNT统计，PFMERGE 合并两个集合

##### 位图Bitmap

常用于记录用户签到情况，在线状态，点赞与否等两个状态的信息

SETBIT设置指定偏移量处的值，GETBIT来获取指定偏移量处的值，BITCOUNT来获取有多少个1

bitmap是String类型的拓展，因此也可通过SET命令来批量设置

##### 位域Bitfield

作用：将很多小的整数存储到一个较大的位图中，以更高效利用内存

### 事务

Redis支持事务，即在一个请求整体中执行多个命令，不同于MYSQL,不支持ACID，只是任务队列

```Redis
MULTI --开启事务
    SET
    LPUSH
    SADD
EXEC/DISCARD -- 执行或回滚取消
```

在执行EXEC前，所有命令保存在一个队列中保存，不会立刻执行

执行EXEC后，依次执行命令，任何命令执行失败，其他命令依然会执行 --不保证原子性

### 持久性

Redis基于内存，若没有持久化，一旦服务器重启或断电，则会数据丢失

两种方式：

**RDB(Redis DataBase)：**
    在指定间隔内将内存中的数据快照写进磁盘，为某时间点上的数据完整副本
    快照文件`dumb.rdb`
    适合于来做备份
    备份时Redis处于阻塞状态

**AOF（append-only-file）:**
    只追加文件：在执行写命令时，不仅写入内存，也会写入到一个追加的文件中（AOF文件）
    以日志的形式记录每一个写操作。
    重启时重新执行AOF文件中记录的操作

### 主从复制

指将一台Redis服务器的数据复制到其他服务器上.
复制是单向的，只能从主节点复制到从节点

用于构建一主多从的Redis集群，来实现一定程度上的高可用。

### 哨兵模式

用于实现集群间自动的故障传递，达到真正的高可用。

哨兵以一个独立的进程运行在集群中，用来监控Redis集群的各个节点是否运行正常。

功能：

1. **监控：**
    通过不断发送命令来检查Redis节点是否正常
2. **通知：**
   如果某个节点发生异常，哨兵通过发送订阅模式来通知其他节点
3. **自动故障转移：**
   当主节点不能正常工作时，哨兵会开启一个自动故障转移的工作：将一个从节点升级为主节点。再将其他从节点指向该主节点。

实际生产环境一般为三个哨兵节点，以防止单节点的故障，实现高可用。