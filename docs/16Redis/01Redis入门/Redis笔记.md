笔记内容包括两个视频的笔记：

**Redis**—尚硅谷java研究院

（推荐）Redis入门到精通【黑马程序员】https://www.bilibili.com/video/BV1CJ411m7Gc

# 第1章 NoSQL 简介

REmote Dictionary Server：是一种用C语言开发的开源的高性能键值对数据库。

1.1 技术的分类

1. 解决功能性的问题

Java、Servlet、Jsp、Tomcat、RDBMS、JDBC、Linux、Svn 等

1. 解决扩展性的问题

Spring、 SpringMVC、SpringBoot、Hibernate、MyBatis等

1. 解决性能的问题

NoSQL、Java多线程、Nginx、MQ、ElasticSearch、Hadoop等

1.2 WEB1.0 及WEB2.0

1. Web1.0的时代,数据访问量很有限，用一夫当关的高性能的单节点服务器可以解决大部分问题.

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzMyMDAucG5n)

1. Web2.0时代的到来，用户访问量大幅度提升，同时产生了大量的用户数据，加上后来的智能移动设备的普及，所有的互联网平台都面临了巨大的性能挑战.

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzMyMjkucG5n)

1.3 解决服务器CPU内存压力

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzMyNTIucG5n)

思考: Session共享问题如何解决?

- 方案一、存在Cookie中

此种方案需要将Session数据以Cookie的形式存在客户端,不安全，网络负担效率低

- 方案二、存在文件服务器或者是数据库里

此种方案会导致大量的IO操作，效率低.

- 方案三、Session复制

此种方案会导致每个服务器之间必须将Session广播到集群内的每个节点，Session数据会冗余，节点越多浪费越大,存在广播风暴问题.

- 方案四、存在Redis中

目前来看，此种方案是最好的。将Session数据存在内存中，每台服务器都从内存中读取数据,速度快，结构还相对简单.

## 1.4 解决IO压力

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzM0MjgucG5n)

将活跃的数据缓存到Redis中，客户端的请求先打到缓存中来获取对应的数据，如果能获取到，直接返回，不需要从MySQL中读取。如果缓存中没有，再从MySQL数据库中读取数据，将读取的数据返回并存一份到Redis中，方便下次读取.

扩展: 对于持久化的数据库来说，单个库单个表存在性能瓶颈，因此会通过水平切分、垂直切分、读取分离等技术提升性能，此种解决方案会破坏一定的业务逻辑，但是可以换取更高的性能.

## 1.5 NoSQL数据库概述

1. NoSQL(NoSQL = Not Only SQL )，意即==“不仅仅是SQL”，泛指非关系型的数据库。==

- NoSQL 不依赖业务逻辑方式存储，而以简单的key-value模式存储。因此大大的增加了数据库的扩展能力。

1. NoSQL的特点

- 不遵循SQL标准
- 不支持ACID（原子性，一致性，持久性，隔离性）
- 远超于SQL的性能。

#### 3) NoSQL的适用场景

- 对数据高并发的读写

- 海量数据的读写

- 对数据高可扩展性的

  目前 NoSQL 不能完全替代关系型数据库.使用关系型数据库结合 NoSQl 数据库进行完成项目
  2.3.1 当数据比较复杂时不适用于 NoSQL 数据库
  2.3.2 关系型数据库依然做为数据存储的主要软件.
  2.3.3 NoSQL 数据库当作缓存工具来使用.
  2.3.3.1 把某些使用频率较高的内容不仅仅存储到关系型数据库中还存储到 NoSQL 数据中
  2.3.3.2 考虑到: NoSQL 和关系型数据库数据同步的问题

  Redis 持久化策略
  3.1 rdb持久化策略
  3.1.1 默认的持久化策略.
  3.1.2 每隔一定时间后把内存中数据持久化到 dump.rdb 文件中

  3.1.3 缺点:

  3.1.3.1 数据过于集中.（都存储到了一个文件中）
  3.1.3.2 可能导致最后的数据没有持久化到 dump.rdb 中（因为是每隔一段时间）
  3.1.3.2.1 解决办法:使用命令:SAVE 或BGSAVE 手动持久
  化.
  3.2 aof持久化策略
  3.2.1 监听 Redis 的日志文件,监听如果发现执行了修改,删除,
  新增命令.立即根据这条命令把数据持久化.
  3.2.2 缺点:
  3.2.2.1 效率降低

1. NoSQL的不适用场景

- 需要事务支持
- 基于sql的结构化查询存储，处理复杂的关系,需要即席查询。

1. 建议: 用不着sql的和用了sql也不行的情况，请考虑用NoSql

1.6 常用的缓存数据库

1. Memcached

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzM1NDIucG5n)

1. Redis

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzM2MDYucG5n)

1. mongoDB

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzM2MTUucG5n)

1. 列式数据库

- 先看行式数据库

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzM2MjkucG5n)

思考: 如下两条SQL的快慢

 select * from users where id =3（快）

 select avg(age) from users（慢）需要先查行年龄，再平均

- 再看列式数据库

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzM3MTEucG5n)

列式数据库对查平均快

1. HBase

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzM3MzUucG5n)

1. Cassandra

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzM3NTMucG5n)

1. Neo4j

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzM4MDUucG5n)

1.7 数据库排名

http://db-engines.com/en/ranking

# 第2章 Redis简介 及 安装

## 2.1 Redis是什么

Redis是一个开源的key-value存储系统。

和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，Redis支持各种不同方式的排序。

与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是Redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

## 2.2 Redis的应用场景

1. 配合关系型数据库做高速缓存

- 高频次，热门访问的数据，降低数据库IO

1. 由于其拥有持久化能力，利用其多样的数据结构存储特定的数据

- 最新N个数据→通过List实现按自然事件排序的数据
- 排行榜，TopN→利用zset(有序集合)
- 时效性的数据，比如手机验证码→Expire过期
- 计数器，秒杀→原子性，自增方法INCR、DECR
- 去除大量数据中的重复数据→利用set集合
- 构建队列→利用list集合
- 发布订阅消息系统→pub/sub模式

2.3 Redis官网

1. Redis官方网站 http://Redis.io
2. Redis中文官方网站 http://www.Redis.net.cn

手册网址： http://doc.redisfans.com/

2.4 关于Redis版本

1. 3.2.5 for Linux
2. 不用考虑在Windows环境下对Redis的支持

Redis官方没有提供对Windows环境的支持，是微软的开源小组开发了对Redis对Windows的支持.

## 2.5 安装步骤

1. 下载获得redis-3.2.5.tar.gz后将它放入我们的Linux目录/opt
2. 解压命令:`tar -zxvf redis-3.2.5.tar.gz`
3. 解压完成后进入目录:`cd redis-3.2.5`
4. 在redis-3.2.5目录下执行make命令

编译：make

安装：make install

```
创建软链接
ln -s 原始目录名 快速访问目录名
 创建配置文件管理目录
mkdir conf
mkdir config
 创建数据文件管理目录
mkdir data
1234567
```

运行Make命令时出现错误,提示 gcc：命令未找到 ,原因是因为当前Linux环境中并没有安装gcc 与 g++ 的环境

1. 安装gcc与g++

- 能上网的情况:

yum install gcc

yum install gcc-c++

- 不能上网[建议]

参考Linux课程中<<03_在VM上安装CentOS7>>中的第40步骤

1. 重新进入到Redis的目录中执行 make distclean后再执行make 命令.

```
Hint: It's a good idea to run 'make test' ;)
1
```

1. 执行完make后，可跳过Redis test步骤，直接执行 make install

```
    INSTALL install
    INSTALL install
    INSTALL install
    INSTALL install
    INSTALL install
    make[1]: Leaving directory '/home/ftp/redis/redis-3.0.4/src'
123456
```

## 2.6 查看默认安装目录 /usr/local/bin

> 放到这个目录下之后，可以在任何目录下访问。

1. Redis-benchmark:性能测试工具，可以在自己本子运行，看看自己本子性能如何(服务启动起来后执行)
2. Redis-check-aof：修复有问题的AOF文件，rdb和aof后面讲
3. Redis-check-dump：修复有问题的dump.rdb文件
4. Redis-sentinel：Redis集群使用
5. Redis-server：前台启动Redis

```
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 3.0.4 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 26926
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                     
1234567891011121314151617
```

这是前台启动，我们要把他设置为后台启动。

1. redis-cli：客户端，操作入口

## 2.7 Redis的启动

1. 默认前台方式启动

- 直接执行redis-server 即可.启动后不能操作当前命令窗口

1. 推荐后台方式启动

- 拷贝一份redis.conf配置文件到其他目录，例如根目录下的myredis目录 /myredis
- 修改redis.conf文件中的一项配置 daemonize 将no 改为yes，代表后台启动
- 设置自己的端口号
- 执行配置文件进行启动 执行 `redis-server /myredis/redis.conf`
- ps -ef | grep 进程名
- kill -s 9 进程号

```
ouc-13   26926 22709  0 15:30 pts/4    00:00:00 redis-server *:6379
ouc-13   29372 28429  0 15:49 pts/3    00:00:00 grep --color=auto redis
端口号是6379
123
conf文件有如下内容即可：
port 6379
daemonize yes
logfile "6379.log"
dir /redis-4/data #log和持久化rdb文件文件的目录
12345
```

## 2.8 客户端访问

1. 使用`redis-cli` 命令访问启动好的Redis

- 如果有多个Redis同时启动，则需指定端口号访问 `redis-cli -h 127.0.0.1 -p 端口号`
- 输入exit或Ctrl+C退出

1. 测试验证,通过 ping 命令 查看是否 返回 PONG

## 2.9 关闭Redis服务

1. 单实例关闭

- 如果还未通过客户端访问，可直接 `redis-cli shutdown`
- 如果已经进入客户端,直接 shutdown即可.

1. 多实例关闭

l 指定端口关闭 redis-cli -p 端口号 shutdown

如何启动多个redis：

```
默认配置启动
redis-server
redis-server –-port 6379
redis-server –-port 6380 ……
 指定配置文件启动
redis-server redis.conf
redis-server redis-6379.conf
redis-server redis-6380.conf ……
redis-server conf/redis-6379.conf
redis-server config/redis-6380.conf ……
默认连接
redis-cli
 连接指定服务器
redis-cli -h 127.0.0.1
redis-cli –port 6379
redis-cli -h 127.0.0.1 –port 6379


daemonize yes
以守护进程方式启动，使用本启动方式， redis将以服务的形式存在，日志将不再打印到命令窗口中
port 6379
设定当前服务启动端口号
dir “/自定义目录/redis/data“
设定当前服务文件保存位置，包含日志文件、持久化文件（后面详细讲解）等
logfile "6***.log“
设定日志文件名，便于查阅
1234567891011121314151617181920212223242526
```

## 2.9 Redis端口号的由来

1. 端口号来自一位影星的名字 . Alessia Merz

## 2.10 Redis 默认16个库

1. Redis默认创建16个库,每个库对应一个下标,从0开始。通过客户端连接后默认进入到0 号库，推荐只使用0号库.
2. 使用命令 `select 库的下标` 来切换数据库，例如 `select 8`

统一的密码管理：所有库都是同样密码，要么都OK，要么一个都连不上

## 2.11 Redis的单线程+多路IO复用技术

1. 多路复用是指使用一个线程来检查多个文件描述符（Socket）的就绪状态，比如调用select和poll函数，传入多个文件描述符，如果有一个文件描述符就绪，则返回，否则阻塞直到超时。得到就绪状态后进行真正的操作可以在同一个线程里执行，也可以启动线程执行（比如使用线程池）。
2. Memcached 是 多线程 + 锁 ；Redis 是 单线程 + 多路IO复用.

> **1.阻塞IO**, 给女神发一条短信, 说我来找你了, 然后就默默的一直等着女神下楼, 这个期间除了等待你不会做其他事情, 属于备胎做法.

> **2 非阻塞IO**, 给女神发短信, 如果不回, 接着再发, 一直发到女神下楼, 这个期间你除了发短信等待不会做其他事情, 属于专一做法.

> **3 IO多路复用**, 是找一个宿管大妈来帮你监视下楼的女生, 这个期间你可以些其他的事情. 例如可以顺便看看其他妹子,玩玩王者荣耀, 上个厕所等等. IO复用又包括 select, poll, epoll 模式. 那么它们的区别是什么?
> 3.1 **select大妈** 每一个女生下楼, select大妈都不知道这个是不是你的女神, 她需要一个一个询问, 并且select大妈能力还有限, 最多一次帮你监视1024个妹子
> 3.2 **poll大妈**不限制盯着女生的数量, 只要是经过宿舍楼门口的女生, 都会帮你去问是不是你女神
> 3.3 **epoll大妈**不限制盯着女生的数量, 并且也不需要一个一个去问. 那么如何做呢? epoll大妈会为每个进宿舍楼的女生脸上贴上一个大字条,上面写上女生自己的名字, 只要女生下楼了, epoll大妈就知道这个是不是你女神了, 然后大妈再通知你.

> 上面这些同步IO有一个共同点就是, 当女神走出宿舍门口的时候, 你已经站在宿舍门口等着女神的, 此时你属于**阻塞状态**

> 接下来是**异步IO**的情况
> 你告诉女神我来了, 然后你就去王者荣耀了, 一直到女神下楼了, 发现找不见你了, 女神再给你打电话通知你, 说我下楼了, 你在哪呢? 这时候你才来到宿舍门口. 此时属于逆袭做法

# 第3章 Redis的五大数据类型

string String

hash HashMap

list LinkedList

set HashSet

sorted set TressSet

## 3.1 key 基本命令

http://www.redis.cn/commands.html

| 命令     | 说明                                              |
| -------- | ------------------------------------------------- |
| keys *   | 查看当前库的所有键                                |
| exists   | 判断某个键是否存在                                |
| type     | 查看键的类型                                      |
| del      | 删除某个键                                        |
| expire   | 为键值设置过期时间，单位秒                        |
| ttl      | 查看还有多久过期，返回-1表示永不过期,-2表示已过期 |
| dbsize   | 查看当前数据库中key的数量                         |
| flushdb  | 清空当前库                                        |
| Flushall | 通杀全部库                                        |

