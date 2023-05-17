# 总览

* 运行在内存中，所以非常快
* 单线程执行
* 用`key:value`形式存储

redis的底层是通过一个巨大的hash表来存储所有的数据

key都是string类型的，但是对于value来说，会有很多别的类型

```bash
set cz 666
incr cz

set cz2 66a
# 会抛出异常
incr cz2
```

在存储的时候会先进行一次强转，不行再用默认的方式进行存储。

查看value的存储方式:`object encoding keyName`

## 登录

用123456进行认证

* auth 123456
* select 0
  * redis中有很多数据库，这里选择了0号数据库

## 通用命令

* set
* get
* del
* exists
* keys 查找匹配的key
* flushall  删除所有
* ttl key 查看key对应的过期时间
* expire key time 设置key的过期时间time，单位s
* setex key time value 设置key，值为value，过期时间time

## hash

### 命令 

* hset hashName key value
* hget hashName key
* hgetall hashName 
* hdel hashName key
* hexists hashName key

`hmset hashTableName key value key value ....`

### 应用场景

用redis hash结构为value实现购物车

1. 用`cart:用户ID`作为key来存储一个用户的购物车
2. 添加某一个物品，用`物品Id:数量`作为存储的hash表中的一个值

```bash
# 添加商品
hset cart:1  1001（物品ID） 1
# 增加数量
hincrby cart:1 1001 1
# 商品总数
hlen cart:1
# 删除商品
hdel cart:1 1001
# 获取所有商品
hgetall cart:1
```

## List

redis还可以采用List作为值，有了List作为值，再结合不同的操作，就可以形成不同的数据结构，redis形成的数据结构的好处是**他的结构是分布式结构**。

### 命令

* lpush/rpush arrayName value
* lpop/rpop arrayName value
* lrange arrayName 0 -1 代表查看arrayName 中的所有值
* get 没法使用

### 应用场景

实现类似说说的消息流推送功能

1. 每个用户用一个列表来维护
2. 当某个人发布了一个说说之后，给所有关注他的人前面插入数据
3. 再刷朋友圈的时候从List的前面取数据

```bash
# 插入数据
LPUSH msg:{userID}  说说ID
# 查看最近说说
LRANGE msg:{userID} 0 4
```

## Set

集合，与list最大的不同在于集合中不会有重复的元素，**集合中的元素是无序的**，redis为集合提供了计算交集，差集，补集等的能力。

### 命令

* sadd setName value
* smembers setName      查看集合中的元素
* srem setName value

### 应用场景

**微信点赞收藏：**

点赞和取消点赞对应往集合中添加和减少元素

访问集合元素可以获得点赞列表

集合中的数目是点赞数

**关注模型：**

每个人关注的人可以用集合进行存储

1. 共同关注：取两个人关注集合的交集
2. 我关注的人也关注了他：遍历关注的人，看看关注的人对应关注集合中是否有这个人
3. 我可能认识的人：通过算法匹配一个和我关系比较大的人，计算二者关注集合的差集

## Zset

有序集合，有序集合仍然保存了集合的性质，不过在此基础上增添了score，作为排序的标准，在进行添加的时候，需要添加score作为其排序的标准。

### 应用场景

用有序集合可以轻松实现点赞安装时间排序的功能，当有人点赞的时候，用**时间戳作为score**进行插入。

### 数据结构

ZipList 压缩列表



SkipList 跳表

![](md_img/redis基础知识/image-20230428101337426.png)

链表的查找是其性能的瓶颈，数组的插入和删除元素是其性能的瓶颈，跳表在增删改查方面的性能都比较不错，跳表的核心是将有序链表改造为支持“折半查找”算法的结构。

## 工作流

1. 开启redis命令行`redis-cli`
2. 认证用户
   1. `auth password`
3. 查看所有的key
   1. `keys *`
4. 查看某一个key（对于不同的key，查看方式也是不一样的）
   * if value is of type string -> GET `<key>`
   * if value is of type hash -> HGET or HMGET or HGETALL `<key>`
   * if value is of type lists -> lrange `<key> <start> <end>`
   * if value is of type sets -> smembers `<key>`
   * if value is of type sorted sets -> ZRANGEBYSCORE `<key> <min> <max>`
   * if value is of type stream -> xread count `<count>` streams `<key>` `<ID>`.

## 事务

Redis的事务和Mysql这种关系型数据库中的事务不同：

Mysql：事务要么全部执行不成功，要么都不执行。

Redis：挨个执行事务中的命令，失败就执行下一条，没有回滚的机制 。

Redis执行的时候，用`MULTI`开始一个事物，之后将多条命令插入到事务中（不会执行），最后用 `EXEC`命令执行。

事务的周期： 开始事务---------命令入列------------执行事务

> Redis 是单线程的，在执行事务中的命令时，会将这些命令按照顺序执行完，别的redis命令不会插队执行。

## 数据持久化

redis是基于内存设计的，所以断电之后肯定会丢失数据，但是直接全部丢失数据肯定是无法接受的，所以redis有对应的数据备份机制，整体的思路就是隔一段时间将相关的数据写入硬盘之后，再重启redis的时候再从硬盘中读取数据。

### RDB(read only database)

这种方式是给整个redis整一个**快照**，用二进制文件保存某一时刻数据库的状态。一般是`dump.rdb`文件

#### 主动触发和被动触发

* 主动触发

  通过手动敲击命令进行触发，有save和bgsave两条命令，都是对当前redis进行备份，前一种在有大量数据的时候会阻塞线程，不推荐使用，后一个创建一个新的线程对数据库进行备份，推荐

* 被动触发

  可以理解为在数据库中添加了触发器，在满足一定的条件下自动触发备份机制，`save m n`，m秒内触发n次操作就保存。

  ```bash
  save 300 1 # 在300s之内有超过1个key被修改就进行备份
  save 60 9000 # 60s内有大9000个key被修改就备份（大量key刷新，可以理解为重要数据需要保存）
  ```

#### 优点和缺点

优点：

1. rdb是一种十分紧缩的二进制文件，体积较小，非常适合备份，一般线上每隔一段时间都进行redis的bgsave备份，以防数据全部丢失

2. Redis加载RDB的速度远大于AOF方式

缺点：

	1. 每次rdb备份所需要的时间较长
 	2. 不同版本的redis对应的rdb不同，会有数据兼容性问题

## AOF(append only file)

将对于数据库的每个**写**操作记录下来，之后通过这些记录进行恢复，有不同的策略决定多会写log，redis一个大优点就是基于内存，不用再和磁盘进行IO，其实这种记录日志的方式又需要和磁盘进行IO了。

默认不开启，开启命令:`appendonly yes`

| 策略                 | 解释                                           |
| -------------------- | ---------------------------------------------- |
| appendfsync always   | 每次命令都对磁盘进行写入，不推荐，效率太低了。 |
| appendfsync everysec | 每1s进行一次写入                               |
| appendfsync no       | 完全依赖os，一般周期30s                        |

>注意：在命令写入AOF文件之后，redis会对这个文件进行重写优化，提高这个脚本的执行速率

#### 优缺点

优点：

1. 安全性高，如果保存策略社会之频繁一点，损失的数据少
2. 不小心flushall之后，也可以恢复，删除最后一个对应的log即可
3. 可以对AOF进行自动重写

缺点：

	1. 文件体积较大
 	2. 执行速度慢与RDB

## 混合方式 

文件的前半部分是RDB（备份数据库），后面一部分是AOF（操作log），redis4.0之后这种方式是默认配置。

这种方式既可以保存重启后加载效率也能防止数据丢失。

