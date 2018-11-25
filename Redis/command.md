## Redis数据类型及常用命令

数据类型
1. String 字符串
2. Hash 散列
3. List 列表
4. Set 集合
5. Sorted Set 有序集合


### Key 键
语法
``` shell
    > COMMAND KEY_NAME
```

命令                    |    描述
--------------          | -------
DEL key                 | 删除
DUMP key                | 序列化给定的key
EXISTS key              | 判断键是否存在
EXPIRE key seconds      | 设置给定键的过期时间
EXPIREAT key timestamps | 设置过期时间为给定的时间
PEXPIRE key milliseconds | 过期时间为毫秒
KEYS pattern             | 查找符合pattern模式的key
MOVE key db              | 将当前数据库的key移到指定的数据库db中
PEXSIST key              | 移除key的过期时间
PTTL key                 | 返回过期时间毫秒
TTL key                  | 返回过期时间秒
RANDOMKEY                | 返回随机一个key
RENAME key newkey        | 重命名键名
RENAMENX key newkey      | 当newkey不存在时重命名
TYPE key                 | 返回key所存储值的类型

### String 字符串
``` shell
    > SET k 'test'
    OK

    > GET k
    "test"
```

命令                    |    描述
--------------          | -------
SET key value           | 设置指定key的值
GET key                 | 获取指定key的值
GETRANGE key start end  | 返回key的字符串中的子字符串
GETSET key value        | 设置key的值为value,并返回key的旧值
GETBIT key offset       | key 所储存的字符串值，获取指定偏移量上的位(bit)
MGET key1[key2...]      | 获取一个或多个指定key的值
SETBIT key offset value | 设置指定key偏移量上的位(bit)
SETEX key seconds value | 设置指定key的value以及过期时间
SETNX key value         | 当指定key不存在时设置它的值为value
SETRANGE key offset value | 用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始
STRLEN key              | 获取指定key的长度
MSET key value [key value...] | 同时设置多个key-value对
MSETNX key value [key value...] | 同时设置多个key-value对，仅当key值不存在时
PSETEX key milliseconds value | 设置key的过期时间，毫秒
INCR key                | 将key中所存储的数字加1
INCRBY key increment    | 将key中所存储的数字加上给定的增量
INCRBYFLOAT key incremetn | 将key中所存储的数字加上给定的浮点数增量
DECR key                | 将key中所存储的数字减1
DECRBY key increment    | 将key中所存储的数字减去给定的增量
APPEND key value        | 将value追加到key所存储的值

### Hash 哈希
``` shell
    > HMSET k f1 "hello" f2 "world"
    OK

    > HGETALL k
    1) "f1"
    2) "hello"
    3) "f2"
    4) "world"
```

命令                    |    描述
--------------          | -------
HDEL key field1 [field2] | 删除一个或多个hash字段
HEXISTS key field        | 判断给定key中是否存在指定字段
HGET key field           | 获取给定哈希表key中的指定字段的值
HGETALL key              | 获取给定哈希表key中所有的字段和值
HINCRBY key field increment | 为哈希表 key 中的指定字段的整数值加上增量 increment
HINCRBYFLOAT key field increment | 为哈希表 key 中的指定字段的整数值加上浮点数增量 increment
HKEYS key                | 获取哈希表key中所有的字段
HLEN key                 | 获取哈希表key中字段的数量
HMGET key field1 field2  | 获取哈希表key中多个字段的值
HMSET key field1 value1 [field2 value2] | 设置哈希表key中多个field和value
HSET key field value     | 设置哈希表key中field的值为value
HSEXNX key field value   | 只有在字段 field 不存在时，设置哈希表字段的值。
HVALS key                | 获取哈希表key中所有的值
HSCAN key cursor [MATCH pattern] [COUNT count] | 迭代哈希表中的键值对


### 列表
``` shell
    > LPUSH k php
    (integer) 1

    > LPUSH k java
    (integer) 2

    > LPUSH k golang
    (integer) 3

    > LRANGE k 0 5
    1) "golang"
    2) "java"
    3) "php"
```

