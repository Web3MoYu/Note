# Redis

## 配置文件

### 设置密码

```shell
requirepass yourpassword # 在redis.conf中加入这个来确保连接需要密码
# 然后在执行 redis-cli -h locathost -p 6379后，进入redis的终端后，执行auth yourpasswd
# 或者redis-cli -h locathost -p 6379 -a yourpasswd
```

### AOF

```shell
appendonly no # 默认没有开启aof
```

### RDB

默认开启的持久化

```shell
save <seconds> <changes> 	# 表示在多少秒内进行多少次操作就保存一次
save 900 1  				# 900s有一个key被修改时保存
save 300 10 				# 300s内有10个key被修改时保存
save 60 10000 				# 60s内有10000个key被修改时保存
```

### 后台启动

```shell
daemonize no # 默认不是后台运行 改成yes改为后台运行
```

### 远程连接

```shell
bind 127.0.0.1 # 默认是打开的，将其注释掉即可
```

## 数据类型

![](img/1.png)

### String

- 常用命令

```shell
set key value				# 设置指定key的值
get key						# 获取指定key的值
setex key seconds value		# 设置指定key的值，并将key的过期时间折欸seconds秒
setnx key value				# 只有在key不存在时设置key的值
```
**set**

- 设置一个元素

```powershell
127.0.0.1:6379> set a 1
OK
```
**get**

- 从key中取值

```powershell
127.0.0.1:6379> get a
"1"
```
**setnx**

- 添加不存在的元素，如果以及存在则添加不成功

```powershell
127.0.0.1:6379> setnx a 2
(integer) 0
127.0.0.1:6379> setnx b 2
(integer) 1
127.0.0.1:6379> get b
"2"
```

如果key相同那么新的value将会覆盖掉旧的

### Hash

Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象，

- 常用命令

```shell
hset key field value	# 将哈希表key中的字段field的值设为value
hget key field			# 获取存储在哈希表中指定字段的值
hdel key field			# 删除存储在哈希表中的指定字段
hkeys key				# 获取哈希表中所有字段
hvals key				# 获取哈希表中所有值
hgetall key				# 获取在哈希表中指定key的所有字段和值
```

**hset**

- 创建hash

```powershell
127.0.0.1:6379> hset 1 name xiaoming age 20 sex nan
(integer) 3
```
hget

- 从hash得到属性

```powershell
127.0.0.1:6379> hget 1 name
"xiaoming"
127.0.0.1:6379> hget 1 age
"20"
127.0.0.1:6379> hget 1 sex
"nan"
```
**hdel**

- 删除属性

```powershell
127.0.0.1:6379> hdel 1 sex
(integer) 1
127.0.0.1:6379> hget 1 sex
(nil)
```
**hkeys,hvals,hgetall**

- 得到hash中的元素

```powershell
127.0.0.1:6379> hkeys 1
1) "name"
2) "age"
127.0.0.1:6379> hvals 1
1) "xiaoming"
2) "20"
127.0.0.1:6379> hgetall 1
1) "name"
2) "xiaoming"
3) "age"
4) "20"
```



### List

Redis列表是简单的字符串列表，按照插入顺序排序，

- 常用命令

```shell
lpush key value1 [value2] 		# 将一个或多个值插入到列表头部
lpop key						# 移除并获取列表的第一个元素
rpop key						# 移除列表的最后一个元素，返回值为移除的元素
rpush key value1 [value2]		# 在列表中添加一个或多个值到列表尾部
lrange key start stop 			# 获取列表指定范围内的元素
llen key 						# 获取列表长度
brpop key1 [key2] timeout 		# 移出并获取列表的最后一个元素，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
```

**lpush**

- 将一个或多个值插入到列表头部

```powershell
127.0.0.1:6379> lpush a 1 2 3 4 5 6 7
(integer) 7
127.0.0.1:6379> lrange a 0 -1
1) "7"
2) "6"
3) "5"
4) "4"
5) "3"
6) "2"
7) "1"
```
**lpop**

- 移除并获取列表的第一个元素

```powershell
127.0.0.1:6379> lpop a
"7"
127.0.0.1:6379> lrange a 0 -1
1) "6"
2) "5"
3) "4"
4) "3"
5) "2"
6) "1"
```
**rpop**

- 移除列表的最后一个元素，返回值为移除的元素