```sh
# 设置key有效期
expire key seconds
pexpire key milliseconds
expireat key timestamp
pexpireat key milliseconds-timestamp

# 查询key有效时间 time to live
ttl key # 如果key不存在或者已过期，返回 -2。如果key存在并且没有设置过期时间（永久有效），返回 -1 。整数代表有效秒数
pttl key # 毫秒数

# 切换key从时效性转换为永久性
persist key
123456789101112
查询匹配的key值
keys pattern正则
12
其他操作
rename key newkey
renamenx key newkey

排序key
sort
sort list1
1234567
```

## 3.2 String

1. String是Redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value
2. String类型是二进制安全的。意味着Redis的string可以包含任何数据。比如jpg图片或者序列化的对象 。
3. String类型是Redis最基本的数据类型，一个Redis中字符串value最多可以是512M
4. 常用操作

m：multiple

为什么有m的操作：发送命令还需要时长

| 命令                                             | 说明                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| get                                              | 查询对应键值                                                 |
| set                                              | 添加键值对                                                   |
| append                                           | 将给定的追加到原值的末尾 ，返回的长度                        |
| strlen                                           | 获取值的长度，不包括\0                                       |
| setnx                                            | 只有在key 不存在时设置key的值                                |
| `incr <key>`                                     | 将key中存储的数字值增1 只能对数字值操作，如果为空，新增值为1 |
| `decr <key>`                                     | 将key中存储的数字值减1 只能对数字之操作，如果为空,新增值为-1 |
| `incrby/decrby <key> 步长`、`incrbyfloat key 值` | 将key中存储的数字值增减，自定义步长。值可正可负，但小数得用float。操作具有原子性，命令都是一个一个执行的，所以安全。超过上限或原来不是数会报错。 |
| `mset <key1> <value1> <key2> <value2>`           | 同时设置一个或多个key-value对                                |
| mget                                             | 同时获取一个或多个value                                      |
| msetnx                                           | 同时设置一个或多个key-value对，当且仅当所有给定的key都不存在 |
| getrange <起始位置> <结束位置>                   | 获得值的范围，双闭区间，类似java中的substring                |
| setrange <起始位置>                              | 用覆盖所存储的字符串值，从<起始位置>开始                     |
| `setex <key> <过期时间> <value>`                 | 设置键值的同时，设置过去时间，单位秒。ex是expire。对投票系统等很有效。但重新设置set的值后，时间就失效了 |
| psetex key milliseconds value                    |                                                              |
| getset                                           | 以新换旧，设置了新值的同时获取旧值                           |

数据操作不成功的反馈与数据正常操作之间的差异

string 类型数据操作的注意事项

① 表示运行结果是否成功
 (integer) 0 → false 失败
 (integer) 1 → true 成功
② 表示运行结果值
 (integer) 3 → 3 3个
 (integer) 1 → 1 1个

 数据未获取到
（ nil）等同于null

 数据最大存储量
512MB
 数值计算最大范围（ java中的long的最大值）
9223372036854775807

key的命名规范：

```
表名:主键名:主键值:字段名
```

如`stu:id:1:class`

1. 详说 incr key 操作的原子性

- 所谓原子操作是指不会被线程调度机制打断的操作；这种操作一旦开始，就一直运行到结束，中间不会有任何 context switch （切换到另一个线程）。
- 在单线程中， 能够在单条指令中完成的操作都可以认为是" 原子操作"，因为中断只能发生于指令之间。
- 在多线程中，不能被其它进程（线程）打断的操作就叫原子操作。
- Redis单命令的原子性主要得益于Redis的单线程
- 思考: java中i++是否是原子操作?

## 3.3 hash

场景：学生有姓名，学号，班级等信息，想让学生为key，剩下的为值。那么此时值想让称为一个hashmap，这样更方便更改。

新的存储需求：对一系列存储的数据进行编组，方便管理，典型应用存储对象信息

需要的存储结构：一个存储空间保存多个键值对数据

hash类型：底层使用哈希表结构实现数据存储

hash存储结构优化
 如果field数量较少，存储结构优化为类数组结构
 如果field数量较多，存储结构使用HashMap结构

```sh
hset key field value #改
hget key field #获取单个字段
hgetall key #获取所有字段
hdel key field1 [field2]

hmset key field1 value1 field2 value2...#设置多个
hmget key field1 field2...#获取多个
hlen key #字段数量
hexists key field #查是否存在指定字段

hkeys key
hvals key

hincrby key field increment
hincrbyfloat key field increment
123456789101112131415
例子：
127.0.0.1:6379> hset user id 01
(integer) 0
127.0.0.1:6379> hset user name lisi
(integer) 0
127.0.0.1:6379> hset user class 1
(integer) 1
# 即hset可以多句分开设置，不会覆盖
127.0.0.1:6379> hgetall user
1) "id"
2) "01"
3) "name"
4) "lisi"
5) "class"
6) "1"
127.0.0.1:6379> hget user name
"lisi"
127.0.0.1:6379> hdel user class
(integer) 1
127.0.0.1:6379> hgetall user
1) "id"
2) "01"
3) "name"
4) "lisi"
127.0.0.1:6379> hmget user id name
1) "01"
2) "lisi"
127.0.0.1:6379> hexists user id
(integer) 1
127.0.0.1:6379> hexists user age
(integer) 0
127.0.0.1:6379> hkeys user
1) "id"
2) "name"
12345678910111213141516171819202122232425262728293031323334
```

hash 类型数据操作的注意事项

- hash类型下的value只能存储字符串，不允许存储其他数据类型，不存在嵌套现象。如果数据未获取到，对应的值为（ nil）
- 每个 hash 可以存储 232 - 1 个键值对
- hash类型十分贴近对象的数据存储形式，并且可以灵活添加删除对象属性。但hash设计初衷不是为了存储大量对象而设计的，切记不可滥用，更不可以将hash作为对象列表使用
- hgetall 操作可以获取全部属性，如果内部field过多，遍历整体数据效率就很会低，有可能成为数据访问瓶颈

hash类型应用场景：

淘宝购物车涉及与实现

业务分析：

- 仅分析购物车的redis存储模型。添加、浏览、更改数量、删除、清空
- 购物车于数据库间持久化同步（不讨论）
- 购物车于订单间关系（不讨论）
  提交购物车：读取数据生成订单
  商家临时价格调整：隶属于订单级别
- 未登录用户购物车信息存储（不讨论）
  cookie存储

解决方案

- 以客户id作为key，每位客户创建一个hash存储结构存储对应的购物车信息
- 将商品编号作为field，购买数量作为value进行存储
- 添加商品：追加全新的field与value
- 浏览：遍历hash
- 更改数量：自增/自减，设置value值
- 删除商品：删除field
- 清空：删除key
- 此处仅讨论购物车中的模型设计
- 购物车与数据库间持久化同步、购物车与订单间关系、未登录用户购物车信息存储不进行讨论

场景2：双11活动日，销售手机充值卡的商家对移动、联通、电信的30元、 50元、 100元商品推出抢购活动，每种商品抢购上限1000张

解决方案

- 以商家id作为key，3个key
- 将参与抢购的商品id作为field
- 将参与抢购的商品数量作为对应的value
- 抢购时使用降值的方式控制产品数量
- 实际业务中还有超卖等实际问题，这里不做讨论

业务场景
string存储对象（ json）与hash存储对象

## 3.4 List

1. 单键多值
2. Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）。
3. 它的底层实际是个**双向链表，对两端的操作性能很高，通过索引下标的操作中间的节点性能会较差**

不可以操作中间的

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzM5NDEucG5n)

1. 常用操作

| 命令           | 说明                                         |
| -------------- | -------------------------------------------- |
| lpush/rpush    | 从左边/右边插入一个或多个值。                |
| lpop/rpop      | 从左边/右边吐出一个值。 值在键在，值光键亡。 |
| rpoplpush      | 从列表右边吐出一个值，插到列表左边           |
| lrange         | 按照索引下标获得元素(从左到右)，可以用-1     |
| lindex         | 按照索引下标获得元素(从左到右)               |
| llen           | 获得列表长度                                 |
| linsert before | 在的后面插入 插入值                          |
| lrem           | 从左边删除n个value(从左到右)                 |

```sh
# 规定时间内获取并移除数据，现在没有没关系，可以等一会
blpop key1 [key2] timeout
brpop key1 [key2] timeout
brpoplpush source destination timeout
1234
```

业务场景：

微信朋友圈点赞，要求按照点赞顺序显示点赞好友信息
如果取消点赞，移除对应好友信息

```sh
# 移除指定数据，中间拿数据,但其实是从左面拿指定value，拿完count个value后结束
lrem key count value
12
```

list操作注意事项：

- list中保存的数据都是string类型的，数据总容量是有限的，最多2^32 - 1 个元素 (4294967295)。
- list具有索引的概念，但是操作数据时通常以队列的形式进行入队出队操作，或以栈的形式进行入栈出栈操作
- 获取全部数据操作结束索引设置为-1
- list可以对数据进行分页操作，通常第一页的信息来自于list，第2页及更多的信息通过数据库的形式加载

场景2：

twitter、新浪微博、腾讯微博中个人用户的关注列表需要按照用户的关注顺序进行展示，粉丝列表需要将最
近关注的粉丝列在前面

twitter、新浪微博、腾讯微博中个人用户的关注列表需要按照用户的关注顺序进行展示，粉丝列表需要将最
近关注的粉丝列在前面
新闻、资讯类网站如何将最新的新闻或资讯按照发生的时间顺序展示？
企业运营过程中，系统将产生出大量的运营数据，如何保障多台服务器操作日志的统一顺序输出？

解决方案

- 依赖list的数据具有顺序的特征对信息进行管理
- 使用队列模型解决多路信息汇总合并的问题
- 使用栈模型解决最新消息的问题

tips：redis 应用于最新消息展示

## 3.5 Set

1. Redis set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以自动排重的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的
2. Redis的Set是string类型的无序集合。它底层其实是一个value为null的hash表,所以添加，删除，查找的复杂度都是O(1)。
3. 常用操作

list内部是链表结构，读取比较慢，所以想起set，高效查询。

把前面的hash表的field保留，value去除，即得到了set。

新的存储需求：存储大量的数据，在查询方面提供更高的效率
 需要的存储结构：能够保存大量的数据，高效的内部存储机制，便于查询
 set类型：与hash存储结构完全相同，仅存储键，不存储值（ nil），并且值是不允许重复的

| 命令        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| sadd …      | 将一个或多个 member 元素加入到集合 key 当中，已经存在于集合的 member 元素将被忽略。 |
| smembers    | 取出该集合的所有值。                                         |
| sismember   | 判断集合是否为含有该值，有返回1，没有返回0                   |
| scard       | 返回该集合的元素个数。                                       |
| srem …      | 删除集合中的某个元素。                                       |
| spop        | 随机从该集合中吐出一个值。                                   |
| srandmember | 随机从该集合中取出n个值。 不会从集合中删除                   |
| sinter      | 返回两个集合的交集元素。                                     |
| sunion      | 返回两个集合的并集元素。                                     |
| sdiff       | 返回两个集合的差集元素。                                     |

缺点: 用户ID数据冗余

- 第三种方案: 通过 key(用户ID) + field(属性标签) 就可以操作对应属性数据了，既不需要重复存储数据，也不会带来序列化和并发修改控制的问题

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzQyNDYucG5n)

1. 常用操作

| hset        | 给集合中的 键赋值                                            |
| ----------- | ------------------------------------------------------------ |
| hget        | 从集合 取出 value                                            |
| hmset …     | 批量设置hash的值                                             |
| hexists key | 查看哈希表 key 中，给定域 field 是否存在。                   |
| hkeys       | 列出该hash集合的所有field                                    |
| hvals       | 列出该hash集合的所有value                                    |
| hincrby     | 为哈希表 key 中的域 field 的值加上增量 increment             |
| hsetnx      | 将哈希表 key 中的域 field 的值设置为 value ，当且仅当域 field 不存在 |

扩展：

```sh
srandmember key [count] #随机获取集合中指定数量的数据
spop key [count] #随机获取集合中的某个数据并将该数据移除集合
12
# 求两个集合的交、并、差集
sinter key1 [key2]
sunion key1 [key2]
sdiff key1 [key2]

# 求两个集合的交、并、差集并存储到指定集合中
sinterstore destination key1 [key2]
sunionstore destination key1 [key2]
sdiffstore destination key1 [key2]

# 将指定数据从原始集合中移动到目标集合中
smove source destination member
123456789101112
```

场景：

每位用户首次使用今日头条时会设置3项爱好的内容，但是后期为了增加用户的活跃度、兴趣点，必须让用户对其他信息类别逐渐产生兴趣，增加客户留存度，如何实现？

业务分析

- 系统分析出各个分类的最新或最热点信息条目并组织成set集合
- 随机挑选其中部分信息
- 配合用户关注信息分类中的热点信息组织成展示的全信息集合

redis 应用于随机推荐类信息检索，例如热点歌单推荐，热点新闻推荐，热卖旅游线路，应用APP推荐，大V推荐等

场景2：

脉脉为了促进用户间的交流，保障业务成单率的提升，需要让每位用户拥有大量的好友，事实上职场新人不具有更多的职场好友，如何快速为用户积累更多的好友？
新浪微博为了增加用户热度，提高用户留存性，需要微博用户在关注更多的人，以此获得更多的信息或热门话题，如何提高用户关注他人的总量？
QQ新用户入网年龄越来越低，这些用户的朋友圈交际圈非常小，往往集中在一所学校甚至一个班级中，如何帮助用户快速积累好友用户带来更多的活跃度？
微信公众号是微信信息流通的渠道之一，增加用户关注的公众号成为提高用户活跃度的一种方式，如何帮助用户积累更多关注的公众号？
美团外卖为了提升成单量，必须帮助用户挖掘美食需求，如何推荐给用户最适合自己的美食？

Tips 9：
 redis 应用于同类信息的关联搜索，二度关联搜索，深度关联搜索
 显示共同关注（一度）
 显示共同好友（一度）
 由用户A出发，获取到好友用户B的好友信息列表（一度）
 由用户A出发，获取到好友用户B的购物清单列表（二度）
 由用户A出发，获取到好友用户B的游戏充值列表（二度）

set 类型数据操作的注意事项

- set 类型不允许数据重复，如果添加的数据在 set 中已经存在，将只保留一份
- set 虽然与hash的存储结构相同，但是无法启用hash中存储值的空间

场景3：

集团公司共具有12000名员工，内部OA系统中具有700多个角色， 3000多个业务操作， 23000多种数据，每位员工具有一个或多个角色，如何快速进行业务操作的权限校验？

场景4：

