# **Redis**

Nosql是什么，由于大数据的出现，所以普通的关系型数据库已经不能支持了，所以需要Nosql来解决。

Redis是将数据存储在内存中，所以存取速度快，但是需要持久化，属于Key-Valu型NoSql，它还是单线程的线程模型的，由于它是在内存中读取，所以才导致它速度相对快。其次，它不依赖于外部的库

- 单线程

  因为单线程所以一次只运行一条命令，拒绝长命令，例如：

  ![image-20200724213601908](D:\App\Typora\resources\md-image\redis\image-20200724213601908.png)

  但其实它不是单线程的（？？？？？这里还需了解）

## 一、Redis安装

- redis使用C语言开发，所以我们需要gcc环境，以及相关插件

  yum -y install gcc automake autoconf libtool make

- 从官网下载tar压缩redis包,并解压

  wget http://download.redis.io/releases/redis-6.0.3.tar.gz

  tar xzf redis-xxx.xx.tar.gz

  make

- 启动客户端

  ./redis-cli -h IP地址  -p 端口号

## 二、配置文件

参数说明
redis.conf 配置项说明如下：

1. Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
    daemonize no
    
2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
    pidfile /var/run/redis.pid
    
3. 指定Redis监听端口，默认端口为6379，作者在自己的一篇博文中解释了为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
    port 6379
    
4. 绑定的访问的主机地址
    bind 127.0.0.1

5. 当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
       timeout 300

6. 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
     loglevel verbose

7. 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
     logfile stdout

8. 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
     databases 16

9. 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
     save <seconds> <changes>
       Redis默认配置文件中提供了三个条件：
       save 900 1
       save 300 10
       save 60 10000
       分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。

10. 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
       rdbcompression yes

11. 指定本地数据库文件名，默认值为dump.rdb
        dbfilename dump.rdb

12. 指定本地数据库存放目录
        dir ./

13. 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
        slaveof <masterip> <masterport>

14. 当master服务设置了密码保护时，slav服务连接master的密码
        masterauth <master-password>

15. 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
        requirepass foobared

16. 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
        maxclients 128

17. 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
        maxmemory <bytes>

18. 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
        appendonly no

19. 指定更新日志文件名，默认为appendonly.aof
         appendfilename appendonly.aof

20. 指定更新日志条件，共有3个可选值： 
        no：表示等操作系统进行数据缓存同步到磁盘（快） 
        always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
        everysec：表示每秒同步一次（折衷，默认值）
        appendfsync everysec

21. 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
         vm-enabled no

22. 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
         vm-swap-file /tmp/redis.swap

23. 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
         vm-max-memory 0

24. Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
         vm-page-size 32

25. 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
         vm-pages 134217728

26. 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
         vm-max-threads 4

27. 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
        glueoutputbuf yes

28. 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
        hash-max-zipmap-entries 64
        hash-max-zipmap-value 512

29. 指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
        activerehashing yes

30. 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
        include /path/to/local.conf

    ---

    常用命令

    |                            |                                |
| -------------------------- | ------------------------------ |
    | rediser-server --port 6380 | 命令                           |
| redis-server 配置文件路径  | 指定路径启动                   |
    | redis-check-aof            | AOF的文件修复工具              |
    | redis-check-rdb            | RDB的文件检查工具              |
    | redis-sentinel             | sentinel                       |
    | keys *[部分字符串]         | 查看全部䣌，复杂度O（n）的命令 |
    | dbsize                     | 时间复杂度O（1）,查询          |
    | del key                    | 删除该key-value                |
    | exists                     | 是否存在某个key-value          |
    | expire key seconds         | 设置某个key值的存在时间        |
    | ttl key                    | 设置某个key值                  |
    | persist key                | 去掉过期时间                   |
    | type key                   | 获取相应的key的类型            |
    | config set xxxx xxxx       | 设置相关的动态配置             |
    
    
    
    ---
    
    防止内存溢出策略
    
      一、设置过期时间
    
      二、使用配置文件
    
      ![image-20200520170738777](D:\App\Typora\resources\md-image\redis\pubno)