```powershell
127.0.0.1:6379> rpop a
"1"
127.0.0.1:6379> lrange a 0 -1
1) "6"
2) "5"
3) "4"
4) "3"
5) "2"
```
**rpush**

- 在列表中添加一个或多个值到列表尾部

```powershell
127.0.0.1:6379> rpush a 1
(integer) 6
127.0.0.1:6379> lrange a 0 -1
1) "6"
2) "5"
3) "4"
4) "3"
5) "2"
6) "1"
```
**llen**

- 查看列表长度

```powershell
127.0.0.1:6379> llen a
(integer) 7
```

### Set

Redis set是string类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据，

- 常用命令

```shell
sadd key memberi1[member2]			# 向集合添加一个或多个成员
smembers key 					 	# 返回集合中的所有成员
scard key							# 获取集合的成员数
sinter key1 [key2]					# 返回给定所有集合的交集
sunion key1 [key2]					# 返回所有给定集合的并集
sdiff key1 [key2]					# 返回给定所有集合的差集
srem key member1 [member2]			# 移除集合中一个或多个成员
```

**sadd**

- 添加集合

```powershell
127.0.0.1:6379> sadd a 1 2 3 4 5
(integer) 5
127.0.0.1:6379> sadd b 3 4 5 6 7
(integer) 5
```
**smembers**

- 查看集合成员

```powershell
127.0.0.1:6379> smembers a
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
127.0.0.1:6379> smembers b
1) "3"
2) "4"
3) "5"
4) "6"
5) "7"
```
**scard**

- 查看集合长度

```powershell
127.0.0.1:6379> scard a
(integer) 5
```
**sinter**

- 交集

```powershell
127.0.0.1:6379> sinter a b
1) "3"
2) "4"
3) "5"
```
**sunion**

- 并集

```powershell
127.0.0.1:6379> sunion a b
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
6) "6"
7) "7"
```
**sidff**

- 差集

```powershell
127.0.0.1:6379> sdiff a b
1) "1"
2) "2"
127.0.0.1:6379> sdiff b a
1) "6"
2) "7"
```
**srem**

- 移除集合中的元素

```powershell
127.0.0.1:6379> srem a 1
(integer) 1
127.0.0.1:6379> SMEMBERS a
1) "2"
2) "3"
3) "4"
4) "5"
```

### Sorted set

Redis sorted set有序集合是string类型元素的集合，且不允许重复的成员。每个元素都会关联一个double:类型的分数
(score)。redis正是通过分数来为集合中的成员进行从小到大排序。有序集合的成员是唯一的，但分数却可以重复。
**常用命令**

```powershell
zadd key score1 member1 [score2 member2] 	# 向有序集合添加一个或多个成员，或者更新已存在成员的分数
zrange key start stop [WITHSCORES]			# 通过索引区间返回有序集合中指定区间内的成员
zincrby key increment member 				# 有序集合中对指定成员的分数加上增量increment
zrem key member [member ...] 				# 移除有序集合中的一个或多个成员
```

**zadd**

- 添加有序集合

```powershell
127.0.0.1:6379> zadd set 10.0 a 9.0 b
(integer) 2
127.0.0.1:6379> zrange set 0 -1
1) "b"
2) "a"
127.0.0.1:6379> zadd set 9.9 c
(integer) 1
127.0.0.1:6379> zrange set 0 -1
1) "b"
2) "c"
3) "a"
```
**zrange**

- 查看集合指定范围的内容[withscores表示显示分数]

```powershell
127.0.0.1:6379> zrange set 0 -1 withscores
1) "b"
2) "9"
3) "c"
4) "9.9000000000000004"
5) "a"
6) "10"
```
**zincrby**

- 为指定元素增加分数

```powershell
127.0.0.1:6379> zincrby set 20 b
"29"
127.0.0.1:6379> zrange set 0 -1
1) "c"
2) "a"
3) "b"
```
**zrem**

- 删除元素

```powershell
127.0.0.1:6379> zrem set b
(integer) 1
127.0.0.1:6379> zrange set 0 -1
1) "c"
2) "a"
```

### 通用命令

```powershell
keys pattern 	# 查找所有符合给定模式(pattern)的key
exists key		# 检查给定key是否存在
type key		# 返回key所储存的值的类型
ttl key			# 返回给定key的剩余生存时间(TTL,time to live),以秒为单位
del key			# 该命令用于在key存在是删除key
```