公司对旗下新的网站做推广，统计网站的PV（访问量） ,UV（独立访客） ,IP（独立IP）。
PV：网站被访问次数，可通过刷新页面提高访问量
UV：网站被不同用户访问的次数，可通过cookie统计访问量，相同用户切换IP地址， UV不变
IP：网站被不同IP地址访问的总次数，可通过IP地址统计访问量，相同IP不同用户访问， IP不变

解决方案
 利用set集合的数据去重特征，记录各种访问数据
 建立string类型数据，利用incr统计日访问量（ PV）
 建立set模型，记录不同cookie数量（ UV）
 建立set模型，记录不同IP数量（ IP）

tips：redis 应用于同类型数据的快速去重

场景5：

黑名单
资讯类信息类网站追求高访问量，但是由于其信息的价值，往往容易被不法分子利用，通过爬虫技术，快速获取信息，个别特种行业网站信息通过爬虫获取分析后，可以转换成商业机密进行出售。例如第三方火车票、机票、酒店刷票代购软件，电商刷评论、刷好评。
同时爬虫带来的伪流量也会给经营者带来错觉，产生错误的决策，有效避免网站被爬虫反复爬取成为每个网站都要考虑的基本问题。在基于技术层面区分出爬虫用户后，需要将此类用户进行有效的屏蔽，这就是黑名单的典型应用。
ps:不是说爬虫一定做摧毁性的工作，有些小型网站需要爬虫为其带来一些流量。

白名单
对于安全性更高的应用访问，仅仅靠黑名单是不能解决安全问题的，此时需要设定可访问的用户群体，
依赖白名单做更为苛刻的访问验证。

解决方案
 基于经营战略设定问题用户发现、鉴别规则
 周期性更新满足规则的用户黑名单，加入set集合
 用户行为信息达到后与黑名单进行比对，确认行为去向
 黑名单过滤IP地址：应用于开放游客访问权限的信息源
 黑名单过滤设备信息：应用于限定访问设备的信息源
 黑名单过滤用户：应用于基于访问权限的信息源

tips：redis 应用于基于黑名单与白名单设定的服务控制

## 3.6 zset (sorted set)

新的存储需求：数据排序有利于数据的有效展示，需要提供一种可以根据自身特征进行排序的方式
 需要的存储结构：新的存储模型，可以保存可排序的数据
 sorted_set类型：在set的存储结构基础上添加可排序字段

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMDMzMjMucG5n)

score不是数据。

1. Redis有序集合zset与普通集合set非常相似，是一个没有重复元素的字符串集合。不同之处是有序集合的每个成员都关联了一个评分（score） ，这个评分（score）被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的，但是评分可以是重复了 。
2. 因为元素是有序的, 所以你也可以很快的根据评分（score）或者次序（position）来获取一个范围的元素。访问有序集合的中间元素也是非常快的,因此你能够使用有序集合作为一个没有重复成员的智能列表。
3. 常用操作

| 命令                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| zadd …                                                       | 将一个或多个 member 元素及其 score 值加入到有序集 key 当中。注意分在前，值在后。 |
| zrange [WITHSCORES]                                          | 返回有序集 key 中，下标在 之间的元素 带WITHSCORES，可以让分数一起和值返回到结果集。 |
| zrangebyscore key min max [withscores] [limit offset count]  | 返回有序集 key 中，所有 score 值介于 min 和 max 之间(包括等于 min 或 max )的成员。有序集成员按 score 值递增(从小到大)次序排列。 |
| zrevrangebyscore key max min [withscores] [limit offset count] | 同上，改为从大到小排列。                                     |
| zincrby                                                      | 为元素的score加上增量                                        |
| zrem                                                         | 删除该集合下，指定值的元素                                   |
| zremrangebyrank/zremrangebyscore key start stop【或min max】 | 条件删除                                                     |
| zcount                                                       | 统计该集合，分数区间内的元素个数                             |
| zrank                                                        | 返回该值在集合中的排名，从0开始。                            |

```sh
 获取集合数据总量
zcard key
zcount key min max

 集合交、并操作
zinterstore destination numkeys key [key ...]
zunionstore destination numkeys key [key ...]
1234567
```

1. 思考: 如何利用zset实现一个文章访问量的排行榜?

```sh
127.0.0.1:6379> zadd test 94 zs
(integer) 1
127.0.0.1:6379> zadd test 100 ls
(integer) 1
127.0.0.1:6379> zadd test 60 ww
(integer) 1
127.0.0.1:6379> zadd test 47 zl
(integer) 1
127.0.0.1:6379> zrange test 0 -1
1) "zl"
2) "ww"
3) "zs"
4) "ls"
127.0.0.1:6379> zrange test 0 -1 withscores
1) "zl"
2) "47"
3) "ww"
4) "60"
5) "zs"
6) "94"
7) "ls"
8) "100"
12345678910111213141516171819202122
```

- min与max用于限定搜索查询的条件
- start与stop用于限定查询范围，作用于索引，表示开始和结束索引
- offset与count用于限定查询范围，作用于查询结果，表示开始位置和数据总量

场景1：

票选广东十大杰出青年，各类综艺选秀海选投票
各类资源网站TOP10（电影，歌曲，文档，电商，游戏等）
聊天室活跃度统计
游戏好友亲密度
业务分析
 为所有参与排名的资源建立排序依据

```sh
 获取数据对应的索引（排名）
zrank key member
zrevrank key member
 score值获取与修改
zscore key member
zincrby key increment member
123456
```

 redis 应用于计数器组合排序功能对应的排名

注意事项：

score保存的数据存储空间是64位，如果是整数范围是-9007199254740992~9007199254740992
 score保存的数据也可以是一个双精度的double值，基于双精度浮点数的特征，可能会丢失精度，使用时候要慎重
 sorted_set 底层存储还是基于set结构的，因此数据不能重复，如果重复添加相同的数据， score值将被反复覆盖，保留最后一次修改的结果

场景2：

基础服务+增值服务类网站会设定各位会员的试用，让用户充分体验会员优势。例如观影试用VIP、游戏VIP体验、云盘下载体验VIP、数据查看体验VIP。当VIP体验到期后，如果有效管理此类信息。即便对于正式VIP用户也存在对应的管理方式。
网站会定期开启投票、讨论，限时进行，逾期作废。如何有效管理此类过期信息。

解决方案：

对于基于时间线限定的任务处理，将处理时间记录为score值，利用排序功能区分处理的先后顺序
 记录下一个要处理的时间，当到期后处理对应任务，移除redis中的记录，并记录下一个要处理的时间
 当新任务加入时，判定并更新当前下一个要处理的任务时间
 为提升sorted_set的性能，通常将任务根据特征存储成若干个sorted_set。例如1小时内， 1天内，周内，月内，季内，年度等，操作时逐级提升，将即将操作的若干个任务纳入到1小时内处理的队列中

```sh
time # 获取当前系统时间
1
```

tips： redis 应用于定时任务执行顺序管理或任务过期管理

场景3：

任务/消息权重设定应用
当任务或者消息待处理，形成了任务队列或消息队列时，对于高优先级的任务要保障对其优先处理，如何实现任务权重管理。

解决方案：

对于带有权重的任务，优先处理权重高的任务，采用score记录权重即可多条件任务权重设定
如果权重条件过多时，需要对排序score值进行处理，保障score值能够兼容2条件或者多条件，例如外贸订单优先于国内订单，总裁订单优先于员工订单，经理订单优先于员工订单
 因score长度受限，需要对数据进行截断处理，尤其是时间设置为小时或分钟级即可（折算后）
 先设定订单类别，后设定订单发起角色类别，整体score长度必须是统一的，不足位补0。第一排序规则首位不得是0
 例如外贸101，国内102，经理004，员工008。
 员工下的外贸单score值为101008（优先）
 经理下的国内单score值为102004

## 数据库通用操作

key的重复问题：

key是由程序员定义的

- redis在使用过程中，伴随着操作数据量的增加，会出现大量的数据以及对应的key
- 数据不区分种类、类别混杂在一起，极易出现重复或冲突

解决方案：

redis为每个服务提供有16个数据库，编号从0到15

- 每个数据库之间的数据相互独立
- 这些数据库共用一块空间，没有空间大小之分

```sh
select index #切换数据库
quit
ping #返回PONG代表连通
echo

move key db #移动到另外一个库
dbsize
flushdb # 删除掉当前库数据
flushall # 删除掉所有库
123456789
```

# 第4章 Redis的相关配置

1. 计量单位说明,大小写不敏感
2. include

类似jsp中的include，多实例的情况可以把公用的配置文件提取出来

1. ip地址的绑定 bind

- 默认情况bind=127.0.0.1，只能接受本机的访问请求
- 不写的情况下，无限制接受任何ip地址的访问
- 生产环境肯定要写你应用服务器的地址
- 如果开启了protected-mode，那么在没有设定bind ip且没有设密码的情况下，Redis只允许接受本机的相应

1. tcp-backlog

- 可以理解是一个请求到达后至到接受进程处理前的队列.
- backlog队列总和=未完成三次握手队列 + 已经完成三次握手队列
- 高并发环境tcp-backlog 设置值跟超时时限内的Redis吞吐量决定

1. timeout

一个空闲的客户端维持多少秒会关闭，0为永不关闭。

1. tcp keepalive

对访问客户端的一种心跳检测，每个n秒检测一次，官方推荐设置为60秒

1. daemonize

是否为后台进程

1. pidfile

存放pid文件的位置，每个实例会产生一个不同的pid文件

1. log level

四个级别根据使用阶段来选择，生产环境选择notice 或者warning

1. log level

日志文件名称

1. syslog

是否将Redis日志输送到linux系统日志服务中

1. syslog-ident

日志的标志

1. syslog-facility

输出日志的设备

1. database

设定库的数量 默认16

1. security

在命令行中设置密码

```SH
config get requirepass
config set requirepass "123456"
config get requirepass
auth 123456
get kl
12345
```

1. maxclient

最大客户端连接数

1. maxmemory

设置Redis可以使用的内存量。一旦到达内存使用上限，Redis将会试图移除内部数据，移除规则可以通过maxmemory-policy来指定。如果Redis无法根据移除规则来移除内存中的数据，或者设置了“不允许移除”，

那么Redis则会针对那些需要申请内存的指令返回错误信息，比如SET、LPUSH等。

1. Maxmemory-policy

- volatile-lru：使用LRU算法移除key，只对设置了过期时间的键
- allkeys-lru：使用LRU算法移除key
- volatile-random：在过期集合中移除随机的key，只对设置了过期时间的键
- allkeys-random：移除随机的key
- volatile-ttl：移除那些TTL值最小的key，即那些最近要过期的key
- noeviction：不进行移除。针对写操作，只是返回错误信息

1. Maxmemory-samples

设置样本数量，LRU算法和最小TTL算法都并非是精确的算法，而是估算值，所以你可以设置样本的大小。

一般设置3到7的数字，数值越小样本越不准确，但是性能消耗也越小。

# 第5章 Redis的Java客户端 Jedis

可视化客户端：Redis Desktop Manager

```java
Jedis jedis = new Jedis("localhost",6379);
jedis.set("name","lisi");//方法名和原来的命令一致
jedis.get("name");
jedis.close();
//http://xetorthio.github.io/jedis/
12345
```

1. Jedis所需要的jar包 ,可通过Maven的依赖引入

```
Commons-pool-1.6.jar
Jedis-2.1.0.jar

maven为jedis，可选junit
1234
```

1. 使用Windows环境下Eclipse连接虚拟机中的Redis注意事项

- 禁用Linux的防火墙：Linux(CentOS7)里执行命令 ： systemctl stop firewalld.service
- redis.conf中注释掉bind 127.0.0.1 ,然后 protect-mode no。

```java
package com.itheima;

import org.junit.Test;
import redis.clients.jedis.Jedis;

import java.util.List;
import java.util.Map;

public class JedisTest {
    @Test
    public void testJedis(){
        //1.连接redis
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        //2.操作redis
//        jedis.set("name","itheima");
        String name = jedis.get("name");
        System.out.println(name);
        //3.关闭连接
        jedis.close();
    }

    @Test
    public void testList(){
        //1.连接redis
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        //2.操作redis
        jedis.lpush("list1","a","b","c");
        jedis.rpush("list1","x");

        List<String> list1 = jedis.lrange("list1", 0, -1);
        for(String s : list1){
            System.out.println(s);
        }

        System.out.println(jedis.llen("list1"));

        System.out.println();
        //3.关闭连接
        jedis.close();
    }

    @Test
    public void testHash(){
        //1.连接redis
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        //2.操作redis

        jedis.hset("hash1","a1","b1");
        jedis.hset("hash1","a2","a2");
        jedis.hset("hash1","a3","b3");

        Map<String, String> hash1 = jedis.hgetAll("hash1");

        System.out.println(hash1);

        System.out.println(jedis.hlen("hash1"));

        System.out.println();
        //3.关闭连接
        jedis.close();
    }

}
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263
```

① 设定一个服务方法，用于模拟实际业务调用的服务，内部采用打印模拟调用
② 在业务调用前服务调用控制单元，内部使用redis进行控制，参照之前的方案
③ 对调用超限使用异常进行控制，异常处理设定为打印提示信息
④ 主程序启动3个线程，分别表示3种不同用户的调用