## 三、数据类型

其数据类型是分为对外我们可以使用的数据类型和内部的编码方式的数据类型

![image-20200724213126693](D:\App\Typora\resources\md-image\redis\image-20200724213126693.png)

### **1. String**

- 一个键最大能存储512MB,是二进制安全的，String可以包含任何数据
- String 通常用于保存单个字符串或JSON字符串数据
- 因为字符串是二进制安全的，所以可以把图片作为字符串来保存(要注意，从redis中取出来的只能是二进制和字符串)
- 应用场景如下：计数器（变相成id生成器）、缓存、分布式锁（后面会详细介绍）

|         API         | 作用 |
| :-----------------: | :--: |
|         set         |      |
|        setnx        |      |
|      setrange       |      |
|  set  key value xx  |      |
|         get         |      |
|      getrange       |      |
|         del         |      |
|        incr         |      |
|        decr         |      |
|       incrby        |      |
|     incrbyfloat     |      |
|       decrby        |      |
|        mset         |      |
|        mget         |      |
| getset key newValue |      |
|  append key value   |      |
|  append key value   |      |
|     strlen key      |      |

### **2. Hash**

- 常用于存储一个hash值

  可以简单的理解为一个小型的嵌套的redis

  ![image-20200724224407809](D:\App\Typora\resources\md-image\redis\image-20200724224407809.png)
  
  |     API      |          作用          |
  | :----------: | :--------------------: |
  |     hget     |        获取元素        |
  |    hmget     |    同时获取多个元素    |
  |   hgetall    |      获取所有元素      |
  |     hset     |     设置一个hash值     |
  |    hmset     |      同时多个设置      |
  |     hdel     |          删除          |
  |   hexists    |        是否存在        |
  |   hincrby    |          递增          |
  |     hlen     |         求长度         |
  |   hgetall    | 返回所有的field和value |
  |    hvals     |    返回所有的value     |
  |    hkeys     |     返回所有的keys     |
  |    hsetnx    |                        |
|   hincryby   |                        |
  | hincrbyfloat |                        |
  
  

### **3. List**

- 存入的类似栈于链表，是双向链表

  ![image-20200727153652748](D:\App\Typora\resources\md-image\redis\image-20200727153652748.png)

  |                   API                   |       作用       |
  | :-------------------------------------: | :--------------: |
  |                  rpush                  |                  |
  |                  lpush                  |                  |
  | linsert key before\|after value neValue |                  |
  |                  lpop                   |                  |
  |                  rpop                   |                  |
  |          lrem key count value           |                  |
  |           ltrim key start end           |                  |
  |          lrange key start end           |                  |
  |            lindex key count             |                  |
  |                llen key                 | 时间复杂度是O(1) |
  |         lset key index newValue         |                  |

  

### **4. Set**

- 无序的不可重复的元素的集合

  |     API     | 作用 |
  | :---------: | :--: |
  |   sinter    |      |
  |    sdiff    |      |
  |   sunion    |      |
  |    sadd     |      |
  |    srem     |      |
  |    scard    |      |
  |  sismember  |      |
  | srandmember |      |
  |  smembers   |      |

  

### **5.  ZSet**

- 有序无重复元素的集合结构

  ![image-20200727161538133](D:\App\Typora\resources\md-image\redis\image-20200727161538133.png)

  |                  API                   | 作用 |
  | :------------------------------------: | :--: |
  |                  zadd                  |      |
  |                  zrem                  |      |
  |           zscore key element           |      |
  |     zincrby key incrScore element      |      |
  |                 zcard                  |      |
  |       zrange key 0 -1 whitscoes        |      |
  |  zrangebyscore key minScore maxScore   |      |
  |      zount key minScore maxScore       |      |
  |     zremrangebyrank key start end      |      |
  | zremrangebyscore key minScore maxSocre |      |
  |                zrevrank                |      |
  |               zrevrange                |      |
  |            zrerangebyscore             |      |
  |              zinterstore               |      |
  |              zunionstore               |      |

  