命令                    |    描述
--------------          | -------
BLPOP key1 [key2] timeout | 移除并获取列表的第一个元素，如果列表没有元素会阻塞列表直到超时或发现可弹出元素为止
BRPOP key1 [key2] timeout | 移除并获取列表的最后一个元素，如果列表没有元素会阻塞列表直到超时或发现可弹出元素为止
BRPOPLPUSH source destination timeout | 从列表中弹出一个值，将弹出的元素插入另外一个列表中并返回它的，如果列表没有元素会阻塞列表直到超时或发现可弹出元素为止
LINDEX key index          | 通过索引获取列表中的元素
LINSERT key BEFORE\|AFTER pivot value | 在列表的元素前或后插入元素
LLEN key                  | 获取列表长度
LPOP key                  | 移除并获取列表第一个元素
LPUSH key value1 [value2] | 将一个值或多个值插入列表头部
LPUSHX key value          | 将一个值插入到已存在的列表头部
LREM key count value      | 移除列表元素
LSET key index value      | 通过索引设置元素的值
LRANGE key start stop     | 获取列表内指定范围内的元素
LTRIM key start stop      | 只保留指定范围内的元素
RPOP key                  | 移除并获取列表最后一个元素
RPOPLPUSH source destination | 移除列表最后一个元素，并将该元素添加到另一个列表并返回
RPUSH key value1 [value2] | 在列表中添加一个或多个值
RPUSHX key value          | 为已存在的列表添加值


### Set 集合
``` shell
    > SADD k redis
    (integer) 1
    > SADD k mongodb
    (integer) 1
    > SADD k mariadb
    (integer) 1
    > SADD k redis
    (integer) 0
    > SMEMBERS k
    1) "redis"
    2) "mariadb"
    3) "mongodb"

```

命令                    |    描述
--------------          | -------
SADD key member1 [member2] | 向集合添加一个或多个成员
SCARD key                  | 获取集合的成员数
SDIFF key1 [key2]          | 获取给定所有集合的差集
SDIFFSTORE destination key1 [key2] | 获取给定所有集合的差集并存储在destination中
SINTER key1 [key2]         | 获取给定所有集合的交集
SINTERSTORE destination key1 [key2] | 获取给定所有集合的交集并存储在destination中
SISMEMBER key member       | 判断member元素是否时集合key的成员
SMEMBERS key               | 返回集合中所有的成员
SMOVE source destination member | 将member元素从source集合移动到destination集合
SPOP key                   | 移除并返回集合中随机元素
SRANDMEMBER key count      | 返回集合中一个或多个随机数
SREM key member1 [member2] | 移除集合中一个或多个成员
SUNION key1 [key2]         | 返回所有给定集合的并集
SUNIONSTORE destination key1 [key2] | 所有给定集合的并集存储到destination集合中
SSCAN key cursor [MATCH patter] [Count count] | 迭代集合中的元素


### Sorted Set 有序集合
``` shell
    > ZADD k 1 php
    (integer) 1
    > ZADD k 2 java
    (integer) 1
    > ZADD k 4 java
    (integer) 0
    > ZADD k 3 golang
    (integer) 1
    > ZRANGE k 0 -1 WITHSCORES
    1) "php"
    2) "1"
    3) "golang"
    4) "3"
    5) "java"
    6) "4"
```


命令                    |    描述
--------------          | -------
ZADD key score1 member1 [score2 member2] | 向有序集合添加一个或多个成员，或be更新已存在成员的分数
ZCARD key               | 获取有序集合的成员数
ZCOUNT key min max      | 获取在有序集合中指定区间分数的成员数
ZINCRBY key increment member | 有序集合中对指定成员的分数加上增量increment
ZINTERSTORE destination numkeys key [key...] | 计算给定的一个或多个有序集合的交集并将结果储存在新的有序集合中
ZLEXCOUNT key min max   | 在有序集合中指定指定字典区间内成员数量
ZRANGE key start stop [WITHSCORES] | 通过索引区间返回有序集合指定区间内的成员
ZRANGEBYLEX key min max [Limit offset count] | 通过字典区间返回有序集合的成员
ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT] | 通过分数返回有序集合指定区间的成员
ZRANK key member         | 返回有序集合中指定成员的索引
ZREM key member [member...] | 移除有序集合中一个或多个成员
ZREMRANGEBYLEX key min max  | 移除有序集合中给定的字典区间的所有成员
ZREMRANGEBYRANK key start stop | 移除有序集合中给定的排名区间的所有成员
ZREMRANGGEBYSCORE key min max | 移除有序集合中给定的分数区间的所有成员
ZREVRANGE key start stop [WITHSCORES] | 返回有序集合中指定区间内的成员，通过索引，分数从高到低
ZREVRANGEBYSCORE key max min [WITHSCORES] | 返回有序集合中指定分数区间内的成员，分数从高到低排序
ZREVRANK key member | 返回有序集合中指定成员的排名，有序集合成员按照分数值递减
ZSCORE key member   | 返回有序集合中，成员的分数值
ZUNIONSTORE destination numkeys key [key...] | 计算给定的一个或多个有序集合的并集，并存储在新的key中
ZSCAN key cursor [MATCH pattern] [COUNT count] | 迭代有序集合中的元素(包括元素成员好元素分值)