```java
package com.itheima;

import com.itheima.util.JedisUtils;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.exceptions.JedisDataException;

public class Service {
    private String id;
    private int num;

    public Service(String id,int num){
        this.id = id;
        this.num = num;
    }
    //控制单元
    public void service(){
//        Jedis jedis = new Jedis("127.0.0.1",6379);
        Jedis jedis = JedisUtils.getJedis();
        String value = jedis.get("compid:"+id);
        //判断该值是否存在
        try{
            if(value == null){
                //不存在，创建该值
                jedis.setex("compid:"+id,5,Long.MAX_VALUE-num+"");
            }else{
                //存在，自增，调用业务
                Long val = jedis.incr("compid:"+id);
                business(id,num-(Long.MAX_VALUE-val));
            }
        }catch (JedisDataException e){
            System.out.println("使用已经到达次数上限，请升级会员级别");
            return;
        }finally{
            jedis.close();
        }
    }
    //业务操作
    public void business(String id,Long val){
        System.out.println("用户:"+id+" 业务操作执行第"+val+"次");
    }
}

class MyThread extends Thread{
    Service sc ;
    public MyThread(String id,int num){
        sc = new Service(id,num);
    }
    public void run(){
        while(true){
            sc.service();
            try {
                Thread.sleep(300L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class Main{
    public static void main(String[] args) {
        MyThread mt1 = new MyThread("初级用户",10);
        MyThread mt2 = new MyThread("高级用户",30);
        mt1.start();
        mt2.start();
    }
}
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667
package com.itheima.util;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

import java.util.ResourceBundle;

public class JedisUtils {
    private static JedisPool jp = null;
    private static String host = null;
    private static int port;
    private static int maxTotal;
    private static int maxIdle;

    static {//定义放到外面
        ResourceBundle rb = ResourceBundle.getBundle("redis");//redis.properties
        host = rb.getString("redis.host");
        port = Integer.parseInt(rb.getString("redis.port"));
        maxTotal = Integer.parseInt(rb.getString("redis.maxTotal"));
        maxIdle = Integer.parseInt(rb.getString("redis.maxIdle"));
        JedisPoolConfig jpc = new JedisPoolConfig();
        jpc.setMaxTotal(maxTotal);
        jpc.setMaxIdle(maxIdle);
        jp = new JedisPool(jpc,host,port);
    }

    public static Jedis getJedis(){
        return jp.getResource();
    }
    public static void main(String[] args){
        JedisUtils.getJedis();
    }
}
// 原方法//这种每次都要拿一个连接池，没有效率，所以放到static代码块中
public static Jedis getJedis() {
    JedisPoolConfig jpc=new JedisPoolConfig();
    jpc.setMaxTotal(30);
    jpc.setMaxIdle(10);
    String host="127.0.0.1";
    int port=6379;
    JedisPool jp=new JedisPool(jpc,host,port);
    return jp.getResource();
}
1234567891011121314151617181920212223242526272829303132333435363738394041424344
```

redis.properties

```properties
redis.host=127.0.0.1
redis.port=6379
redis.maxTotal=30
redis.maxIdle=10
1234
```

# 第6章 Redis 事务

## 6.1 Redis中事务的定义

Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断

Redis事务的主要作用就是串联多个命令防止别的命令插队

基本操作：

```sh
multi # 开启事务
作用：设定事务的开启位置，此指令执行后，后续的所有指令均加入到事务中

exec # 执行事务
作用：设定事务的结束位置，同时执行事务。与multi成对出现，成对使用
注意：加入事务的命令暂时进入到任务队列中，并没有立即执行，只有执行exec命令才开始执行

discard # 取消事务
作用：终止当前事务的定义，发生在multi之后， exec之前
123456789
```

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMjU4NTEucG5n)

事务的注意事项

- 事务过程中，输入的语法错误：会造成取消事务，且整体事务的所有命令均不会执行。包括正确的指令也不执行
- 事务过程中，输入语法无误，但不符合逻辑：例如对list进行incr操作。能够正确运行的命令会执行，运行错误的命令不会被执行。注意：已经执行完毕的命令对应的数据不会自动回滚，需要程序员自己在代码中实现回滚。

```
手动进行事务回滚
 记录操作过程中被影响的数据之前的状态
单数据： string
多数据： hash、 list、 set、 zset

 设置指令恢复所有的被修改的项
单数据：直接set（注意周边属性，例如时效）
多数据：修改对应值或整体克隆复制
12345678
```

锁：

基于特定条件的事务执行

业务场景

天猫双11热卖过程中，对已经售罄的货物追加补货， 4个业务员都有权限进行补货。补货的操作可能是一系列的操作，牵扯到多个连续操作，如何保障不会重复操作？

业务分析：

 多个客户端有可能同时操作同一组数据，并且该数据一旦被操作修改后，将不适用于继续操作
 在操作之前锁定要操作的数据，一旦发生变化，终止当前操作

```sh
watch的变量发生变化了，那么事务将不会执行。例如多终端修改。

解决方案
# 对 key 添加监视锁，在执行exec前如果key发生了变化，终止事务执行  # 得写在multi外
watch key1 [key2……]

# 取消对【所有】 key 的监视
unwatch


Tips 18：redis 应用基于状态控制的批量任务执行
1234567891011
```

业务场景

天猫双11热卖过程中，对已经售罄的货物追加补货，且补货完成。客户购买热情高涨， 3秒内将所有商品购买完毕。本次补货已经将库存全部清空，如何避免最后一件商品不被多人同时购买？ 【超卖问题】

业务分析

 使用watch监控一个key有没有改变已经不能解决问题，此处要监控的是具体数据
 虽然redis是单线程的，但是多个客户端对同一数据同时进行操作时，如何避免不被同时修改？

watch的值是不停在改变的，1件的时候一个人买到其他人就消掉？他是监控一个值能不能变的，而不是监控其他人能不能改这个值的，

```sh
解决方案：

setnx lock-keyname value # 设置一个公共锁
#value值不重要，比如我们要锁name这个变量，那么就是setnx lock-name true
利用setnx命令的返回值特征，有值则返回设置失败，无值则返回设置成功
 对于返回设置成功的，拥有控制权，进行下一步的具体业务操作
 对于返回设置失败的，不具有控制权，排队或等待


操作完毕通过del操作释放锁

注意：上述解决方案是一种设计概念，依赖规范保障，具有风险性

Tips 19：
 redis 应用基于分布式锁对应的场景控制
123456789101112131415
set num 10
setnx lock-num 1
incrby num -1
del lock-num
如果别的终端在这个过程中页想加锁，别的终端是加不上的
。到时他得重新输入命令他再锁

1234567
```

业务场景：死锁

依赖分布式锁的机制，某个用户操作时对应客户端宕机，且此时已经获取到锁。如何解决？

业务分析

 由于锁操作由用户控制加锁解锁，必定会存在加锁后未解锁的风险
 需要解锁操作不能仅依赖用户控制，系统级别要给出对应的保底处理方案

```sh
setnx lock-name 1
此时停电了，没有释放锁。
所以得给锁加失效时间

解决方案：
setnx lock-name 1
expire lock-name 20 #20s
1234567
```

由于操作通常都是微秒或毫秒级，因此该锁定时间不宜设置过大。具体时间需要业务测试后确认。

- 例如：持有锁的操作最长执行时间127ms，最短执行时间7ms。
- 测试百万次最长执行时间对应命令的最大耗时，测试百万次网络延迟平均耗时
- 锁时间设定推荐：最大耗时*120%+平均网络延迟*110%
- 如果业务最大耗时<<网络平均延迟，通常为2个数量级，取其中单个耗时较长即可

## 6.2 multi 、exec、discard

1. 从输入Multi命令开始，输入的命令都会依次进入命令队列中，但不会执行，至到输入Exec后，Redis会将之前的命令队列中的命令依次执行。
2. 组队的过程中可以通过discard来放弃组队。

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzQ2MjIucG5n)

## 6.3 事务中的错误处理

1. 组队中某个命令出现了报告错误，执行时整个的所有队列会都会被取消。

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzQ2MzcucG5n)

1. 如果执行阶段某个命令报出了错误，则只有报错的命令不会被执行，而其他的命令都会执行，不会回滚。

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzQ3MjIucG5n)

## 6.4 为什么要做成事务?

1. 想想一个场景: 有很多人有你的账户，同时去参加双十一抢购

悲观锁(Pessimistic Lock), 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁

乐观锁(Optimistic Lock), 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量。Redis就是利用这种check-and-set机制实现事务的。

## 6.5 Redis事务的使用

1. WATCH key[key….]

在执行multi之前，先执行watch key1 [key2],可以监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。

1. unwatch

取消 WATCH 命令对所有 key 的监视。

如果在执行 WATCH 命令之后， EXEC 命令或 DISCARD 命令先被执行了的话，那么就不需要再执行 UNWATCH 了。

1. 三特性

l 单独的隔离操作

事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

l 没有隔离级别的概念

队列中的命令没有提交之前都不会实际的被执行，因为事务提交前任何指令都不会被实际执行，也就不存在“事务内的查询要看到事务里的更新，在事务外查询不能看到”这个让人万分头痛的问题

l 不保证原子性

Redis同一个事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚

## 6.6 Redis事务 秒杀案例

1. 解决计数器和人员记录的事务操作

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9vbGRHaXRodWIvMjAyMDAxMTAxMzQ4MzEucG5n)

1. 秒杀并发模拟 ab工具

l CentOS6 默认安装 ,CentOS7需要手动安装

l 联网: yum install httpd-tools

无网络: 进入cd /run/media/root/CentOS 7 x86_64/Packages

 顺序安装

 apr-1.4.8-3.el7.x86_64.rpm

 apr-util-1.5.2-6.el7.x86_64.rpm

 httpd-tools-2.4.6-67.el7.centos.x86_64.rpm

l ab –n 请求数 -c 并发数 -p 指定请求数据文件

-T “application/x-www-form-urlencoded” 测试的请求

1. 超卖问题
2. 请求超时问题

节省每次连接redis服务带来的消耗，把连接好的实例反复利用

连接池参数:

MaxTotal：控制一个pool可分配多少个jedis实例，通过pool.getResource()来获取；如果赋值为-1，则表示不限制；如果pool已经分配了MaxTotal个jedis实例，则此时pool的状态为exhausted。

maxIdle：控制一个pool最多有多少个状态为idle(空闲)的jedis实例；

MaxWaitMillis：表示当borrow一个jedis实例时，最大的等待毫秒数，如果超过等待时间，则直接抛JedisConnectionException；

testOnBorrow：获得一个jedis实例的时候是否检查连接可用性（ping()）；如果为true，则得到的jedis实例均是可用的；

1. 遗留问题

l LUA脚本

Lua 是一个小巧的脚本语言，Lua脚本可以很容易的被C/C++ 代码调用，也可以反过来调用C/C++的函数，Lua并没有提供强大的库，一个完整的Lua解释器不过200k，所以Lua不适合作为开发独立应用程序的语言，而是作为嵌入式脚本语言。

很多应用程序、游戏使用LUA作为自己的嵌入式脚本语言，以此来实现可配置性、可扩展性。这其中包括魔兽争霸地图、魔兽世界、博德之门、愤怒的小鸟等众多游戏插件或外挂

l LUA脚本在Redis中的优势

将复杂的或者多步的redis操作，写为一个脚本，一次提交给redis执行，减少反复连接redis的次数。提升性能。

LUA脚本是类似redis事务，有一定的原子性，不会被其他命令插队，可以完成一些redis事务性的操作

但是注意redis的lua脚本功能，只有在2.6以上的版本才可以使用。

l 利用lua脚本淘汰用户，解决超卖问题。

# 第7章 Redis 持久化

Redis提供了2个不同形式的持久化方式 RDB 和 AOF

- RDB：将当前数据状态进行保存，快照形式，存储数据结果，存储格式简单，关注点在数据
- AOF：将数据的操作过程进行保存，日志形式，存储操作过程，存储格式复杂，关注点在数据的操作过程

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMTU4MzEucG5n)

## 7.2 RDB

save命令：

```sh
save #手动执行一次保存操作
1
```

save指令操作配置：需要在conf文件中配置

- dbfilename dump.rdb
  - 说明：设置持久化后文件名（本地数据库文件名），默认值为 dump.rdb
  - 经验：通常设置为dump-端口号.rdb
- dir
  - 说明：设置存储.rdb、log等文件的路径
  - 经验：通常设置成存储空间较大的目录中，目录名称data
- rdbcompression yes
  - 说明：设置存储至本地数据库时是否压缩数据，默认为 yes，采用 LZF 压缩
  - 经验：通常默认为开启状态，如果设置为no，可以节省 CPU 运行时间，但会使存储的文件变大（巨大）
- rdbchecksum yes
  - 说明：设置是否进行RDB文件格式校验，该校验过程在写文件和读文件过程均进行
  - 经验：通常默认为开启状态，如果设置为no，可以节约读写性过程约10%时间消耗，但是存储一定的数据损坏风险

要点：

- save一次之后，删除rdb文件，重新save一次，rdb文件又出现（恢复）了
- 退出后再登录，还有数据

RDB工作原理：

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMjE3MTIucG5n)

如图，4个客户端分别输入指令，redis是单线程的，所以需要排序。

有个问题是如果save是第三个，save执行时间很长，所以后面的get需要等待很长时间。就会阻塞。所以线上不建议使用save指令，会很拖慢性能。

注意： save指令的执行会阻塞当前Redis服务器， 直到当前RDB过程完成为止， 有可能会造成长时间阻塞， 线上环境不建议使用。

bgsave指令：手动启动后台保存操作，但不是立即执行

```sh
bgsave # 后台save
1
```

bgsave原理：

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMjIyMzEucG5n)

发生bgsave后，返回一个消息，但并没有真正执行，redis会抽空去调用fork函数生成子进程，这个子进程不参与redis序列的命令操作，单独用一个子进程操作save，让子进程去创建rgb文件。做完之后返回消息告诉控制台。可以在日志文件中查看有backgroud saving terminated with succeess。

注意： bgsave命令是针对save阻塞问题做的优化。 Redis内部所有涉及到RDB操作都采用bgsave的方式， save命令可以放弃使用

bgsave相关配置：

```
dbfilename dump.rdb
dir
rdbcompression yes
rdbchecksum yes
stop-writes-on-bgsave-error yes
说明：后台存储过程中如果出现错误现象，是否停止保存操作
经验：通常默认为开启状态
1234567
```

但是save和bgsave都需要手动保存，难免疏忽，使用需要自动执行。

```sh
# 在配置文件中设置
save second changes
作用:满足限定时间范围内key的变化数量达到指定数量即进行持久化。即时间片内变化大才自动保存，是快照的思想。
 参数
second：监控时间范围
changes：监控key的变化量
 位置:在conf文件中进行配置
示例：
save 900 1 # 900s内变化一个就保存
save 300 10
save 60 10000

save配置相关配置：
dbfilename dump.rdb
dir
rdbcompression yes
rdbchecksum yes
1234567891011121314151617
```

配置文件中save原理

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMjM0MjAucG5n)

发送3条指令，每条都会返回个结果，通过结果判断这条指令算不算影响数量。不进行数据比对指的是两个set就都算

注意：

save配置要根据实际业务情况进行设置，频度过高或过低都会出现性能问题，结果可能是灾难性的
save配置中对于second与changes设置通常具有互补对应关系，尽量不要设置成包含性关系
save配置启动后执行的是bgsave操作

RDB三种保存方式对比：