**6.BitMaps** 位图

就是相当于把二进制存了进行，并且能进行操作

**7. hyperLogLog**：唯小内存唯一值计数

利用极小的空间进行独立数量的统计，但是它是允许有错误率的，大概为0.81%

**8. GEO** 地理信息定位



## 四、集成开发环境客户端

### 1.lettuce

高级redis客户端，用于线程安全同步，异步和响应使用，支持集群，Sentinel

在spring boot2之后，对redis连接的支持，**默认采用了lettuce**。这就一定程度说明了lettuce和jedis的优劣

基于Netty框架的事件驱动的通信层，其方法调用是异步的。Lettuce的API是线程安全的，所以可以操作单个Lettuce连接来完成各种操作

### 2.Jedis

jiedis是老牌的redis的Java客户端，提供了较为全面的redis命令的支持.Jedis中的方法调用是比较底层的暴露的Redis的API，也即Jedis中的Java方法基本和Redis的API保持着一致，了解Redis的API，也就能熟练的使用Jedis。

### 3. Redisson

实现了分布式和可扩展的Java数据结构

和Jedis相比，功能较为简单，不支持字符串操作，不支持排序、事务、管道、分区等Redis特性。Redisson的宗旨是促进使用者对Redis的关注分离，从而让使用者能够将精力更集中地放在处理业务逻辑上。

### 4. RedisTemplate

它封装了redis连接池的模板，业务代码无需关心，释放连接逻辑

主要是调用它的api

### **tips**:

- 把任何数据保存到redis中时，都需要进行序列化，默认使用JDKserializationRedisSerializer进行序列化，在所有的key和value前都加了一串字符

## 五、其他功能

### - 发布与订阅

redis的消息队列其实并不完善，有很多缺点，比如它并没有做阻塞型的IO，来做相关的，谁抢到算谁的

![image-20200523162545942](C:\Users\31262\AppData\Roaming\Typora\typora-user-images\image-20200523162545942.png)

|     API     |       作用       |
| :---------: | :--------------: |
|   publish   |     发布频道     |
|  subscribe  |   订阅某个频道   |
| unsubscribe | 取消订阅某个频道 |

消息是一个有三个元素的多块响应 。

第一个元素是消息类型:

- subscribe: 表示我们成功订阅到响应的第二个元素提供的频道。第三个参数代表我们现在订阅的频道的数量。
- unsubscribe:表示我们成功取消订阅到响应的第二个元素提供的频道。第三个参数代表我们目前订阅的频道的数量。当最后一个参数是0的时候，我们不再订阅到任何频道。当我们在Pub/Sub以外状态，客户端可以发出任何redis命令。
- message: 这是另外一个客户端发出的发布命令的结果。第二个元素是来源频道的名称，第三个参数是实际消息的内容。

发布/订阅与key所在空间没有关系，它不会受任何级别的干扰，包括不同数据库编码。 发布在db 10,订阅可以在db 1。 如果你需要区分某些频道，可以通过在频道名称前面加上所在环境的名称（例如：测试环境，演示环境，线上环境等）。

模式匹配订阅，也就是客户端可以根据所在端的名字进行相应的

### - 慢查询

慢查询是指当前执行的命令所需时间太长，将其放到一个先进先出的队列中去

配置文件中：

**slowlog-log-slower-than**：配置一条命令最长的执行时间（单位微秒）

**slowlog-max-len**：配置慢查询日志的最多存储条数

相关命令：

slowlog get [n]

slowlog len : 获取慢查询队列的长度

slowlog reset : 清空慢查询队列

### **- pipeline**

流水线操作，主要为了减少命令在网络之间传播的时间，简单理解为批处理，因为redis主要在内存中进行执行，所以命令的执行时间其实是很快的，所以主要减少命令在网络中的传输次数

![image-20200727222712697](D:\App\Typora\resources\md-image\redis\image-20200727222712697.png)