### Redis事务
Redis 事务可以一次执行多个命令， 并且带有以下两个重要的保证：

* 批量操作在发送 EXEC 命令前被放入队列缓存。
* 收到 EXEC 命令后进入事务执行，事务中任意命令执行失败，其余的命令依然被执行。
* 在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中。

一个事务从开始到执行会经历以下三个阶段：

* 开始事务。
* 命令入队。
* 执行事务。

``` shell
    > multi
    > set a aaa
    QUEUED
    > set b bbb
    QUEUED
    > set c ccc
    QUEUED
    > exec
    1) OK
    2) OK
    3) OK
```

命令                    |    描述
--------------          | -------
DISCARD                 | 取消事务，放弃执行事务块内的所有命令
EXEC                    | 执行所有事务块内的命令
MULTI                   | 标记一个事务块的开始
UNWATCH                 | 取消WATCH命令对所有key的监视
WATCH key [key...]      | 监视一个或多个key，如果在事务执行之前这个key被其他命令所改变，则事务将打断


### 读写锁实现
```php

<?php

class ReadWriteLock
{
    const rLock = 'lock:read:';
    const wLock = 'lock:write:';

    public function readLock($key, $token)
    {
        // 判断当前是否有写操作
        $script = <<<LUA
if redis.call('GET', KEYS[1]) then
    return 0
else 
    redis.call('HSET', KEYS[2], ARGV[1], 10)
    redis.call('SET', ARGV[1], 10, 'EX', 10)
    redis.call('EXPIRE', KEYS[2], 10)
    return 1
end
LUA;

        return redis()->eval($script, 2, self::wLock . $key, self::rLock . $key, $token);
    }

    public function writeLock($key, $token)
    {
        // 判断当前是否有写或读操作
        $script = <<<LUA
if redis.call('GET', KEYS[1]) or redis.call('EXISTS', KEYS[2]) == 1 then
    return 0
else 
    return redis.call('SET', KEYS[1], ARGV[1], 'EX', 10, 'NX')
end
LUA;

        return redis()->eval($script, 2, self::wLock . $key, self::rLock . $key, $token);
    }


    public function unlockRead($key, $token)
    {
        $script = <<<LUA
if redis.call('HEXISTS', KEYS[1], KEYS[2]) == false then
    return
end
redis.call('HDEL', KEYS[1], KEYS[2])

local e1 = redis.call('TTL', KEYS[1])
local e2 = redis.call('TTL', KEYS[2])
redis.call('DEL', KEYS[2])

if e1 > e2 then
    return 
end

local keys = redis.call("HKEYS", KEYS[1])
local max = -2
for k, v in pairs(keys) do
    local t = redis.call('TTL', KEYS[2])
    max = math.max(max, t)
end

if max > 0 then
    redis.call('EXPIRE', KEYS[1], max)
end
LUA;

        return redis()->eval($script, 2, self::rLock . $key, $token);
    }

    public function unlockWrite($key, $token)
    {
        $script = <<<LUA
if ARGV[1] == redis.call('GET', KEYS[1])
then
    return redis.call('DEL', KEYS[1])
else
    return 0
end
LUA;

        return redis()->eval($script, 1, self::wLock . $key, $token);
    }
}

$rw = new ReadWriteLock();

$key = 'hello';
$token = uniqid();


if ($rw->readLock($key, $token)) {
    redis()->get($key);
    $rw->unlockRead($key, $token);
}

if ($rw->writeLock($key, $token)) {
    redis()->set($key, 'abc');
    $rw->unlockWrite($key, $token);
}


```