| 方式           | save指令 | bgsave指令 | save配置 |
| -------------- | -------- | ---------- | -------- |
| 读写           | 同步     | 异步       |          |
| 阻塞客户端指令 | 是       | 否         |          |
| 额外内存消耗   | 否       | 是         |          |
| 启动新进程     | 否       | 是         |          |

RDB特殊启动形式：

- 全量复制：在主从复制中详细讲解
- 服务器运行过程中重启：debug reload
- 关闭服务器时指定保存数据：shutdown save

默认情况下执行shutdown命令时， 自动执行
bgsave(如果没有开启AOF持久化功能)

RDB优点：

RDB是一个紧凑压缩的二进制文件， 存储效率较高
 RDB内部存储的是redis在某个时间点的数据快照， 非常适合用于数据备份，全量复制等场景
 RDB恢复数据的速度要比AOF快很多
 应用：服务器中每X小时执行bgsave备份，并将RDB文件拷贝到远程机器中，用于灾难恢复。

RGB缺点：

RDB方式无论是执行指令还是利用配置，无法做到实时持久化，具有较大的可能性丢失数据
 bgsave指令每次运行要执行fork操作创建子进程， 要牺牲掉一些性能
 Redis的众多版本中未进行RDB文件格式的版本统一，有可能出现各版本服务之间数据格式无法兼容现象

1. 在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里。
2. 备份是如何执行的

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。

1. 关于fork

在Linux程序中，fork()会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，出于效率考虑，Linux中引入了“写时复制技术”，一般情况父进程和子进程会共用同一段物理内存，只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程。

1. RDB保存的文件

在redis.conf中配置文件名称，默认为dump.rdb

1. RDB文件的保存路径

默认为Redis启动时命令行所在的目录下,也可以修改

1. RDB的保存策略





1. 手动保存快照

save: 只管保存，其它不管，全部阻塞

bgsave:按照保存策略自动保存

1. RDB的相关配置

l stop-writes-on-bgsave-error yes

当Redis无法写入磁盘的话，直接关掉Redis的写操作

l rdbcompression yes

进行rdb保存时，将文件压缩

l rdbchecksum yes

在存储快照后，还可以让Redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能

1. RDB的备份 与恢复

l 备份:先通过config get dir 查询rdb文件的目录 , 将*.rdb的文件拷贝到别的地方

l 恢复: 关闭Redis，把备份的文件拷贝到工作目录下,启动redis,备份数据会直接加载。

1. RDB的优缺点

l 优点: 节省磁盘空间,恢复速度快.

l 缺点: 虽然Redis在fork时使用了写时拷贝技术,但是如果数据庞大时还是比较消耗性能。 在备份周期在一定间隔时间做一次备份，所以如果Redis意外down掉的话，就

 会丢失最后一次快照后的所有修改

## 7.2 AOF

RDB弊端：

存储数据量较大，效率较低
基于快照思想，每次读写都是全部数据，当数据量巨大时，效率非常低
 大数据量下的IO性能较低
 基于fork创建子进程，内存产生额外消耗
 宕机带来的数据丢失风险

解决思路：

不写全数据，仅记录部分数据
 降低区分数据是否改变的难度，改记录数据为记录操作过程
 对所有操作均进行记录，排除丢失数据的风险

什么是AOF：

- AOF(append only file)持久化：以独立日志的方式记录每次写命令，重启时再重新执行AOF文件中命令达到恢复数据的目的。与RDB相比可以简单描述为改记录数据为记录数据产生的过程
- AOF的主要作用是解决了数据持久化的实时性，目前已经是Redis持久化的主流方式

AOF写数据过程：

1. 当客户端发出一条指令给服务器时，服务器收到并没有马上记录，而是放到临时区域:刷新缓存区，缓存区是最终存成文件时用的。
   ![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMjQ2MTEucG5n)

AOF写数据三种策略(appendfsync)

- always(每次）
  每次写入操作均同步到AOF文件中， 数据零误差，性能较低。不建议使用
- everysec（每秒）
  每秒将缓冲区中的指令同步到AOF文件中，数据准确性较高，性能较高
  在系统突然宕机的情况下丢失1秒内的数据
- no（系统控制）
  由操作系统控制每次同步到AOF文件的周期，整体过程不可控

AOF功能开启：

```
配置
appendonly yes|no
 作用：是否开启AOF持久化功能，默认：为不开启状态

 配置：appendfsync always|everysec|no
- 作用：AOF写数据策略

配置
appendfilename filename
 作用：AOF持久化文件名，默认文件名未appendonly.aof，建议配置为appendonly-端口号.aof

 配置
dir
 作用
AOF持久化文件保存路径，与RDB持久化文件保持一致即可
123456789101112131415
```

AOF写数据遇到的问题：

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMjQ5MzQucG5n)

AOF重写
随着命令不断写入AOF，文件会越来越大，为了解决这个问题， Redis引入了AOF重写机制压缩文件体积。 AOF文件重
写是将Redis进程内的数据转化为写命令同步到新AOF文件的过程。 简单说就是将对同一个数据的若干个条命令执行结
果转化成最终结果数据对应的指令进行记录。

AOF重写作用

- 降低磁盘占用量，提高磁盘利用率
- 提高持久化效率，降低持久化写时间，提高IO性能
- 降低数据恢复用时，提高数据恢复效率

AOF重写规则

- 进程内已超时的数据不再写入文件
- 忽略无效指令，重写时使用进程内数据直接生成，这样新的AOF文件只保留最终数据的写入命令
  如del key1、 hdel key2、 srem key3、 set key4 111、 set key4 222等
- 对同一数据的多条写命令合并为一条命令
  如lpush list1 a、 lpush list1 b、 lpush list1 c 可以转化为： lpush list1 a b c。
  为防止数据量过大造成客户端缓冲区溢出，对list、 set、 hash、 zset等类型， 每条指令最多写入64个元素

AOF重写方式：

```
手动重写
命令行输入bgrewriteaof

自动重写
配置文件里写
auto-aof-rewrite-min-size size
auto-aof-rewrite-percentage percentage  
1234567
```

RDB启动方式 —— bgsave指令工作原理

AOF手动重写 —— bgrewriteaof指令工作原理

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMjUxMjIucG5n)

AOF自动重写方式：

```
 自动重写触发条件设置
auto-aof-rewrite-min-size 文件大小size
auto-aof-rewrite-percentage 比例percent

 自动重写触发比对参数（ 运行指令info Persistence获取具体信息 ）
aof_current_size
aof_base_size

 自动重写触发条件

12345678910
```

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMjUzMjkucG5n)

AOF工作流程：

Always时，Set正常执行，另开一个子进程进行重写。
Sec时会先放到缓存区。

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMjU0MDUucG5n)

AOF重写流程：

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMjU0MjUucG5n)

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIwMjU0NTUucG5n)

AOF缓冲区同步文件策略， 由参数appendfsync控制
系统调用write和fsync说明：

 write操作会触发延迟写（ delayed write） 机制， Linux在内核提供页缓冲区用来提高硬盘IO性能。 write操作在写入系统缓冲区后直接返回。 同步硬盘操作依赖于系统调度机制， 列如：缓冲区页空间写满或达到特定时间周期。 同步文件之前， 如果此时系统故障宕机， 缓冲区内数据将丢失。

 fsync针对单个文件操作（ 比如AOF文件） ， 做强制硬盘同步， fsync将阻塞知道写入硬盘完成后返回， 保证了数据持久化。

除了write、 fsync、 Linx还提供了sync、 fdatasync操作， 具体API说明参见：

AOF文件的保存路径，同RDB的路径一致

1. AOF和RDB同时开启，redis听谁的？
2. AOF文件故障备份

AOF的备份机制和性能虽然和RDB不同, 但是备份和恢复的操作同RDB一样，都是拷贝备份文件，需要恢复时再拷贝到Redis工作目录下，启动系统即加载

1. AOF文件故障恢复

如遇到AOF文件损坏，可通过

redis-check-aof --fix appendonly.aof 进行恢复

1. AOF同步频率设置



l 始终同步，每次Redis的写入都会立刻记入日志

l 每秒同步，每秒记入日志一次，如果宕机，本秒的数据可能丢失。

l 把不主动进行同步，把同步时机交给操作系统。

1. Rewrite

l AOF采用文件追加方式，文件会越来越大为避免出现此种情况，新增了重写机制,当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集.可以使用命令bgrewriteaof。

l Redis如何实现重写

AOF文件持续增长而过大时，会fork出一条新进程来将文件重写(也是先写临时文件最后再rename)，遍历新进程的内存中数据，每条记录有一条的Set语句。重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件，这点和快照有点类似。

l 何时重写

重写虽然可以节约大量磁盘空间，减少恢复时间。但是每次重写还是有一定的负担的，因此设定Redis要满足一定条件才会进行重写。



系统载入时或者上次重写完毕时，Redis会记录此时AOF大小，设为base_size,如果Redis的AOF当前大小>= base_size +base_size*100% (默认)且当前大小>=64mb(默认)的情况下，Redis会对AOF进行重写。

1. AOF的优缺点

l 优点:

备份机制更稳健，丢失数据概率更低。

可读的日志文本，通过操作AOF稳健，可以处理误操作。

l 缺点:

比起RDB占用更多的磁盘空间

恢复备份速度要慢

每次读写都同步的话，有一定的性能压力。

## 7.3 RDB和AOF 用哪个好

| 持久化方式   | RDB                | AOF                |
| ------------ | ------------------ | ------------------ |
| 占用存储空间 | 小（数据级：压缩） | 大（指令级：重写） |
| 存储速度     | 慢                 | 快                 |
| 恢复速度     | 快                 | 慢                 |
| 数据安全性   | 会丢失数据         | 依据策略决定       |
| 资源消耗     | 高/重量级          | 低/轻量级          |
| 启动优先级   | 低                 | 高                 |

对数据非常敏感， 建议使用默认的AOF持久化方案
 AOF持久化策略使用everysecond，每秒钟fsync一次。该策略redis仍可以保持很好的处理性能， 当出
现问题时，最多丢失0-1秒内的数据。
 注意：由于AOF文件存储体积较大，且恢复速度较慢
 数据呈现阶段有效性，建议使用RDB持久化方案
 数据可以良好的做到阶段内无丢失（该阶段是开发者或运维人员手工维护的），且恢复速度较快，阶段
点数据恢复通常采用RDB方案
 注意：利用RDB实现紧凑的数据持久化会使Redis降的很低，慎重总结：
 综合比对
 RDB与AOF的选择实际上是在做一种权衡，每种都有利有弊
 如不能承受数分钟以内的数据丢失，对业务数据非常敏感， 选用AOF
 如能承受数分钟以内的数据丢失， 且追求大数据集的恢复速度， 选用RDB
 灾难恢复选用RDB
 双保险策略， 同时开启 RDB 和 AOF， 重启后， Redis优先使用 AOF 来恢复数据，降低丢失数据的量

l 官方推荐两个都启用。

l 如果对数据不敏感，可以选单独用RDB

l 不建议单独用 AOF，因为可能会出现Bug。

l 如果只是做纯内存缓存，可以都不用

# 删除策略

Redis是一种内存级数据库，所有数据均存放在内存中，内存中的数据可以通过TTL指令获取其状态

- XX ：具有时效性的数据
- -1 ：永久有效的数据
- -2 ：已经过期的数据 或 被删除的数据 或 未定义的数据

过期的数据真的被删除了吗？扔垃圾我们往往都是过会再扔。等CPU空闲时候再处理扔。

### 数据删除策略

- \1. 定时删除
- \2. 惰性删除
- \3. 定期删除

### 时效性数据的存储结构

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxMjU2MTEucG5n)

这四种操作会给key设置一个过期时间，这个过期时间存放在expires区域里。右面的1359…是一个系统时间点

数据删除策略的目标：在内存占用与CPU占用之间寻找一种平衡，顾此失彼都会造成整体redis性能的下降，甚至引发服务器宕机或内存泄露

### 定时删除

 创建一个定时器，当key设置有过期时间，且过期时间到达时，由定时器任务立即执行对键的删除操作。此时存储空间的东西也删除了expires空间的内容也删除了。

 优点：节约内存，到时就删除，快速释放掉不必要的内存占用

 缺点： CPU压力很大，无论CPU此时负载量多高，均占用CPU去进行删除，会影响redis服务器响应时间和指令吞吐量

 总结：用处理器性能换取存储空间（拿时间换空间）

### 惰性删除

数据到达过期时间，不做处理（此时还在expires区里存在）。等下次访问该数据时

- 如果未过期，返回数据
- 发现已过期，删除，返回不存在

 优点：节约CPU性能，发现必须删除的时候才删除
 缺点：内存压力很大，出现长期占用内存的数据
 总结：用存储空间换取处理器性能（拿时间换空间）

内部：有个expireIfNeeded()函数，调用获取函数时（获取值），均会执行这个操作。

### 定期删除

两种方案都走极端，有没有折中方案？

每个库都有一个expire区，expire[0]…expire[15]

- Redis启动服务器初始化时，读取配置server.hz的值，默认为10。（通过`info server`查询），该值代表每秒对16个库整体进行的查询次数。
- 每秒钟执行server.hz(10)次==serverCron()==对服务器进行定时轮询。其中会调用：==databaseCron()==继续对每个库进行轮询，该函数会调用：==activeExporeCycle()==对变量进行检查
- activeExpireCycle()对每个expires[0]里的变量逐一进行检测，每次执行250ms/server.hz
- 对某个expires[0]检测时，随机挑选W个key检测
  - W=ACTIVE_EXPIRE_CYCLE_LOOKUPS_PER_LOOP属性值（配置文件中）
  - 对这W个key进行以下操作：
  - 如果key超时，删除key
  - 如果一轮中删除的key的数量>W*25%，说明删除的量比较大，很可能还有很多没删除的，循环该过程
  - 如果一轮中删除的key的数量≤W*25%，检查下一个expires[1]，如此轮询16个库， 0-15循环
- 那么下次轮询从几号库开始查询呢？：参数current_db用于记录activeExpireCycle() 进入哪个expires[] 执行。如果activeExpireCycle()执行时间到期，下次从current_db继续向下执行。取值0-15？

定期删除总结：

 周期性轮询redis库中的时效性数据，采用随机抽取的策略，利用过期数据占比的方式控制删除频度
 特点1： CPU性能占用设置有峰值，检测频度可自定义设置
 特点2：内存压力不是很大，长期占用内存的冷数据会被持续清理
 总结：周期性抽查存储空间（随机抽查，重点抽查）