pipeline其实类似于原生的**<u>m</u>字**操作（类似于mget），但是实质上是不同的，m的一些命令是原子性的，而pipeline是多个的

### - 多数据库

不同的数据库一般是用于完成不同的环境下，例如线上运行和测试

### -Redis事务

- discard 取消该事务
- MULTI 标记事务的开始
- UNWATCH  取消监控该事务
- WATCH 监控事务
- EXEC 执行该事务

报错处理：

​	如果命令没有明显的语法错误，但是是不可执行的，那么在该事务执行时会将除开该事务的所有行为都执行一遍

### - 缓存穿透

缓存穿透的概念很简单，用户想要查询一个数据，发现redis内存数据库没有，也就是缓存没有命中，于是向持久层数据库查询。发现也没有，于是本次查询失败。当用户很多的时候，缓存都没有命中，于是都去请求了持久层数据库。这会给持久层数据库造成很大的压力，这时候就相当于出现了缓存穿透。

解决办法：

布隆过滤器，具体是什么如下：

https://www.jianshu.com/p/2104d11ee0a2

缓存空对象：

当存储层不命中后，即使返回的空对象也将其缓存起来，同时会设置一个过期时间，之后再访问这个数据将会从缓存中获取，保护了后端数据源；

### - 缓存击穿

这里需要注意和缓存击穿的区别，缓存击穿，是指一个key非常热点，在不停的扛着大并发，大并发集中对这一个点进行访问，当这个key在失效的瞬间，持续的大并发就穿破缓存，直接请求数据库，就像在一个屏障上凿开了一个洞。

解决办法就是

### - 缓存雪崩

缓存雪崩是指，缓存层出现了错误，不能正常工作了。于是所有的请求都会达到存储层，存储层的调用量会暴增，造成存储层也会挂掉的情况。

## 六、持久化

把内存的数据写到磁盘中去，防止服务宕机了内存数据丢失

redis支持以下两种持久化方式

### - RDB

RDB 是 Redis 默认的持久化方案。在指定的时间间隔内，执行指定次数的写操作，则会将内存中的数据写入到磁盘中。即在指定目录下生成一个dump.rdb文件。Redis 重启会通过加载dump.rdb文件恢复数据。

触发机制：Sava、bgsave、自动（例如配置文件中的方式默认为900-1，300-10，60-10000）

tips: 

1. 全量复制

### - AOF 

aof存储是将redis执行过程中的所有**写指令**以**追加**的方式写到一个文件,这个文件通常是appendonly.aof

tips:

1. AOF会对文件进行重写，以此减少文件的大小

   bgrewriteaof/AOF重写配置

## 七、Redis缓存与数据库一致性

### 1. 实时同步

### 2. 异步队列

异步：

中间件：

### 3. 使用阿里的同步工具canal

### 4. 采用UDF自定义函数的方式

## 八、分布式

一台redis会有如下问题：

- 内存容量有限
- 处理能力有限
- 无法高可用

所以为了解决这个，产生了主机和从机，读写分离，主从复制

### 1.主从复制配置

在从服务器的redis.conf配置文件中，

```Java
-- port 6380 #从服务的端口号
--slaveof 127.0.0.1 6379 #指定主服务器
```

启动从服务器：

```java 
redis-server redis.conf --port 6380 --slaveof 127.0.0.1 6379
```

一般会发生在主/从结点发生故障后 

### 2. 哨兵机制

- **监控（Monitoring**）： Sentinel 会不断地检查你的主服务器和从服务器是否运作正常。
- **提醒（Notification）**： 当被监控的某个 Redis 服务器出现问题时， Sentinel 可以通过 API 向管理员或者其他应用程序发送通知。
- **自动故障迁移（Automatic failover）**： 当一个主服务器不能正常工作时， Sentinel 会开始一次自动故障迁移操作， 它会将失效主服务器的其中一个从服务器升级为新的主服务器， 并让失效主服务器的其他从服务器改为复制新的主服务器； 当客户端试图连接失效的主服务器时， 集群也会向客户端返回新主服务器的地址， 使得集群可以使用新主服务器代替失效服务器。

