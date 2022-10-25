#todo 
[[redis]]
## redis 客户端
1. Redission,Jedis,RedisTemplate,SpringbootCache联系与区别?
2. Jedis 和redission的区别
	1. Jedis使用的是阻塞式的IO,Redission使用的是异步的IO
	2. Jedis支持的数据集相比Redission少
	3. Jedis比较老牌是争对单机设计的
	4. Redission针对高并发多主机设计的，它是线程安全的
	5. 总而言之Redission功能更加强大，但是Jedis更加简单
6. redisTemplate是spring提供的一个抽象
## redis在springboot中使用的几种方式
## redis实现功能
### 实现增删改查
#### redission
1. 直接使用 redisson-spring-boot-starter
		1.参考
			1.  [redisson/redisson-spring-boot-starter at master · redisson/redisson (github.com)](https://github.com/redisson/redisson/tree/master/redisson-spring-boot-starter)
	 2.  直接引入redisson-spring-boot-starter
	 - [ ] 3.  redission使用基本的增删改查操作
2. redission使用SpringbootCache
#### SpringbootCache
##### **@Cacheable** 主要是针对方法配置，能根据方法的请求参数对结果进行缓存

```java
    //@Cacheable(cacheNames = "user",key = "#id")
    @Cacheable(cacheNames = "user",key = "#id",,condition = "#id > 0")
    //当参数为对象格式时，key = #参数值.id  或者  #p0.id
    //当参数值为id的时候，可直接 key = “#id”  或是  #p0
    //@Cacheable(cacheNames = "user")
    @Override
    public Suser selectByPrimaryKey(int id) {
        return userDao.selectByPrimaryKey(id);
    }
```
@Cacheable的参数：  
**cacheNames**：缓存的名称，必须至少指定一个  
**key**：缓存的关键字，可以为空，按照spEL表达式编写，如果为空则按照所有参数进行组合定义  
**condition**：使用缓存的条件，可以为空，使用spEl表达式编写，结果为“true”才执行缓存，“false”则不缓存。  
##### **@CachePut**
- [x] 在redission实现这个更新的过程中这是如何做的，是否会使用延迟双删？ 
不会在高并发的情况下才需要对此做出延迟双闪
- [ ] 使用evicat实现延迟双删
[(75 封私信 / 80 条消息) @CacheEvict可以做到延迟双删吗？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/447824154)

在Spring Cache中，在我们使用@Cacheable注解时，我们会先查找在缓存中是否存在该key的缓存，如果没有，会查找数据库数据并添加到缓存中，而我们在使用@CachePut注解时，无论在缓存中是否存在缓存，程序都会执行，并将查到的结果添加指定的缓存中。

```java
 //@CachePut也可以标注在类上和方法上。使用@CachePut时我们可以指定的属性跟@Cacheable是一样的。
   @CachePut("user")//每次都会执行方法，并将结果存入指定的缓存中
   public User find(Integer id) {
      return null;
   }
```

##### **@CacheEvict**  

@CacheEvict是用来标注在需要清除缓存元素的方法或类上的。当标记在一个类上时表示其中所有的方法的执行都会触发缓存的清除操作。@CacheEvict可以指定的属性有value、key、condition、allEntries和beforeInvocation。其中value、key和condition的语义与@Cacheable对应的属性类似。即value表示清除操作是发生在哪些Cache上的（对应Cache的名称）；key表示需要清除的是哪个key，如未指定则会使用默认策略生成的key；condition表示清除操作发生的条件。

@CacheEvit 的属性：  
**cachename**：缓存的名称，至少指定一个

**key**：缓存的关键字，可以为空，按照spEL表达式编写，如果为空则按照所有参数进行组合定义

**condition**：使用缓存的条件，可以为空，使用spEl表达式编写，结果为“true”才执行清空缓存，“false”则不清空缓存。

**allEntries**：是否清空所有缓存，使用spEl表达式编写，当程序执行时，为true则全部清空缓存，false则不全部清空，缺省为false。  
**beforeInvocation**：是否在程序执行前清空缓存，为true时则在程序执行前清空缓存，为false则不清空，缺省为false。

---

##### **@Caching**  
@Caching注解可以让我一个类或方法上定义多个Spring cache相关的注解，其拥有三个属性：cacheable、put和evict，分别用于指定@Cacheable、@CachePut和@CacheEvict。

```java
@Caching(cacheable = @Cacheable("user"), evict = { @CacheEvict("cache2"),
   @CacheEvict(value = "cache3", allEntries = true) })
   public User find(Integer id) {
      return null;
   }
```
- [ ] 在springbootCache中的 cache乱码的问题
- [x] redis的db0,db1,db2的问题
**redis中有多个数据库的问题**
1. 在单体情况下redis中默认有16个数据库，而且redis不支持自定义数据库的名字，而且对于每个数据库不能单独设置密码，可以通过select命令选择使用的数据库。
2. 在集群模式下，redis不支持使用select来切换数据库，因为在集群环境下只有一个db0数据库
3. 应以命名空间的方式理解Redis数据库db，**多个应用程序不应使用同一个Redis的不同库，而应一个应用程序对应一个Redis实例，不同的数据库可用于存储不同环境的数据。**
### 实现分布式锁
### 高级操作
#### 直接命令操作
#### lua脚本