### 删除策略比对

\1. 定时删除

 节约内存，无占用  不分时段占用CPU资源，频度高  拿时间换空间

\2. 惰性删除

 内存占用严重  延时执行， CPU利用率高  拿空间换时间

\3. 定期删除

 内存定期随机清理 每秒花费固定的CPU资源维护内存  随机抽查， 重点抽查

redis里用后两种

### 逐出算法：

新数据进入检测：当新数据进入redis时，如果内存不足怎么办？expire控制的是过期数据，如果都不会过期怎么办？

 Redis使用内存存储数据，在执行每一个命令前，会调用==freeMemoryIfNeeded()==检测内存是否充足。如果内存不满足新加入数据的最低存储要求， redis要临时删除一些数据为当前指令清理存储空间。清理数据的策略称为逐出算法（临时淘汰）。

 注意：逐出算法不一定肯定成功：逐出数据的过程不是100%能够清理出足够的可使用的内存空间，如果不成功则反复执行。当对所有数据尝试完毕后，如果不能达到内存清理的要求，将出现错误信息：

(error) OOM command not allowed when used memory >‘maxmemory’

#### 影响数据逐出的相关配置：

 最大可使用内存：maxmemory

占用物理内存的比例，默认值为0，表示不限制，全用掉内存。生产环境中根据需求设定，通常设置在50%以上。

 每次选取待删除数据的个数：maxmemory-samples

选取数据时并不会全库扫描，导致严重的性能消耗，降低读写性能。因此采用随机获取数据的方式作为待检测删除数据

 删除策略，删除哪个：maxmemory-policy

达到最大内存后的，对被挑选出来的数据进行删除的策略

#### 影响数据逐出的相关配置

 检测易失数据（可能会过期的数据集server.db[i].expires ）
① volatile-lru：挑选最近最少使用的数据淘汰。Least Recently Used
② volatile-lfu：挑选最近使用次数最少的数据淘汰。Least Frequently Used
③ volatile-ttl：挑选将要过期的数据淘汰
④ volatile-random：任意选择数据淘汰

 检测当前库全库数据（所有数据集server.db[i].dict ）
⑤ allkeys-lru：挑选最近最少使用的数据淘汰
⑥ allkeys-lfu：挑选最近使用次数最少的数据淘汰
⑦ allkeys-random：任意选择数据淘汰

 放弃数据驱逐
⑧ no-enviction（驱逐）：禁止驱逐数据（ redis4.0中默认策略），会引发错误OOM（ Out Of Memory）

如何配置：在配置文件中：

```
maxmemory-policy  volatile-lru
1
```

数据逐出策略配置依据：

 使用INFO命令输出监控信息，查询缓存 hit 和 miss 的次数，根据业务需求调优Redis配置

# 服务器配置

```sh
# -----服务器端设置-----
daemonize yes|no # 设置服务器以守护进程的方式运行

bind 127.0.0.1 # 绑定主机地址，如果不绑别人也能访问

port 6379 # 设置服务器端口号

databases 16  # 设置数据库数量


# -----日志配置-----
loglevel debug|verbose|notice|warning #  设置服务器以指定日志记录级别

logfile 端口号.log  #  日志记录文件名

#  -----客户端配置-----
maxclients 0  # 设置同一时间最大客户端连接数，默认无限制。当客户端连接到达上限， Redis会关闭新的连接
# 注意：日志级别开发期设置为verbose即可，生产环境中配置为notice，简化日志输出量，降低写日志IO的频度  
timeout 300 #  客户端闲置等待最大时长，达到最大值后关闭连接。如需关闭该功能，设置为 0  
12345678910111213141516171819
```

多服务器快捷配置：

 导入并加载指定配置文件信息，用于快速创建redis公共配置较多的redis实例配置文件，便于维护

include /path/server-端口号.conf

相当于继承

# 高级数据模型

### Bitmaps

电影网站是有资源常年没有人浏览，可以删除。年度浏览最低起，月浏览量最低。我们用计算机上最小单位bit存储状态。用string

Bitmaps类型的基础操作：

-  获取指定key对应偏移量上的bit值 :getbit key offset
-  设置指定key对应偏移量上的bit值， value只能是1或0。setbit key offset value 如`setbit 20200606 0 1`，就代表这个字符串的第0位为1

Bitmaps类型的扩展操作

业务场景
电影网站
 统计每天某一部电影是否被点播
 统计每天有多少部电影被点播
 统计每周/月/年有多少部电影被点播
 统计年度哪部电影没有被点播

第二列为每日的浏览状态，那么进行或操作的第3列就是每月浏览状态

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxMzA4NDcucG5n)

Bitmaps类型的扩展操作
 对指定key按位进行交、并、非、异或操作，并将结果保存到destKey中

bitop or destKey key1 [key2…]。如`bitop or 202006 20200606 20200607`

 and：交
 or：并
 not：非
 xor：异或
 统计指定key中1的数量

bitcount key [start end]

Tips 21：
 redis 应用于信息状态统计

### HyperLogLog：

用处：统计独立UV

 原始方案： set
 存储每个用户的id（字符串）
 改进方案： Bitmaps
 存储每个用户状态（ bit）
 全新的方案： Hyperloglog

基数：基数是数据集去重后元素个数，即set
 HyperLogLog 是用来做基数统计的，运用了LogLog的算法

HyperLogLog类型的基本操作
 添加数据

pfadd key element [element …]
 统计数据

pfcount key [key …]

 合并数据

pfmerge destkey sourcekey [sourcekey…]

Tips 22：
 redis 应用于独立信息统计

相关说明
 用于进行基数统计，不是集合，不保存数据，只记录数量而不是具体数据
 核心是基数估算算法，最终数值存在一定误差
 误差范围：基数估计的结果是一个带有 0.81% 标准错误的近似值
 耗空间极小，每个hyperloglog key占用了12K的内存用于标记基数
 pfadd命令不是一次性分配12K内存使用，会随着基数的增加内存逐渐增大
 Pfmerge命令合并后占用的存储空间为12K，无论合并之前数据量多少

### GEO：

GEO类型的基本操作

 添加坐标点

geoadd key longitude latitude member [longitude latitude member …]

 获取坐标点

geopos key member [member …]

 计算坐标点距离

geodist key member1 member2 [unit]

GEO类型的基本操作
 添加坐标点

georadius key longitude latitude radius m|km|ft|mi [withcoord] [withdist] [withhash] [count count]

 获取坐标点

georadiusbymember key member radius m|km|ft|mi [withcoord] [withdist] [withhash] [count count]

 计算经纬度

geohash key member [member …]

Tips 23：
 redis 应用于地理位置计算

# 第8章 Redis主从复制

高并发、高性能、高可用

单机redis的风险与问题
 问题1.机器故障
 现象： 硬盘故障、系统崩溃
 本质： 数据丢失，很可能对业务造成灾难性打击
 结论： 基本上会放弃使用redis.
 问题2.容量瓶颈
 现象：内存不足，从16G升级到64G，从64G升级到128G，无限升级内存
 本质：穷，硬件条件跟不上
 结论：放弃使用redis
 结论：
为了避免单点Redis服务器故障，准备多台服务器，互相连通。 将数据复制多个副本保存在不同的服务器上， 连接在一起， 并保证数据是同步的。 即使有其中一台服务器宕机，其他服务器依然可以继续提供服务，实现Redis的高可用， 同时实现数据冗余备份

多台服务器连接方案
收集数据（写）
提供数据（读） 提供数据（读） 提供数据（读）
同步数据
master
slave

 提供数据方： master
主服务器，主节点，主库
主客户端

 接收数据方： slave
从服务器，从节点，从库
从客户端

 需要解决的问题：数据同步

 核心工作：master的数据复制到slave中

## 8.1 什么是主从复制

主从复制：即将master中的数据即时、有效的复制到slave中

特征：一个master可以拥有多个slave，一个slave只对应一个master。Master以写为主，Slave以读为主。

master和slave是个相对的概念，下面的slave还可以当做下一级的master

职责：

- master:
   写数据
   执行写操作时，将出现变化的数据自动同步到slave
   读数据（可忽略）
- slave:
   读数据
   写数据（禁止）

## 8.2 主从复制的目的

主从复制的作用：

 读写分离： master写、 slave读，提高服务器的读写负载能力
 负载均衡： 基于主从结构，配合读写分离，由slave分担master负载，并根据需求的变化，改变slave的数量，通过多个从节点分担数据读取负载，大大提高Redis服务器并发量与数据吞吐量
 故障恢复：当master出现问题时，由slave提供服务，实现快速的故障恢复
 数据冗余：实现数据热备份，是持久化之外的一种数据冗余方式
 高可用基石： 基于主从复制，构建哨兵模式与集群，实现Redis的高可用方案

## 8.3 主从配置

主从复制过程大体可以分为3个阶段
 建立连接阶段（即准备阶段）
 数据同步阶段
 命令传播阶段

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjA1MzQucG5n)

### 阶段一：建立连接阶段

 建立slave到master的连接，使master能够识别slave， 并保存slave端口号

建立连接阶段工作流程

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjA3MzYucG5n)

步骤1：设置master的地址和端口，连接成功就保存master信息
步骤2：建立socket连接，以后通过这个通道传数据
步骤3：发送ping命令（定时器任务），周期性地向master发生指令，验证master还在不在。master返回pong
步骤4：身份验证
步骤5：发送slave端口信息，这样master保存了slave，回头找slave就简单了
至此，主从连接成功！

状态：
slave：保存master的地址与端口
master：保存slave的端口
总体：之间创建了连接的socket

#### 主从连接（ slave连接master）

 方式一：客户端发送命令 `slaveof <masterip> <masterport>`

 方式二：启动服务器参数 ：启动redis时在命令行后面加上参数：`redis-server conf文件 -slaveof <masterip> <masterport>` 。（此时在前台启动server能看到slave正在连接的一些信息）

 方式三：服务器配置（主流方式），在配置文件里添加：`slaveof <masterip> <masterport>`

 slave系统信息
 master_link_down_since_seconds
 masterhost
 masterport

master系统信息
 slave_listening_port(多个)

#### 主从断开连接

 客户端发送命令：`slaveof no one`
 说明：
slave断开连接后，不会删除已有数据，只是不再接受master发送的数据

#### 授权访问

 master客户端发送命令设置密码`requirepass <password>`
 master配置文件设置密码
`config set requirepass <password>`
`config get requirepass`

 slave客户端发送命令设置密码 `auth <password>`
 slave配置文件设置密码 `masterauth <password>`
 slave启动服务器设置密 `redis-server –a <password>`

### 阶段二：同步阶段

思想：在slave初次连接master后，复制master中的所有数据到slave，将slave的数据库状态更新成master当前的数据库状态 。接下来就是只复制更改的部分了。

数据同步阶段工作流程：
步骤1： 请求同步数据
步骤2： 创建RDB同步数据
步骤3： 恢复RDB同步数据
步骤4： 请求部分同步数据
步骤5： 恢复部分同步数据

达到状态：
slave：具有master端全部数据，包含RDB过程接收的数据
master：保存slave当前数据同步的位置

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjExMzAucG5n)

注意：上面的缓冲区只在部分复制时才起作用，他里面是AOF指令，而不是数据快照。

#### 数据同步阶段master说明

\1. 如果master数据量巨大，数据同步阶段应避开流量高峰期，避免造成master阻塞，影响业务正常执行
\2. 复制缓冲区大小设定不合理，会导致数据溢出，后进的指令会冲掉先进的指令。如进行全量复制周期太长，进行部分复制时又出现数据已经存在丢失的情况，必须进行第二次全量复制，致使slave陷入死循环状态。在master端更改：`repl-backlog-size 1mb`

\3. master单机内存占用主机内存的比例不应过大，建议使用50%-70%的内存，留下30%-50%的内存用于执
行bgsave命令和创建复制缓冲区

#### ![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjEyMTgucG5n)

#### 数据同步阶段slave说明

\1. 为避免slave进行全量复制、部分复制时服务器响应阻塞或数据不同步，建议关闭此期间的对外服务。

```
slave-serve-stale-data yes|no
```

\2. 数据同步阶段， master发送给slave信息可以理解master是slave的一个客户端，主动向slave发送命令
\3. 多个slave同时对master请求数据同步， master发送的RDB文件增多， 会对带宽造成巨大冲击， 如果master带宽不足， 因此数据同步需要根据业务需求， 适量错峰
\4. slave过多时， 建议调整拓扑结构，由一主多从结构变为树状结构， 中间的节点既是master，也是slave。注意使用树状结构时，由于层级深度，导致深度越高的slave与最顶层master间数据同步延迟较大， 数据一致性变差， 应谨慎选择

### 阶段三：命令传播阶段

 当master数据库状态被修改后，导致主从服务器数据库状态不一致，此时需要让主从数据同步到一致的状态，同步的动作称为命令传播
 master将接收到的数据变更命令发送给slave， slave接收命令后执行命令

#### 命令传播阶段的部分复制

命令传播阶段出现了断网现象
 网络闪断闪连 忽略
 短时间网络中断 部分复制
 长时间网络中断 全量复制

部分复制的三个核心要素
 服务器的运行 id（ run id）
 主服务器的复制积压缓冲区
 主从服务器的复制偏移量

#### 服务器运行ID（ runid）

 概念：服务器运行ID是每一台服务器每次运行的身份识别码，一台服务器多次运行可以生成多个运行id。每个计算机也有自己的运行ID，slave也有。
 组成：运行id由40位字符组成，是一个随机的十六进制字符
例如： fdc9ff13b9bbaab28db42b3d50f852bb5e3fcdce

 作用：运行id被用于在服务器间进行传输，识别身份
如果想两次操作均对同一台服务器进行，必须每次操作携带对应的运行id，用于对方识别。master发送ID，slave比对ID，

 实现方式： 运行id在每台服务器启动时自动生成的， master在首次连接slave时，会将自己的运行ID发
送给slave， slave保存此ID，通过info Server命令，可以查看节点的runid

#### 复制缓冲区

 概念：复制缓冲区，又名复制积压缓冲区，是一个先进先出（ FIFO）的队列， 用于存储服务器执行过的命令， 每次传播命令（命令传播程序负责）， master都会将传播的命令记录下来， 并存储在复制缓冲区 。

