[[redis]]
## 使用docke安装redis
```Shell
sudo docker run -p 6379:6379 --name redis -v  ~/mydata/redis/data:/data -v ~/mydata/redis/conf/redis.conf:/etc/redis/redis.conf -d redis  redis-server /etc/redis/redis.conf
```
集群部署多多安装
redis1
``` shell
sudo docker run -p 6380:6379 --name redis1 -v  ~/mydata/redis1/data:/data -v ~/mydata/redis1/conf/redis.conf:/etc/redis/redis.conf -d redis  redis-server /etc/redis/redis.conf
```
redis2
``` shell
sudo docker run -p 6381:6379 --name redis2 -v  ~/mydata/redis2/data:/data -v ~/mydata/redis2/conf/redis.conf:/etc/redis/redis.conf -d redis  redis-server /etc/redis/redis.conf
```

redis3
``` shell
sudo docker run -p 6382:6379 --name redis3 -v  ~/mydata/redis3/data:/data -v ~/mydata/redis3/conf/redis.conf:/etc/redis/redis.conf -d redis  redis-server /etc/redis/redis.conf
```

redis4
``` shell
sudo docker run -p 6383:6379 --name redis4 -v  ~/mydata/redis4/data:/data -v ~/mydata/redis4/conf/redis.conf:/etc/redis/redis.conf -d redis  redis-server /etc/redis/redis.conf
```

redis5
``` shell
sudo docker run -p 6384:6379 --name redis5 -v  ~/mydata/redis5/data:/data -v ~/mydata/redis5/conf/redis.conf:/etc/redis/redis.conf -d redis  redis-server /etc/redis/redis.conf
```
记下一个问题：protected mode 如果在redis没有设置密码的时候这个protectedmode就会启动只能够通过本地环回地址访问不能通过局域网访问。
docker 进入容器的方式
```shell
sudo docker exec -it 775c7c9ee1e1 /bin/bash
```
在docker中如果将容器删除不会释放这个容器对应的logs，而是要手动删除或者是将容器重启，（在使用snap安装的时候不能够通过service docker restart 重启，而是要通过sudo snap restart docker重启）。
### 主从节点配置
只要在从节点中加入replicaof就可以连接到主节点，但是在一台机器下如果有多个redis实例那么就会连接失败。情况不明
- [ ]  redis在同一个主机下搭建主从集群失败
### 主从节点加入哨兵
### cluster模式