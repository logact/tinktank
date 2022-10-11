# 参考
[面渣逆袭（Redis面试题八股文）必看👍 | Java 程序员进阶之路 (tobebetterjavaer.com)](https://tobebetterjavaer.com/sidebar/sanfene/redis.html)
### 1.说说什么是Redis?

![Redis图标](http://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/redis-96e079f9-49a3-4c55-b0a4-47d043732b62.png)

Redis是一种基于键值对（key-value）的NoSQL数据库。

比一般键值对数据库强大的地方，Redis中的value支持string（字符串）、hash（哈希）、 list（列表）、set（集合）、zset（有序集合）、Bitmaps（位图）、 HyperLogLog、GEO（地理信息定位）等多种数据结构，因此 Redis可以满足很多的应用场景。

而且因为Redis会将所有数据都存放在内存中，所以它的读写性能非常出色。

不仅如此，Redis还可以将内存的数据利用快照和日志的形式保存到硬盘上，这样在发生类似断电或者机器故障的时候，内存中的数据不会“丢失”。

除了上述功能以外，Redis还提供了键过期、发布订阅、事务、流水线、Lua脚本等附加功能。

总之，Redis是一款强大的性能利器。

### 2.Redis可以用来干什么？
![[Pasted image 20221010171348.png]]
1.  缓存
    
    这是Redis应用最广泛地方，基本所有的Web应用都会使用Redis作为缓存，来降低数据源压力，提高响应速度。 ![Redis缓存](http://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/redis-d44c2397-5994-452f-8b7b-eb85d2b87685.png)
    
2.  计数器 Redis天然支持计数功能，而且计数性能非常好，可以用来记录浏览量、点赞量等等。
    
3.  排行榜 Redis提供了列表和有序集合数据结构，合理地使用这些数据结构可以很方便地构建各种排行榜系统。
    
4.  社交网络 赞/踩、粉丝、共同好友/喜好、推送、下拉刷新。
    
5.  消息队列 Redis提供了发布订阅功能和阻塞队列的功能，可以满足一般消息队列功能。
    
6.  分布式锁 分布式环境下，利用Redis实现分布式锁，也是Redis常见的应用。
    

Redis的应用一般会结合项目去问，以一个电商项目的用户服务为例：

-   Token存储：用户登录成功之后，使用Redis存储Token
-   登录失败次数计数：使用Redis计数，登录失败超过一定次数，锁定账号
-   地址缓存：对省市区数据的缓存
-   分布式锁：分布式环境下登录、注册等操作加分布式锁