其实是利用sentinel来完成对主从结点的监听和管理，相当于ZK注册中心，而获取结点的话，也只需要通过sentinel来获取即可，而sentinel是如何完成对结点的监控呢，其实是通过三个定时任务：

1. 每隔一段时间（10秒）sentinel结点对所有结点发送**info**命令，所以在sentinel结点的配置中只需要有主节点的配置，可以通过主节点中的信息来获取slave结点。 Sentinel 向下线主服务器的所有从服务器发送 INFO 命令的频率会从 10 秒一次改为每秒一次。这是为了了解主从关系结点
2. 每个 Sentinel 会以每两秒一次的频率， 通过发布与订阅功能， 向被它监视的所有主服务器和从服务器的 **sentinel**:hello 频道发送一条信息， 信息中包含了 Sentinel 的 IP 地址、端口号和运行 ID （runid）。每个 Sentinel 都订阅了被它监视的所有主服务器和从服务器的 **sentinel**:hello 频道， 查找之前未出现过的 sentinel （looking for unknown sentinels）。 当一个 Sentinel 发现一个新的 Sentinel 时， 它会将新的 Sentinel 添加到一个列表中， 这个列表保存了 Sentinel 已知的， 监视同一个主服务器的所有其他 Sentinel 。这是为了发现其他的sentinel
3. 每1秒每个sentinel结点对其他的sentinel和redis执行ping，这是为了检测是否结点可用

由此还要引出 **主观下线**和**客观下线**，前面也提到了单个的sentinel结点是可以对单个其他结点做出下线的判断，但如何真正判定他下线了呢？并且是如何通知其他sentinel结点的呢？主观下线其实就是单台的sentinel结点认定这个结点挂了，而客观下线是超过规定的“人数”认为这个结点挂了。这个**规定的人数**其实在sentinel结点中设置好了，详见：

````java 
sentinel monitor mymaster 127.0.0.1 6379 2
sentinel down-after-milliseconds mymaster 30000
````

这个设置为2，就是超过2台及以上的结点认定为下线即为客观下线

down-after-milliseconds mymaster为30000，如果一个服务器没有在 master-down-after-milliseconds 选项所指定的时间内， 对向它发送 PING 命令的 Sentinel 返回一个有效回复（valid reply）， 那么 Sentinel 就会将这个服务器标记为主观下线。

在选择一个master结点下线之后，必然需要新的master，但首先需要从sentinel中选出一个领导者，那么如何选举出新的leader呢？每个发现主观下线的sentinel结点都想上位做master，并且如果sentinel结点发现自己的票数已经超过半数且超过quorum，如果有多个结点成为了领导者，那么将重新进行选举。

其中还有currentEpoch的概念，其实就是纪元的问题，当集群的状态发生改变，某个节点为了执行一些动作需要寻求其他节点的同意时，就会增加 ***currentEpoch*** 的值。目前 ***currentEpoch*** 只用于 ***slave\*** 的故障转移流程，这就跟哨兵中的sentinel.current_epoch 作用是一模一样的。当 ***slave A*** 发现其所属的 ***master*** 下线时，就会试图发起故障转移流程。首先就是增加 **currentEpoch** 的值，这个增加后的 currentEpoch 是所有集群节点中最大的。然后slave A 向所有节点发起拉票请求，请求其他 ***master*** 投票给自己，使自己能成为新的 ***master***。其他节点收到包后，发现发送者的 ***currentEpoch*** 比自己的 ***currentEpoch*** 大，就会更新自己的 ***currentEpoch***，并在尚未投票的情况下，投票给 ***slave A***，表示同意使其成为新的 ***master***；***configEpoch\*** 主要用于解决不同的节点的配置发生冲突的情况。举个例子就明白了：节点A 宣称负责 ***slot 1\***，其向外发送的包中，包含了自己的 ***configEpoch\*** 和负责的 ***slots\*** 信息。节点 C 收到 A 发来的包后，发现自己当前没有记录 ***slot 1*** 的负责节点（也就是 server.cluster->slots[1] 为 NULL），就会将 A 置为 ***slot 1*** 的负责节点（server.cluster->slots[1] = A），并记录节点 A 的 ***configEpoch\***。后来，节点 C 又收到了 B 发来的包，它也宣称负责 ***slot 1***，此时，如何判断 ***slot 1*** 到底由谁负责呢？

