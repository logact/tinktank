用于数据的存储检索
## 概念理论
### 基本概念
![[Pasted image 20221016134038.png]]
1. Index 
	1. 动词相当于Mysql中的insert
	2. 名词相当于Mysql中的database
2. Type
	1. 在index中可以定义一种或者多种类型
	2. 类似于mysql中的table多个类型放在一起
3. Document
	1. 保存在某一个Index下，某种类型Type下的一个数据
4. 倒排索引
	1. 存储数据的时候将整个一句话拆分为单词
![[Pasted image 20221016134501.png]]

## 最佳实战
### 安装elasticsearch
##### 使用docker
``` shell
sudo docker pull elasticserach:7.4.2
sudo docker pull kibana:7.4.2
mkdir -p ~/mydata/elasticsearch/config
mkdir -p ~/mydata/elasticsearch/data
echo "http.host:0.0.0.0">>~/mydata/elsticsearch/config/elasticsearch.yml
sudo docker run --name elasticsearch -p 9200:9200 -p 9300:9300 \
-e discovery-type="single-node" \
-e ES_JAVA_OPTS="-Xms64m -Xmx128m" \
-v ~/mydata/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v ~/mydata/elasticsearch/data:/usr/share/elasticsearch/data \
-v ~/mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-d elasticsearch:7.4.2

# 安装kibana
sudo docker run --name kibana -e ELASTICSEARCH_HOSTS=http://192.168.1.102:9200 -p 5601:5601 \
-d kibana:7.4.2
```

### 基本的命令
![[Pasted image 20221016144040.png]]
### 基本增删改查
##### 增加文档
```shell
PUT customer/external/1
```
数据
```json
{
"data":"nike"
}

```
#####  可以使用seq_no来实现乐观锁
在API中添加参数就可以
![[Pasted image 20221016145757.png]]
##### 查询文档
使用get请求就可以了
##### 更新文档
使用_update如果原来的数据和现在的数据如果和原来的数据一样那么就什么都不做，序列号和版本号都不变。而如果使用post，put不带_update那么就会不断的更新。
##### 删除文档
使用delete请求删除请求。
##### 批量修改
使用_bulk
### 进阶操作
##### 高级检索
需要带上_search
可以使用get +请求体来进行复杂的查询，请求体的参数称为DSL
######  使用match
###### 使用match_phrase
###### 使用multi match
######  使用bool查询
######  使用filter过滤
使用fiter与match 的区别就是filter没有相关性得分
###### term
避免使用文本字段使用term。不要再全文检索中使用
###### 聚合
aggregation
1. avg
2. terms

##### 映射
es会在第一次存储数据库的时候猜测数据的类型，所以在es7以后就直接移除了类型。映射就是每个字段的数据类型。
``` 
使用mapping可以设置字段的类型

```
想要修改映射必须迁移到新的索引中
使用POST _reindex.迁移数据



##### 版本改变
在7.0后就不使用type了。因为在底层对于不同的type的同名字段处理是一样的。
##### 分词
使用_analyze 请求接口可以查询分词结果。
默认的分词器无法分中文，所以使用IK分词器。
###### 安装IK分词器
IK 分词器有ik_smart 和 ik_max_word两种常用的分词器
1. ik_smart:
	-  找到一个最恰当的分词组合
2. ik_max_word:
	- 找到最大的分词组合
如何让分词器添加词库？
1. 让词库放入nginx
2. 放入词库到服务器地址里面
![[Pasted image 20221016174157.png]]


### Java操作
### es在谷粒商城中的应用
[[谷粒商城网课记录]]
#### 业务搜索
##### 如何设计索引结构


#### 采集日志
![[Pasted image 20221016211751.png]]