如果一台slave断网，那么此时命令收不到，但别的slave收到了，所以个slave创建了一个复制缓冲区：FIFO队列。

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjE1MDcucG5n)

 复制缓冲区默认数据存储空间大小是1M，由于存储空间大小是固定的，当入队元素的数量大于队列长度时，最先入队的元素会被弹出，而新元素会被放入队列
 由来：每台服务器启动时，如果开启有AOF或被连接成为master节点， 即创建复制缓冲区

 作用：用于保存master收到的所有指令（仅影响数据变更的指令，例如set， select）

 数据来源：当master接收到主客户端的指令时，除了将指令执行，会将该指令存储到缓冲区中

#### 复制缓冲区内部工作原理

组成
 偏移量
 字节值

工作原理：

master先把他放到复制缓冲区，不是放整个命令，而是放入AOF日志文件里的$3 \r \n,set等。此时将缓冲区分格，每个格放一个字符。上面的类似索引的"偏移量"识别当前slave执行到哪里了。所以不同slave的偏移量是不一样的。

- 通过offset区分不同的slave当前数据传播的差异
- master记录已发送的信息对应的offset
- slave记录已接收的信息对应的offset

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjE1NTMucG5n)

#### 主从服务器复制偏移量（ offset）

概念：一个数字，描述复制缓冲区中的指令字节位置

分类：
 master复制偏移量：记录发送给所有slave的指令字节对应的位置（多个）
 slave复制偏移量：记录slave接收master发送过来的指令字节对应的位置（一个）

 数据来源：
master端：发送一次记录一次
slave端：接收一次记录一次

 作用：同步信息，比对master与slave的差异，当slave断线后，恢复数据使用

#### 数据同步+命令传播阶段工作流程

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjE2NTIucG5n)

master发过来的为红的offset，slave记下来的为蓝的offset。

如果ID不匹配。那么就触发全部复制。或者slave传过来的offset没了（在master里找不到这个偏移量了，被队列挤出去了），那么就触发全量复制。

### 心跳机制

心跳机制：进入命令传播阶段候， master与slave间需要进行信息交换，使用心跳机制进行维护，实现双方连接保持在线

master心跳：
 指令： PING
 周期：由repl-ping-slave-period决定，默认10秒
 作用：判断slave是否在线
 查询： INFO replication 获取slave最后一次连接时间间隔， lag项维持在0或1视为正常

slave心跳任务
 指令： REPLCONF ACK {offset}
 周期： 1秒
 作用1：汇报slave自己的复制偏移量，获取最新的数据变更指令
 作用2：判断master是否在线

心跳阶段注意事项

 当slave多数掉线，或延迟过高时， master为保障数据稳定性，将拒绝所有信息同步操作。都掉了就没有同步的意义了。或者同步完成时间太久了

```sh
min-slaves-to-write 2 # 少于2时就不再写了
min-slaves-max-lag 8  # 链接延迟，在info里可以看到
12
```

slave数量少于2个，或者所有slave的延迟都大于等于10秒时，强制关闭master写功能，停止数据同步
 slave数量由slave发送REPLCONF ACK命令做确认
 slave延迟由slave发送REPLCONF ACK命令做确认

主从复制总结：

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjE4MjEucG5n)

### 主从复制常见问题

这个地方还没看视频

#### 频繁的全量复制（ 1）

伴随着系统的运行， master的数据量会越来越大，一旦master重启， runid将发生变化，会导致全部slave的
全量复制操作
内部优化调整方案：
\1. master内部创建master_replid变量，使用runid相同的策略生成，长度41位，并发送给所有slave
\2. 在master关闭时执行命令 shutdown save，进行RDB持久化,将runid与offset保存到RDB文件中
 repl-id repl-offset
 通过redis-check-rdb命令可以查看该信息
\3. master重启后加载RDB文件， 恢复数据
重启后，将RDB文件中保存的repl-id与repl-offset加载到内存中
 master_repl_id = repl master_repl_offset = repl-offset
 通过info命令可以查看该信息
作用：
本机保存上次runid，重启后恢复该值，使所有slave认为还是之前的master

#### 频繁的全量复制（ 2）

 问题现象
 网络环境不佳，出现网络中断， slave不提供服务
 问题原因
 复制缓冲区过小，断网后slave的offset越界，触发全量复制
 最终结果
 slave反复进行全量复制
 解决方案
 修改复制缓冲区大小

repl-backlog-size

 建议设置如下：
\1. 测算从master到slave的重连平均时长second
\2. 获取master平均每秒产生写命令数据总量write_size_per_second
\3. 最优复制缓冲区空间 = 2 * second * write_size_per_second

#### 频繁的网络中断（ 1）

 问题现象
 master的CPU占用过高 或 slave频繁断开连接
 问题原因
 slave每1秒发送REPLCONF ACK命令到master
 当slave接到了慢查询时（ keys * ， hgetall等），会大量占用CPU性能
 master每1秒调用复制定时函数replicationCron()，比对slave发现长时间没有进行响应
 最终结果
 master各种资源（输出缓冲区、带宽、连接等） 被严重占用
 解决方案
 通过设置合理的超时时间，确认是否释放slave：repl-timeout

该参数定义了超时时间的阈值（默认60秒），超过该值，释放slave

频繁的网络中断（ 2）
 问题现象
 slave与master连接断开
 问题原因
 master发送ping指令频度较低
 master设定超时时间较短
 ping指令在网络中存在丢包
 解决方案
 提高ping指令发送的频度：repl-ping-slave-period
超时时间repl-time的时间至少是ping指令频度的5到10倍，否则slave很容易判定超时

#### 数据不一致

 问题现象
 多个slave获取相同数据不同步
 问题原因
 网络信息不同步，数据发送有延迟
 解决方案
 优化主从间的网络环境，通常放置在同一个机房部署，如使用阿里云等云服务器时要注意此现象
 监控主从节点延迟（通过offset）判断，如果slave延迟过大，暂时屏蔽程序对该slave的数据访问：slave-serve-stale-data yes|no
开启后仅响应info、 slaveof等少数命令（慎用，除非对数据一致性要求很高）

## 8.4 一主二从模式演示

1. 相关问题:

l 切入点问题？slave1、slave2是从头开始复制还是从切入点开始复制?比如从k4进来，那之前的123是否也可以复制

l 从机是否可以写？set可否？

l 主机shutdown后情况如何？从机是上位还是原地待命

l 主机又回来了后，主机新增记录，从机还能否顺利复制

l 其中一台从机down后情况如何？依照原有它能跟上大部队吗？

1. 复制原理

l 每次从机联通后，都会给主机发送sync指令

l 主机立刻进行存盘操作，发送RDB文件，给从机

l 从机收到RDB文件后，进行全盘加载

l 之后每次主机的写操作，都会立刻发送给从机，从机执行相同的命令

## 8.5 薪火相传模式演示

1. 上一个slave可以是下一个slave的Master，slave同样可以接收其他slaves的连接和同步请求，那么该slave作为了链条中下一个的master, 可以有效减轻master的写压力,去中心化降低风险.

 中途变更转向:会清除之前的数据，重新建立拷贝最新的

 风险是一旦某个slave宕机，后面的slave都没法备份

1. 反客为主(小弟上位)

当一个master宕机后，后面的slave可以立刻升为master，其后面的slave不用做任何修改。

用 slaveof no one 将从机变为主机。

1. 哨兵模式 sentinel (推举大哥)

反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库.

配置哨兵

l 调整为一主二从模式

l 自定义的/myredis目录下新建sentinel.conf文件

l 在配置文件中填写内容

sentinel monitor mymaster 127.0.0.1 6379 1

 其中mymaster为监控对象起的服务器名称， 1 为 至少有多少个哨兵同意迁移的

 数量。

l 启动哨兵

执行redis-sentinel /myredis/sentinel.conf

# 第9章 Redis集群

redis提供的服务OPS可以达到10万/秒，当前业务OPS已经达到10万/秒
 内存单机容量达到256G，当前业务需求内存容量1T
 使用集群的方式可以快速解决上述问题

集群就是使用网络将若干台计算机联通起来，并提供统一的管理方式，使其对外呈现单机的服务效果

集群作用
 分散单台服务器的访问压力，实现负载均衡
 分散单台服务器的存储压力，实现可扩展性
 降低单台服务器宕机带来的业务灾难

数据存储设计

这个key存到哪个redis存储空间：

 通过CRC16算法设计，计算出key应该保存的位置
 将所有的存储空间计划切割成16384份，每台主机保存一部分
每份代表的是一个存储空间，不是一个key的保存空间
 将key按照计算出的结果，然后(%16384)，比如得到37，放到对应的37存储空间

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxODU3MzcucG5n)

那如何加入一台新的计算机，怎么重新划分？每个原来的redis拿出一部分存到新的redis上。即改变槽的位置。

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxODU5NTQucG5n)

怎么知道槽新地址？

集群内部通讯设计：

- 各个数据库相互通信，保存各个库中槽的编号数据。槽可以不连续
- 一次命中，直接返回
- 一次未命中，告知具体位置。不是A去B里找，而是链接请求客户端去B里找。

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxOTAwNDIucG5n)

#### 搭建方式

 原生安装（单条命令）
 配置服务器（ 3主3从）
 建立通信（ Meet）
 分槽（ Slot）
 搭建主从（ master-slave）
 工具安装（批处理）

#### Cluster配置

 添加节点：cluster-enabled yes|no
 cluster配置文件名，该文件属于自动生成，仅用于快速查找文件并查询文件内容cluster-config-file
 节点服务响应超时时间，用于判定该节点是否下线或切换为从节点 cluster-node-timeout
 master连接的slave最小数量

cluster-migration-barrier

查看集群节点信息 cluster nodes
 进入一个从节点 redis，切换其主节点 cluster replicate
 发现一个新节点，新增主节点cluster meet ip:port
 忽略一个没有solt的节点cluster forget
 手动故障转移cluster failover

redis-trib命令
 添加节点redis-trib.rb add-node
 删除节点redis-trib.rb del-node
 重新分片redis-trib.rb reshard

## 9.1 问题

1. 容量不够，redis如何进行扩容？
2. 并发写操作， redis如何分摊？

## 9.1 什么是集群

1. Redis 集群实现了对Redis的水平扩容，即启动N个redis节点，将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N。
2. Redis 集群通过分区（partition）来提供一定程度的可用性（availability）： 即使集群中有一部分节点失效或者无法进行通讯， 集群也可以继续处理命令请求

## 9.2 集群方案

## 9.3 安装ruby环境

1. 能上网:

执行yum install ruby

执行yum install rubygems

1. 不能上网:

l cd /run/media/root/CentOS 7 x86_64/Packages 获取如下rpm包

l 拷贝到/opt/rpmruby/目录下，并cd到此目录

l 执行：rpm -Uvh *.rpm --nodeps –force 按照依赖安装各个rpm包

l 按照依赖安装各个rpm包

l 执行在opt目录下执行 gem install --local redis-3.2.0.gem

## 9.4 准备6个Redis实例

1. 准备6个实例 6379,6380,6381,6389,6390,6391

 拷贝多个redis.conf文件

 开启daemonize yes

 Pid文件名字

 指定端口

 Log文件名字

 Dump.rdb名字

 Appendonly 关掉或者换名字

1. 再加入如下配置

cluster-enabled yes 打开集群模式

cluster-config-file nodes-端口号.conf 设定节点配置文件名

cluster-node-timeout 15000 设定节点失联时间，超过该时间（毫秒），集群自动进行主从切换

## 9.5 合体

1. 将6个实例全部启动，nodes-端口号.conf文件都生成正常
2. 合体

l 进入到 cd /opt/redis-3.2.5/src

l 执行

./redis-trib.rb create --replicas 1

192.168.31.211:6379 192.168.31.211:6380 192.168.31.211:6381

192.168.31.211:6389 192.168.31.211:6390 192.168.31.211:6391

l 注意: IP地址修改为当前服务器的地址，端口号为每个Redis实例对应的端口号.

## 9.6 集群操作

1. 以集群的方式进入客户端

redis-cli -c -p 端口号

1. 通过cluster nodes 命令查看集群信息
2. redis cluster 如何分配这六个节点

一个集群至少要有三个主节点。

选项 --replicas 1 表示我们希望为集群中的每个主节点创建一个从节点。

分配原则尽量保证每个主数据库运行在不同的IP地址，每个从库和主库不在一个IP地址上。

1. 什么是slots

l 一个 Redis 集群包含 16384 个插槽（hash slot）， 数据库中的每个键都属于这 16384 个插槽的其中一个， 集群使用公式 CRC16(key) % 16384 来计算键 key 属于哪个槽， 其中 CRC16(key) 语句用于计算键 key 的 CRC16 校验和 。

l 集群中的每个节点负责处理一部分插槽。 举个例子， 如果一个集群可以有主节点， 其中：

 节点 A 负责处理 0 号至 5500 号插槽。

 节点 B 负责处理 5501 号至 11000 号插槽。

 节点 C 负责处理 11001 号至 16383 号插槽

1. 在集群中录入值

l 在redis-cli每次录入、查询键值，redis都会计算出该key应该送往的插槽，如果不是该客户端对应服务器的插槽，redis会报错，并告知应前往的redis实例地址和端口.

l redis-cli客户端提供了 –c 参数实现自动重定向。

如 redis-cli -c –p 6379 登入后，再录入、查询键值对可以自动重定向。

l 不在一个slot下的键值，是不能使用mget,mset等多键操作。

l 可以通过{}来定义组的概念，从而使key中{}内相同内容的键值对放到一个slot中去

1. 查询集群中的值

l CLUSTER KEYSLOT 计算键 key 应该被放置在哪个槽上。

l CLUSTER COUNTKEYSINSLOT 返回槽 slot 目前包含的键值对数量

l CLUSTER GETKEYSINSLOT 返回 count 个 slot 槽中的键

1. 故障恢复

l 如果主节点下线？从节点能否自动升为主节点？

l 主节点恢复后，主从关系会如何？

l 如果所有某一段插槽的主从节点都当掉，redis服务是否还能继续?

redis.conf中的参数 cluster-require-full-coverage

## 9.7 集群的Jedis开发

```JAVA
public class JedisClusterTest {
  public static void main(String[] args) {
 
     Set<HostAndPort> set =new HashSet<HostAndPort>();
     set.add(new HostAndPort("192.168.31.211",6379));
     JedisCluster jedisCluster=new JedisCluster(set);
     jedisCluster.set("k1", "v1");
     System.out.println(jedisCluster.get("k1"));
  }
}
12345678910
```

## 9.8 Redis集群的优缺点

l 优点