```powershell
127.0.0.1:6379> keys *
1) "set"
127.0.0.1:6379> set a 1
OK
127.0.0.1:6379> type a
string
127.0.0.1:6379> type set
zset
127.0.0.1:6379> del set
(integer) 1
127.0.0.1:6379> keys *
1) "a"
127.0.0.1:6379> exists a
(integer) 1
127.0.0.1:6379> exists b
(integer) 0
```

## 在Java中操作Redis

### Jedis

导入jedis的包

```xml
<dependency>
   <groupId>redis.clients</groupId>
   <artifactId>jedis</artifactId>
   <version>4.3.1</version>
</dependency>
```

#### 实际操作

```java
@Test
public void testJedis(){
	//1.获取连接
	Jedis jedis = new Jedis("localhost", 6379);
	//2.执行操作
	jedis.set("username","xiao
	String username = jedis.get("username");
              
	System.out.println(usename);
	jedis.del("username");
              
	jedis.hset("myhash","addr","bj");
	String hget = jedis.hget("myhash", "addr");
              
	System.out.println(hget);
	//3.关闭管理
	jedis.close();
}
```

### SpringDataRedis

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

#### 简介

Spring Data Redis中提供了一个高度封装的类：RedisTemplate,针对jedis客户端中大量api进行了归类封装将同一类型操作封装为operation接口，具体分类如下：

- ValueOperations:简单K-V操作
- SetOperations:set类型数据操作
- ZSetOperations:zset类型数据操作
- HashOperations:针对map类型的数据操作
- ListOperations:针对list类型的数据操作



#### 配置

```yaml
# Redis相关配置
spring:
  data:
    redis:
      host: localhost
      port: 6479
      # password: 123456
      database: 0
      # Redis连接池配置
      jedis:
        pool:
          max-active: 8 # 最大连接数
          max-wait: 1ms # 连接池最大阻塞等待时间
          max-idle: 4   # 连接池中的最大空闲连接
          min-idle: 0   # 连接池中的最小空闲时间
```

然后自动注入即可

```java
@Resource
RedisTemplate<Object,Object> redisTemplate;
```

### String

```java
@Test
public void testString(){
	ValueOperations<Object, Object> valueOperations = redisTemplate.opsForValue();
	valueOperations.set("city", "bj");
}
```

但是redis中存储的是

```powershell
127.0.0.1:6379> keys *
1) "\xac\xed\x00\x05t\x00\x04city"
```

原因是RedisTemplate是Springboot框架提供的，我们需要修改它的序列化方式(也可以不用修改，在Springboot中在获取和设置数据的时候会自动给进行序列化和反序列化)

```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<Object,Object> redisTemplate(RedisConnectionFactory redisConnectionFactory){
        RedisTemplate<Object,Object> redisTemplate = new RedisTemplate<>();

        // 默认的Key序列化器为：JdkSerializationRedisSerializer
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());

        // 可以全局替换序列化方式
        //redisTemplate.setDefaultSerializer(new StringRedisSerializer());
        redisTemplate.setConnectionFactory(redisConnectionFactory);

        return redisTemplate;
    }
}
```



```java
@Test
public void testString(){
	ValueOperations<Object, Object> valueOperations = redisTemplate.opsForValue();
	valueOperations.set("city", "bj");
	String value = (String) valueOperations.get("city");

	System.out.println(value);
	// 设置过期时间
    valueOperations.set("key1", "value1", 10L, TimeUnit.SECONDS);

    // 如果存在则不设置
	Boolean aBoolean = valueOperations.setIfAbsent("city", "hn");
	System.out.println(aBoolean);
}
```

### Hash

```java
@Test
public void testHash(){
	HashOperations<Object, Object, Object> hashOperations = redisTemplate.opsForHash();
	// 存值-->hset
	hashOperations.put("002", "name", "xiaoming");
	hashOperations.put("002", "age", "20");
	hashOperations.put("002", "city", "bj");
	System.out.println("=====================");

	// 取值-->hget
	String age = (String) hashOperations.get("002", "age");
	System.out.println(age);
	System.out.println("=====================");

	// 获取hash结构中的所有字段-->hkeys
	Set<Object> keys = hashOperations.keys("002");
	keys.forEach(s->System.out.println((String)s));
	System.out.println("=====================");

	// 获取hash结构中的所有值-->hvals
	List<Object> values = hashOperations.values("002");
	values.forEach(s->System.out.println((String)s));
	System.out.println("=====================");
}
```