这就是 ***configEpoch\*** 起作用的时候了，C 在 B 发来的包中，发现它的 ***configEpoch***，要比 A 的大，说明 B 是更新的配置。因此，就将 ***slot 1\*** 的负责节点设置为 B（server.cluster->slots[1] = B）。在 ***slave\*** 发起选举，获得足够多的选票之后，成功当选时，也就是 ***slave\*** 试图替代其已经下线的旧 ***master\***，成为新的 ***master\*** 时，会增加它自己的 ***configEpoch***，使其成为当前所有集群节点的 ***configEpoch*** 中的最大值。这样，该 ***slave*** 成为 ***master*** 后，就会向所有节点发送广播包，强制其他节点更新相关 ***slots*** 的负责节点为自己

在选举出了领导者之后，需要由leader结点从原来的master结点的从结点中选出新的master。选择原则：

1. 过滤故障的节点
2. 选择优先级`slave-priority`最大的从节点作为主节点，如不存在则继续
3. 选择复制偏移量（数据写入量的字节，记录写了多少数据。主服务器会把偏移量同步给从服务器，当主从的偏移量一致，则数据是完全同步）最大的从节点作为主节点，如不存在则继续
4. 选择`runid`（redis每次启动的时候生成随机的`runid`作为redis的标识）最小的从节点作为主节点

### 3.cluster集群

​	**分区**：

  1. 范围分区

     就是将不同范围的对象映射到不同Redis实例。比如说，用户ID从0到10000的都被存储到**R0**,用户ID从10001到20000被存储到**R1**,依此类推

  2. 散列分区

     利用取余

		3. 一致性哈希

其结构特点：
 1、所有的redis节点彼此互联(PING-PONG机制),内部使用二进制协议优化传输速度和带宽。
 2、节点的fail是通过集群中超过半数的节点检测失效时才生效。
 3、客户端与redis节点直连,不需要中间proxy层.客户端不需要连接集群所有节点,连接集群中任何一个可用节点即可。
 4、redis-cluster把所有的物理节点映射到[0-16383]slot上（不一定是平均分配）,cluster 负责维护node<->slot<->value。
 5、Redis集群预分好16384个桶，当需要在 Redis 集群中放置一个 key-value 时，根据 CRC16(key) mod 16384的值，决定将一个key放到哪个桶中

### 4. 分布式锁

**锁的基本要素：**

1. 安全属性（Safety property）: 独享（相互排斥）。在任意一个时刻，只有一个客户端持有锁。
2. 活性A(Liveness property A): 无死锁。即便持有锁的客户端崩溃（crashed)或者网络被分裂（gets partitioned)，锁仍然可以被获取。
3. 活性B(Liveness property B): 容错。 只要大部分Redis节点都活着，客户端就可以获取和释放锁.

一般分布式锁的实现：实现Redis分布式锁的最简单的方法就是在Redis中创建一个key，这个key有一个失效时间（TTL)，以保证锁最终会被自动释放掉（这个对应特性2）。当客户端释放资源(解锁）的时候，会删除掉这个key。但往往会出错，当它的节点挂掉时，基于故障转移的设定也不能解决：在这种场景（主从结构）中存在明显的竞态:

1. 客户端A从master获取到锁
2. 在master将锁同步到slave之前，master宕掉了。
3. slave节点被晋级为master节点
4. 客户端B取得了同一个资源被客户端A已经获取到的另外一个锁。**安全失效！** 