实现扩容

分摊压力

无中心配置相对简单

l 缺点

多键操作是不被支持的

多键的Redis事务是不被支持的。lua脚本不被支持。

由于集群方案出现较晚，很多公司已经采用了其他的集群方案，而代理或者客户端分片的方案想要迁移至redis cluster，需要整体迁移而不是逐步

# 哨兵模式

master主机宕机，从slave里选一个作为master

- 关闭master和所有slave
- 找一个slave作为master
- 修改其他slave的配置，连接新的主
- 启动新的master与slave
- 全量复制×N+部分复制×N

谁来确认master宕机了？
 找一个主？怎么找法？
 修改配置后，原始的主恢复了怎么办？

哨兵(sentinel) ：是一个分布式系统，用于对主从结构中的每台服务器进行监控，当master出现故障时通过投票机制从slave中选择新的master并将所有slave连接到新的master。

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjIyMTMucG5n)

哨兵的作用

 监控：
不断的检查master和slave是否正常运行。
master存活检测、 master与slave运行情况检测

 通知（提醒）
当被监控的服务器出现问题时， 向其他（哨兵间，客户端） 发送通知。

 自动故障转移
断开master与slave连接，选取一个slave作为master，将其他slave连接到新的master，并告知客户端新的服务器地址

注意：
哨兵也是一台redis服务器，只是不提供数据服务。通常哨兵配置数量为单数

### 配置哨兵

 配置一拖二的主从结构

 配置三个哨兵（配置相同，端口不同）
参看sentinel.conf

复制conf 到别的地方，做为新的哨兵

 启动哨兵
redis-sentinel sentinel-端口号.conf

先启动主机，再启动从机，再启动哨兵

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjIzMzMucG5n)

| 配置项                                                       | 范例                                             | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------ | ------------------------------------------------------------ |
| sentinel auth-pass <服务器名称>                              | sentinel auth-pass mymaster itcast               | 连接服务器口令                                               |
| sentinel down-after-milliseconds <自定义服 务名称><主机地址><端口><主从服务器总量> | sentinel monitor mymaster 192.168.194.131 6381 1 | 设置哨兵监听的主服务器信息，最后的参数决定了最终参与选举的服务器 数量（ -1） |
| sentinel down-after-milliseconds <服务名称><毫秒数（整数） > | sentinel down-after milliseconds mymaster 3000   | 指定哨兵在监控Redis服务时，判定服务器挂掉的时间周期，默认30秒 （ 30000），也是主从切换的启动条件之一 |
| sentinel parallel-syncs <服务名称><服务器数（整数） >        | sentinel parallel-syncs mymaster 1               | 指定同时进行主从的slave数量，数值越大，要求网络资源越高，要求约 小，同步时间约长 |
| sentinel failover-timeout <服务名称><毫秒数（整数） >        | sentinel failover-timeout mymaster 9000          | 指定出现故障后，故障切换的最大超时时间，超过该值，认定切换失败， 默认3分钟 |
| sentinel notification-script <服务名称><脚本路径>            |                                                  | 服务器无法正常联通时，设定的执行脚本，通常调试使用。         |

### 哨兵工作原理：

#### 主从切换

哨兵在进行主从切换过程中经历三个阶段
 监控
 通知
 故障转移

#### 阶段一：监控阶段

 用于同步各个节点的状态信息
 获取各个sentinel的状态（是否在线）
 获取master的状态
 master属性
 runid
 role： master
 各个slave的详细信息
 获取所有slave的状态（根据master中的slave信息）
 slave属性
 runid
 role： slave
 master_host、 master_port
 offset

…

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjI1MjcucG5n)

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjI2MTAucG5n)

### 阶段二：通知阶段

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjI2MzAucG5n)

### 阶段三：故障转移阶段

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjI3MTQucG5n)

![img](img/aHR0cHM6Ly9mZXJtaGFuLm9zcy1jbi1xaW5nZGFvLmFsaXl1bmNzLmNvbS9pbWcvMjAyMDA0MjIxNjI3MzcucG5n)

服务器列表中挑选备选master
 在线的
 响应慢的
 与原master断开时间久的
 优先原则
 优先级
 offset
 runid

发送指令（ sentinel ）
 向新的master发送slaveof no one
 向其他slave发送slaveof 新masterIP端口

主从切换总结
 服务器列表中挑选备选master
 在线的
 响应慢的
 与原master断开时间久的
 优先原则
 优先级
 offset
 runid

监控
 同步信息
 通知
 保持联通
 故障转移
 发现问题
 竞选负责人
 优选新master
 新master上任，其他slave切换master，原master作为slave故障回复后连接

# 企业级解决方案

### 缓存预热

宕机：服务器启动后迅速宕机

问题排查
\1. 请求数量较高
\2. 主从之间数据吞吐量较大，数据同步操作频度较高

解决方案
前置准备工作：

\1. 日常例行统计数据访问记录，启动之前统计访问频度较高的热点数据
\2. 利用LRU数据删除策略，构建数据留存队列
例如： storm与kafka配合

准备工作：
\1. 将统计结果中的数据分类，根据级别， redis优先加载级别较高的热点数据
\2. 利用分布式多服务器同时进行数据读取， 提速数据加载过程
\3. 热点数据主从同时预热

实施：
\1. 使用脚本程序固定触发数据预热过程
\2. 如果条件允许， 使用了CDN（内容分发网络），效果会更好

总结
缓存预热就是系统启动前， 提前将相关的缓存数据直接加载到缓存系统。避免在用户请求的时候，先查询数据库，然后再将数据缓
存的问题！用户直接查询事先被预热的缓存数据

### 缓存雪崩

数据库服务器崩溃（ 1）
\1. 系统平稳运行过程中，忽然数据库连接量激增
\2. 应用服务器无法及时处理请求
\3. 大量408， 500错误页面出现
\4. 客户反复刷新页面获取数据
\5. 数据库崩溃
\6. 应用服务器崩溃
\7. 重启应用服务器无效
\8. Redis服务器崩溃
\9. Redis集群崩溃
\10. 重启数据库后再次被瞬间流量放倒

问题排查
\1. 在一个较短的时间内，缓存中较多的key集中过期
\2. 此周期内请求访问过期的数据， redis未命中， redis向数据库获取数据
\3. 数据库同时接收到大量的请求无法及时处理
\4. Redis大量请求被积压，开始出现超时现象
\5. 数据库流量激增，数据库崩溃
\6. 重启后仍然面对缓存中无数据可用
\7. Redis服务器资源被严重占用， Redis服务器崩溃
\8. Redis集群呈现崩塌，集群瓦解
\9. 应用服务器无法及时得到数据响应请求，来自客户端的请求数量越来越多，应用服务器崩溃
\10. 应用服务器， redis，数据库全部重启，效果不理想

问题分析
 短时间范围内
 大量key集中过期

解决方案（道）
\1. 更多的页面静态化处理
\2. 构建多级缓存架构
Nginx缓存+redis缓存+ehcache缓存
\3. 检测Mysql严重耗时业务进行优化
对数据库的瓶颈排查：例如超时查询、耗时较高事务等
\4. 灾难预警机制
监控redis服务器性能指标
 CPU占用、 CPU使用率
 内存容量
 查询平均响应时间
 线程数
\5. 限流、降级
短时间范围内牺牲一些客户体验，限制一部分请求访问，降低应用服务器压力，待业务低速运转后再逐步放开访问

解决方案（术）
\1. LRU与LFU切换
\2. 数据有效期策略调整
 根据业务数据有效期进行分类错峰， A类90分钟， B类80分钟， C类70分钟
 过期时间使用固定时间+随机值的形式，稀释集中到期的key的数量
\3. 超热数据使用永久key
\4. 定期维护（自动+人工）
对即将过期数据做访问量分析，确认是否延时，配合访问量统计，做热点数据的延时
\5. 加锁
慎用！

总结
缓存雪崩就是瞬间过期数据量太大，导致对数据库服务器造成压力。如能够有效避免过期时间集中，可以有效解决雪崩现象的出现（约40%），配合其他策略一起使用，并监控服务器的运行数据，根据运行记录做快速调整。

### 缓存击穿

数据库服务器崩溃（ 2）
\1. 系统平稳运行过程中
\2. 数据库连接量瞬间激增
\3. Redis服务器无大量key过期
\4. Redis内存平稳，无波动
\5. Redis服务器CPU正常
\6. 数据库崩溃

问题排查
\1. Redis中某个key过期，该key访问量巨大
\2. 多个数据请求从服务器直接压到Redis后，均未命中
\3. Redis在短时间内发起了大量对数据库中同一数据的访问

问题分析
 单个key高热数据
 key过期

解决方案（术）
\1. 预先设定
以电商为例，每个商家根据店铺等级， 指定若干款主打商品，在购物节期间， 加大此类信息key的过期时长
注意：购物节不仅仅指当天，以及后续若干天，访问峰值呈现逐渐降低的趋势
\2. 现场调整
监控访问量，对自然流量激增的数据延长过期时间或设置为永久性key
\3. 后台刷新数据
启动定时任务，高峰期来临之前， 刷新数据有效期， 确保不丢失
\4. 二级缓存
设置不同的失效时间，保障不会被同时淘汰就行
\5. 加锁
分布式锁，防止被击穿，但是要注意也是性能瓶颈，慎重！

总结
缓存击穿就是单个高热数据过期的瞬间，数据访问量较大，未命中redis后，发起了大量对同一数据的数据库访问，导致对数据库服
务器造成压力。应对策略应该在业务数据分析与预防方面进行，配合运行监控测试与即时调整策略，毕竟单个key的过期监控难度
较高，配合雪崩处理策略即可

### 缓存穿透

数据库服务器崩溃（ 3）
\1. 系统平稳运行过程中
\2. 应用服务器流量随时间增量较大
\3. Redis服务器命中率随时间逐步降低
\4. Redis内存平稳，内存无压力
\5. Redis服务器CPU占用激增
\6. 数据库服务器压力激增
\7. 数据库崩溃

问题排查
\1. Redis中大面积出现未命中
\2. 出现非正常URL访问

问题分析
 获取的数据在数据库中也不存在，数据库查询未得到对应数据
 Redis获取到null数据未进行持久化，直接返回
 下次此类数据到达重复上述过程
 出现黑客攻击服务器

解决方案（术）
\1. 缓存null
对查询结果为null的数据进行缓存（长期使用，定期清理）， 设定短时限，例如30-60秒， 最高5分钟
\2. 白名单策略
 提前预热各种分类数据id对应的bitmaps， id作为bitmaps的offset，相当于设置了数据白名单。 当加载正常数据时，放
行，加载异常数据时直接拦截（效率偏低）
 使用布隆过滤器（有关布隆过滤器的命中问题对当前状况可以忽略）
\3. 实施监控
实时监控redis命中率（ 业务正常范围时，通常会有一个波动值）与null数据的占比
 非活动时段波动：通常检测3-5倍，超过5倍纳入重点排查对象
 活动时段波动：通常检测10-50倍， 超过50倍纳入重点排查对象
根据倍数不同，启动不同的排查流程。然后使用黑名单进行防控（运营）
\4. key加密
问题出现后，临时启动防灾业务key，对key进行业务层传输加密服务，设定校验程序，过来的key校验
例如每天随机分配60个加密串，挑选2到3个，混淆到页面数据id中，发现访问key不满足规则，驳回数据访问

总结
缓存击穿访问了不存在的数据，跳过了合法数据的redis数据缓存阶段，每次访问数据库，导致对数据库服务器造成压力。通常此类
数据的出现量是一个较低的值，当出现此类情况以毒攻毒，并及时报警。应对策略应该在临时预案防范方面多做文章。
无论是黑名单还是白名单，都是对整体系统的压力，警报解除后尽快移除。

### 性能指标监控

监控指标
 性能指标： Performance
 内存指标： Memory
 基本活动指标： Basic activity
 持久性指标： Persistence
 错误指标： Error

监控指标
 性能指标： Performance

| Name                      | 描述                     |
| ------------------------- | ------------------------ |
| latency                   | Redis响应一个请求的时间  |
| instantaneous_ops_per_sec | 平均每秒处理请求次数     |
| hit rate                  | 缓存命中率（计算出来的） |

监控指标
 内存指标： Memory

| name                    | 描述                                       |
| ----------------------- | ------------------------------------------ |
| used_memory             | 已使用内存                                 |
| mem_fragmentation_ratio | 内存碎片率                                 |
| evited_keys             | 由于最大内存限制被移除的key值              |
| blocked_clients         | 由于BLPOP,BRPOP,BRPOPLPUSH而被阻塞的客户端 |

监控指标
 基本活动指标： Basic activity

| Name                       | 描述                       |
| -------------------------- | -------------------------- |
| connected_clients          | 客户端连接数               |
| connected_slaves           | slave数量                  |
| master_last_io_seconds_ago | 最近一次主从交互之后的秒数 |
| keyspace                   | 数据库中的key值总数        |

监控指标
 持久性指标： Persistence

| name                        | 描述                                 |
| --------------------------- | ------------------------------------ |
| rdb_last_save_time          | 最后一次持久化保存到磁盘的时间戳     |
| rdb_changes_since_last_save | 自最后一次持久化以来数据库的更改次数 |

监控指标
 错误指标： Error

| name                           |                                       |
| ------------------------------ | ------------------------------------- |
| rejected_connections           | 由于达到maxclient限制而被拒绝的连接数 |
| keyspace_misses                | key值查找失败（没有命中）次数         |
| master_link_down_since_seconds | 主从断开的持续时间（秒）              |

监控方式
 工具
 Cloud Insight Redis
 Prometheus
 Redis-stat
 Redis-faina
 RedisLive
 zabbix
 命令
 benchmark
 redis cli
 monitor
 showlog

benchmark

 命令

redis-benchmark [-h ] [-p ] [-c ] [-n <requests]> [-k ]

 范例1

redis-benchmark

说明： 50个连接， 10000次请求对应的性能
 范例2

redis-benchmark -c 100 -n 5000

说明： 100个连接， 5000次请求对应的性能

monitor

 命令

monitor

打印服务器调试信息

showlong

 命令

showlong [operator]

 get ：获取慢查询日志
 len ：获取慢查询日志条目数
 reset ：重置慢查询日志
 相关配置

```SH
slowlog-log-slower-than 1000 #设置慢查询的时间下线，单位：微妙
slowlog-max-len 100 #设置慢查询命令对应的日志显示长度，单位：命令数  
```