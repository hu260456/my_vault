#### nosql

- NoSQL = not only SQL
- NoSQL 泛指非关系型数据库
- Nosql 的分类

![](attachments/Pasted%20image%2020240723132832.png)

#### 数据类型

- string
- hash
- list
- set
- zset(sorted set)
- HyperLogLog
- Bitmap(位图)
- Bloom Filter(布隆过滤器)
- Geospatial(地理位置)
- Module(模块)
- Streams(流信息)

#### string

```redis
使用 set key 设置数据
如果同名数据存在则覆盖
set name hu
set age 18

批量设置数据
mset k1 v1 k2 v2

使用 setnx key 设置数据
如果同名数据存在则无操作
setnx name wang

设置数据，有效期 5 秒
setex code 5 man

查看 code 还有多久失效
-1 表示永久有效
ttl code

使用 get key 获取数据
get name
get age

批量获取数据
mget k1 k2

对 key + 1
incr key
对 key - 1
decr key
对 key + 10
incrby key 10

拼接
append name _sir

删除数据
del key
```

#### hash

- 结构
	- K
	- V
		- hk
		- hv

```redis
设置一条记录
hset user name hu

获取
hget user name

判断 user 存不存在字段 age
hexists user age

删除
hdel user name

+1 操作
hincrby user age 1

获取 user 字段数
hlen user

获取 user 的所有 key
hkeys user

获取 user 的所有 value
hvals user

获取 user 的所有
hgetall user
```

#### list

- 结构
	- K
	- V（是一个双向队列）
		- item
		- item1
		- ...

```redis
left push
lpush K V

right push
rpush K V

left pop
lpop K

right pop
rpop K

获取列表长度
llen K

从 left 开始列出 K 中从 start 到 stop 的 items
支持负索引表示倒数
lrange K start stop

操作 K 队列，在 pivot 之前/后插入 value
linsert K before/after pivot value

操作 K 队列，更新索引 index 处值为 value
lset K index value

操作 K 队列，删除 count 个 value 值
lrem K count value

操作 K 队列，截取从 start 到 stop
ltrim K start stop

操作 K 队列，获取索引 index 位置的数据
lindex K index
```

#### set

> 无序集合；可以进行交集、并集、差集运算

```redis
往 K 集合中添加 a b c
sadd K a b c

遍历 K 集合中的元素
smembers K

删除 K 集合中的 a
srem K a

从 K 集合中随机弹出 count 个
spop K count

交集
sinter set1 set2

并集
sunion set1 set2

差集
sdiff set1 set2
```

#### sorted set(zset)

> 有序集合；要求插入数据时附加一个 double 类型的 score 作为排序依据

```redis
往 K 集合中添加 a b c
zadd 1000 a 2000 b 3000 c

a 加 1000 分
zincrby K 1000 a

K 中的元素按分数升序排列
zrange K 0 -1

K 中的元素按分数升序排列，并显示分数
zrange K 0 -1 withscores

K 中的元素按分数降序排列
zrevrange K 0 -1

K 中的元素按分数降序排列，并显示分数
zrevrange K 0 -1 withscores

查看 a 的正序排名
zrank K a

查看 a 的倒序排名
zrevrank K a

查看 K 的元素个数
zcard K
```

#### 全局命令

| 命令                 | 功能                          | 案例                |
| ------------------ | --------------------------- | ----------------- |
| keys pattern       | 根据 pattern 列出 redis 所有的 key | key xxx:*         |
| exists key         | 判断一个 key 是否存在               | -                 |
| expire key seconds | 给一个 key 设定过期时间              | expire v 10       |
| persist key        | 取消一个 key 的过期时间              | -                 |
| randomkey          | 随机返回一个 key                  | -                 |
| rename key newkey  | 将 key 改名为 newkey            | rename sex gender |
| select db          | 切换数据库 0-15                  | select 1          |
| move key db        | 将一个 key 移动其其他数据库            | move k 1          |
| dbsize             | 查看当前数据库的 key 个数             | -                 |
| info               | 打印 redis 相关信息               | -                 |
| flushdb            | 清空当前数据库                     | -                 |
| flushall           | 清空所有数据库                     | -                 |

#### 安全

```shell
# 编辑 redis.conf，修改 requirepass

# 重启 redis 服务

# 使用密码登录 redis
redis-cli -a <密码>
```

#### 事务

```redis
使用 multi 开启一个事务
MULTI

后续的多个命令会添加到一个队列中，不会执行
...

最后由 EXEC 命令触发事务，一并执行事务中的所有命令
中间某条指令的失败不会导致前面已做指令的回滚，也不会造成后续的指令不做
EXEC
```

#### 持久化机制

redis 持久化机制有 3 中

- 快照 (RDB, redis database)
- 文件追加 (AOF, append only file)
- 混合 (after redis4)

##### RDB

> 在 redis4 前是默认采用的方式

	将内存数据中以快照的方式写入到二进制文件中，默认为 dump.rdb
	redis 加载 RDB 恢复数据远远快于 AOF
	适合全量备份的场景
	不适合实时备份

- 手动触发

	使用 save 命令，会阻塞当前 Redis 服务器，直到 RDB 过程完成
	使用 bgsave 命令，Redis 进程会 fork 一个子进程，由子进程实现 RDB 持久化

- 自动触发

	使用命令 save m n，表示 m 秒内数据集存在 n 次修改则自动触发 bgsave 命令

##### AOF

> 使用命令 appendonly yes 来开启

	备份的实时性高，配置得当最多损失秒级数据
	AOF 是一个增量日志文件，体积较大，断电不易失，恢复速度慢于 RDB
	当 AOF 变得太大时，Redis 会在后台自动重写 AOF（rewrite）

AOF 有 3 种文件同步策略

- appendfsync always
	- 收到命令就立即写到磁盘
- appendfsync everysec
	- 每秒写入磁盘一次
- appendfsync no
	- 完全依赖 os，一般同步周期是 30 秒

##### 混合方式

> 自 Redis4 后默认采用 RDB-AOF 混合方式

#### 内存淘汰机制

```redis
配置内存上限，0 代表无限制
maxmemory 0

配置内存淘汰机制
maxmemory-policy
```

内存淘汰算法

- LRU 算法
- LFU 算法
- TTL 算法
	- 挑选 ttl 短的淘汰
- 随即淘汰算法

#### 过期 key 处理

3 种方案

- 惰性删除
	- 当访问 Key 时才去判断它是否过期，如果过期才删除
	- 容易造成内存浪费
- 定时删除
	- 设置一个定时器，用来执行对 Key 的删除操作
	- 增加 cpu 的负担
- 定期删除
	- 隔一段时间，对数据进行一次检查，删除里面的过期Key，至于要删除多少过期Key，检查多少数据，则由算法决定