```
=====================
20
=====================
name
age
city
=====================
xiaoming
20
bj
=====================
```

### List

```java
@Test
public void testList() {
	ListOperations<Object, Object> listOperations = redisTemplate.opsForList();
	// 存值-->lpush
	listOperations.leftPushAll("mylist", "a");
	listOperations.leftPushAll("mylist", "b", "c");
	System.out.println("=====================");
	// 取值-->lrange
	List<Object> mylist = listOperations.range("mylist", 0, -1);
	assert mylist != null;
	mylist.forEach(s -> System.out.println((String) s));
	System.out.println("=====================");

	// 获取列表长度->llen
	Long size = listOperations.size("mylist");
	assert size != null;
	int lSize = size.intValue();
	for (int i = 0; i < lSize; i++){
		// 出队列-->rpop
		String element = (String) listOperations.rightPop("mylist");
		System.out.println(element);
	}
	System.out.println("=====================");
}
```

```
=====================
c
b
a
=====================
a
b
c
=====================
```

### Set

```java
@Test
public void testSet() {
	SetOperations<Object, Object> setOperations = redisTemplate.opsForSet();
	// 存值-->sadd
	setOperations.add("myset", "a", "b", "c", "d", "a");
	System.out.println("=====================");

	// 取值-->smembers
	Set<Object> myset = setOperations.members("myset");
	assert myset != null;
	myset.forEach(s -> System.out.println((String) s));
	System.out.println("=====================");

	// 删除成员-->srem
	setOperations.remove("myset", "a","b");
	myset = setOperations.members("myset");
	assert myset != null;
	myset.forEach(s -> System.out.println((String) s));
	System.out.println("=====================");
}
```

```
=====================
c
b
d
a
=====================
d
c
=====================
```

### Zset

```java
@Test
public void testZset() {
	ZSetOperations<Object, Object> zSetOperations = redisTemplate.opsForZSet();
	// 存值-->zadd
	zSetOperations.add("myZset", "a", 10.0);
	zSetOperations.add("myZset", "b", 11.0);
	zSetOperations.add("myZset", "c", 12.0);
	zSetOperations.add("myZset", "a", 13.0);
	System.out.println("=====================");

	// 取值-->zrange
	Set<Object> myZset = zSetOperations.range("myZset", 0, -1);
	assert myZset != null;
	myZset.forEach(s -> System.out.println((String) s));
	System.out.println("=====================");

	// 修改分数-->zincrby
	zSetOperations.incrementScore("myZset", "b", 20.0);
	myZset = zSetOperations.range("myZset", 0, -1);
	assert myZset != null;
	myZset.forEach(s -> System.out.println((String) s));
	System.out.println("=====================");

	// 删除元素-->zrem
	zSetOperations.remove("myZset", "a", "b");
	myZset = zSetOperations.range("myZset", 0, -1);
	assert myZset != null;
	myZset.forEach(s -> System.out.println((String) s));
	System.out.println("=====================");
}
```

```
=====================
b
c
a
=====================
c
a
b
=====================
c
=====================
```

### 通用

```java
@Test
public void testCommon() {
	// 获取Redis中所有的key
	Set<Object> keys = redisTemplate.keys("*");
	assert keys != null;
	System.out.println("=====================");
	keys.forEach(s -> System.out.println((String) s));

	// 判断某个key是否存在
	System.out.println("=====================");
	Boolean itcase = redisTemplate.hasKey("itcase");
	System.out.println(itcase);
	Boolean myset = redisTemplate.hasKey("myset");
	System.out.println(myset);

	// 删除指定key
	redisTemplate.delete("myZset");
	keys = redisTemplate.keys("*");
	assert keys != null;
	System.out.println("=====================");
	keys.forEach(s -> System.out.println((String) s));

	// 获取指定key对于的value的数据类型
	System.out.println("=====================");
	DataType dataType = redisTemplate.type("myset");
	assert dataType != null;
	System.out.println(dataType.name());
	System.out.println("=====================");
}
```



```
=====================
002
myZset
myset
=====================
false
true
=====================
002
myset
=====================
SET
=====================
```

