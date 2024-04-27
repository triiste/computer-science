# Redis的五种常用数据类型

Redis存储的是key-value结构的数据，其中key是字符串类型，value有5种常用的数据类型

![image-20240125145913130](C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20240125145913130.png)

- 字符串 string 

普通字符串，Redis中最简单的数据类型

- 哈希 hash(不可重复)

也叫散列，类似于java中的HashMap结构

- 列表 list 

按照插入顺序排序，可以有重复元素，类似于java中的LinkedList (可以做点赞)

- 集合 set

无序集合，没有重复元素，类似于java中的HashSet

- 有序集合 sorted set / zset

集合中每个元素关联一个分数(score)，根据分数升序排序，没有重复元素(可作投票排行榜)   （每个博客 可以采用 blog_id,readCount）

# Redis 常用命令

## 字符串常用命令

- SET key value 设置指定key的值
- GET key 获取指定key的值
- SETEX key seconds value 设置指定key的值，并将key的过期时间设为seconds 秒 (应用场景 短期验证码)
- SETNX key value 只有在key不存在时设置key的值 （分布式锁）

## 哈希操作命令

Redis hash 是一个string类型的field 和 value 的映射表，**hash特别适合用于存储对象**，常用命令：

- HSET key field value 将哈希表key的字段field的值设为value
- HGET key field 获取存储在哈希表中指定字段的值
- HDEL key field 删除存储在哈希表中的指定字段
- HKEYS key 获取哈希表中所有字段
- HVALS key 获取哈希表中所有值

![image-20240125152743807](C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20240125152743807.png)

## 列表操作命令

Redis列表时简单的字符串列表，按照插入顺序排序，常用命令

- LPUSH key value1 [value2]  将一个或多个值插入到列表头部
- LRANGE key start stop 获取列表指定范围内的元素
- RPOP key 移除并获取列表最后一个元素
- LLEN key 获取列表长度

![image-20240125154813108](C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20240125154813108.png)

## 集合操作命令

适用场景：共同关注

Redis set 是string类型的无序集合。集合中不能出现重复的数据，常用命令：

- SADD key member1 [member2] 向集合添加一个或多个成员
- SMEMBERS key 返回集合中的所有成员
- SCARD key 获取集合的成员数
- SINTER　key1　［key2］　返回所有给定集合的交集
- SUNION　key1　［key2］　返回所有给定集合的并集
- SREM　key  ｍember１［member］　删除集合中一个或多个成员

![image-20240125160611157](C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20240125160611157.png)

## 有序集合操作命令

Redis有序集合是string类型元素的集合，且不允许有重复成员。每个元素都会关联一个double类型的分数。常用命令为：

- ZADD key score1 member [score2 member2] 向有序集合添加一个或多个成员 
- ZRANGE key start stop[WITHSCORES] 通过索引区间返回有序集合中指定区间内的成员
- ZINCRBY key increment member 有序集合中对指定成员的分数加上增量increment
- ZREM key member [member 1] 移除有序集合中的一个或多个成员

## 通用命令

Redis的通用命令是不分数据类型的，都可以使用的命令

- keys [pattern]
- exist key
- type key
- del key

## springboot 中使用Redis

RedisConfig.java配置

~~~java
    @Bean
    public ZSetOperations<String, Object> zSetOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForZSet();
    }
    // 为 Set 数据结构类型注入操作集合
    @Bean
    public SetOperations<String, Object> setOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForSet();
    }
~~~

Autowired注入

~~~java
    @Autowired
    private ZSetOperations zSetOperations;
    @Autowired
    private SetOperations setOperations;
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
~~~

ZSetOperations使用

~~~java
zSetOperations.remove(BLOG_KEY,id);//移除
Double score = zSetOperations.score(BLOG_KEY, blog.getId());//获取分数
Long size = zSetOperations.size(BLOG_KEY); //获取长度
zset = zSetOperations.reverseRange(BLOG_KEY, 0, TopN - 1);//获取根据分数排行
zSetOperations.incrementScore(BLOG_KEY, blogId, 1);//分数加1
~~~

SetOperations使用

~~~java
Set<String> set = setOperations.members(KEY);//获取数量
setOperations.isMember(KEY,value) //判断是否存在
setOperations.remove(type,x);//移除某个值
setOperations.add(COLLECT_KEY,value); //增加某个值
~~~

stringRedisTemplate 使用

~~~java
stringRedisTemplate.opsForValue().set(id,"在线");//添加
RedisTemplate.delete(userMap.get(session.getId()));//删除
String value = stringRedisTemplate.opsForValue().get(key);//获取 判断是否能获取到
~~~



