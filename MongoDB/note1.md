<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [MongoDB再入门](#mongodb%E5%86%8D%E5%85%A5%E9%97%A8)
  - [关于MongoDB](#%E5%85%B3%E4%BA%8Emongodb)
  - [MongoDB版本变迁](#mongodb%E7%89%88%E6%9C%AC%E5%8F%98%E8%BF%81)
  - [MongoDB vs 关系型数据库](#mongodb-vs-%E5%85%B3%E7%B3%BB%E5%9E%8B%E6%95%B0%E6%8D%AE%E5%BA%93)
  - [MongoDB 特色及优势](#mongodb-%E7%89%B9%E8%89%B2%E5%8F%8A%E4%BC%98%E5%8A%BF)
  - [MongoDB 技术优势总结](#mongodb-%E6%8A%80%E6%9C%AF%E4%BC%98%E5%8A%BF%E6%80%BB%E7%BB%93)
  - [MongoDB基本操作](#mongodb%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C)
    - [使用insert完成插入操作](#%E4%BD%BF%E7%94%A8insert%E5%AE%8C%E6%88%90%E6%8F%92%E5%85%A5%E6%93%8D%E4%BD%9C)
    - [使用find查询文档](#%E4%BD%BF%E7%94%A8find%E6%9F%A5%E8%AF%A2%E6%96%87%E6%A1%A3)
    - [使用find搜索子文档](#%E4%BD%BF%E7%94%A8find%E6%90%9C%E7%B4%A2%E5%AD%90%E6%96%87%E6%A1%A3)
    - [使用find搜索数组](#%E4%BD%BF%E7%94%A8find%E6%90%9C%E7%B4%A2%E6%95%B0%E7%BB%84)
    - [使用find搜索数组中的对象](#%E4%BD%BF%E7%94%A8find%E6%90%9C%E7%B4%A2%E6%95%B0%E7%BB%84%E4%B8%AD%E7%9A%84%E5%AF%B9%E8%B1%A1)
    - [控制find返回的字段](#%E6%8E%A7%E5%88%B6find%E8%BF%94%E5%9B%9E%E7%9A%84%E5%AD%97%E6%AE%B5)
    - [使用remove删除文档](#%E4%BD%BF%E7%94%A8remove%E5%88%A0%E9%99%A4%E6%96%87%E6%A1%A3)
    - [使用update更新文档](#%E4%BD%BF%E7%94%A8update%E6%9B%B4%E6%96%B0%E6%96%87%E6%A1%A3)
    - [使用drop删除集合](#%E4%BD%BF%E7%94%A8drop%E5%88%A0%E9%99%A4%E9%9B%86%E5%90%88)
    - [使用dropDatabase删除数据库](#%E4%BD%BF%E7%94%A8dropdatabase%E5%88%A0%E9%99%A4%E6%95%B0%E6%8D%AE%E5%BA%93)
  - [聚合查询](#%E8%81%9A%E5%90%88%E6%9F%A5%E8%AF%A2)
    - [什么是MongoDB聚合框架](#%E4%BB%80%E4%B9%88%E6%98%AFmongodb%E8%81%9A%E5%90%88%E6%A1%86%E6%9E%B6)
    - [管道(Pipeline)和步骤(Stage)](#%E7%AE%A1%E9%81%93pipeline%E5%92%8C%E6%AD%A5%E9%AA%A4stage)
    - [聚合运算的基本格式](#%E8%81%9A%E5%90%88%E8%BF%90%E7%AE%97%E7%9A%84%E5%9F%BA%E6%9C%AC%E6%A0%BC%E5%BC%8F)
    - [聚合运算的使用场景](#%E8%81%9A%E5%90%88%E8%BF%90%E7%AE%97%E7%9A%84%E4%BD%BF%E7%94%A8%E5%9C%BA%E6%99%AF)
  - [聚合实战](#%E8%81%9A%E5%90%88%E5%AE%9E%E6%88%98)
    - [聚合实验一](#%E8%81%9A%E5%90%88%E5%AE%9E%E9%AA%8C%E4%B8%80)
    - [聚合实验二](#%E8%81%9A%E5%90%88%E5%AE%9E%E9%AA%8C%E4%BA%8C)
  - [MongoDB 复制集](#mongodb-%E5%A4%8D%E5%88%B6%E9%9B%86)
    - [复制集的作用](#%E5%A4%8D%E5%88%B6%E9%9B%86%E7%9A%84%E4%BD%9C%E7%94%A8)
    - [典型复制集结构](#%E5%85%B8%E5%9E%8B%E5%A4%8D%E5%88%B6%E9%9B%86%E7%BB%93%E6%9E%84)
    - [数据是如何复制的？](#%E6%95%B0%E6%8D%AE%E6%98%AF%E5%A6%82%E4%BD%95%E5%A4%8D%E5%88%B6%E7%9A%84)
    - [通过选举完成故障恢复](#%E9%80%9A%E8%BF%87%E9%80%89%E4%B8%BE%E5%AE%8C%E6%88%90%E6%95%85%E9%9A%9C%E6%81%A2%E5%A4%8D)
    - [影响选举的因素](#%E5%BD%B1%E5%93%8D%E9%80%89%E4%B8%BE%E7%9A%84%E5%9B%A0%E7%B4%A0)
    - [常见选项](#%E5%B8%B8%E8%A7%81%E9%80%89%E9%A1%B9)
    - [复制集注意事项](#%E5%A4%8D%E5%88%B6%E9%9B%86%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)
  - [搭建MongoDB复制集](#%E6%90%AD%E5%BB%BAmongodb%E5%A4%8D%E5%88%B6%E9%9B%86)
  - [MongoDB 全家桶](#mongodb-%E5%85%A8%E5%AE%B6%E6%A1%B6)
    - [Mongodump/mongorestore](#mongodumpmongorestore)
    - [Atlas-MongoDB公有云托管服务](#atlas-mongodb%E5%85%AC%E6%9C%89%E4%BA%91%E6%89%98%E7%AE%A1%E6%9C%8D%E5%8A%A1)
    - [MongoDB BI Connector](#mongodb-bi-connector)
    - [MongoDB Ops Manager-集群管理平台](#mongodb-ops-manager-%E9%9B%86%E7%BE%A4%E7%AE%A1%E7%90%86%E5%B9%B3%E5%8F%B0)
    - [MongoDB Charts](#mongodb-charts)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# MongoDB再入门

## 关于MongoDB
MongoDB是一个以JSON为数据模型的文档数据库，文档来自于"JSON Document"

MongoDB主要用途：应用数据库，类似于Oracle，MySQL，海量数据处理，数据平台

MongoDB横向扩展可以支撑很大数据量和并发。

MongoDB4.0之后已经完全支持ACID事务

MongoDB有两个发布版本：社区版和企业版。社区版基于SSPL，一种和AGPL基本类似的开源协议。企业版是基于商业协议，需付费使用。

## MongoDB版本变迁
- 0.x起步阶段 2008
- 1.x支持复制集和分片集 2010
- 2.x更丰富的数据库功能 2012
- 3.xWiredTiger和周边生态环境 2014
- 4.x分布式事务支持 2018

## MongoDB vs 关系型数据库
![image](https://user-images.githubusercontent.com/34932312/73416580-30006b80-4350-11ea-8a93-723e1afaef00.png)

## MongoDB 特色及优势
- 灵活性
    - 多形性：同一个集合中可以包含不同字段（类型）的文档对象，可动态增加新字段
    -  动态性：线上修改数据模式，修改时应用与数据库均无需下线
    - 数据治理：支持使用JSON Schema来支持范数据模式。在保证模式灵活动态的前提下，提供数据治理能力
- 快速性
    - 数据库引擎只需要在一个存储区读写
    - 反范式、无关联的组织极大优化查询速度
    - 程序API自然，开发快速

![image](https://user-images.githubusercontent.com/34932312/73417224-52938400-4352-11ea-9794-18460ffd49b5.png)
- Replica Set - 2 to 50个成员 （MongoDB在部署的时候默认就是三个节点复制集部署的方式，复制集英文replica set，最佳实践永远是3个以上，要保证投票机制必须是个多数机制所以必须是个奇数）
- 自恢复 （主节点故障，从节点马上自动恢复到主节点）
- 多中心容灾能力
- 滚动服务-最小化服务终端

![image](https://user-images.githubusercontent.com/34932312/73419027-a43f0d00-4358-11ea-8306-35a9dc5fda0c.png)

## MongoDB 技术优势总结
- JSON结构和对象模型接近，开发代码量低
- JSON的动态模型意味着更容易响应新的业务需求
- 复制集提供99.999%高可用
- 分片架构支持海量数据和无缝扩容

## MongoDB基本操作
### 使用insert完成插入操作
操作格式：
```
db.<collection>.insertOne(<JSON对象>)
db.<collection>.insertMany([<JSON1>, <JSON2>, ..., <JSON N>])

db.fruit.insertOne({name: "apple"})
db.fruit.insertMany([{name: "apple"}, {name: "pear"}, {name: "orange"}])
```

### 使用find查询文档
find是MongoDB中查询数据的基本命令，find返回的是游标
```
db.movies.find({"year": 1975}) // 单条件查询
db.movies.find({"year": 1989, "title": "Batman"}) // 多条件and查询
db.movies.find({$and:[{"title": "Batman"}, {"category": "action"}]}) // 多条件and查询
db.movies.find({$or: [{"year": 1989}, {"title": "Batman"}]}) // 多条件or查询
db.movies.find({"title": /^B/}) //按正则表达式查询 大写B开头的找回来
```

MongoDB中查询对照表
```
a = 1   {a: 1}
a <> 1  {a: {$ne: 1}}
a > 1   {a: {$gt: 1}}
a >= 1  {a: {$gte: 1}}
a < 1   {a: {$lt: 1}}
a <= 1  {a: {$lte: 1}}
```

查询逻辑对照表
```
a = 1 AND b = 1  {a: 1, b: 1}或{$and: [{a: 1}, {b: 1}]}
a = 1 OR b = 1   {$or: [{a: 1}, {b: 1}]}
a is null        {a: {$exists: false}}
a IN (1, 2, 3)   {a: {$in: [1, 2, 3]}}
```

查询逻辑运算法
```
$lt: 存在并小于
$lte: 存在并小于等于
$gt: 存在并大于
$gte: 存在并大于等于
$ne: 不存在或存在但不等于
$in: 存在并在指定数组中
$nin: 不存在或不在指定数组中
$or: 匹配两个或多个条件中的一个
$and: 匹配全部条件
```

### 使用find搜索子文档
find支持使用"field.sub_field"的形式查询子文档。假设有一个文档：
```
db.fruit.insertOne({
    name: "apple",
    from: {
        country: "China",
        province: "Guangdon"
    }    
})
```

使用以下查询：
```
db.fruit.find({"from.country": "China"})
```

### 使用find搜索数组
find支持对数组中的元素进行搜索
```
db.fruit.insert([
    {"name": "Apple", color: ["red", "green"]},
    {"name": "Mango", color: ["yellow", "green"]}
])
```


```
db.fruit.find({"color": "red"}) // 返回一条
db.fruit.find({$or: [{color: "red"}, {color: "yellow"}]}) //返回两条
```

### 使用find搜索数组中的对象
```
db.movies.insertOne( {
          "title" : "Raiders of the Lost Ark",
          "filming_locations" : [
              { "city" : "Los Angeles", "state" : "CA", "country" : "USA" },
              { "city" : "Rome", "state" : "Lazio", "country" : "Italy" },
              { "city" : "Florence", "state" : "SC", "country" : "USA" }
          ]
})

```
查找城市是 Rome 的记录
```
db.movies.find({"filming_locations.city": "Rome"})
```
在数组中搜索子对象的多个字段时，如果使用 $elemMatch，它表示必须是同一个子对象满足多个条件。考虑以下两个查询:
```
 db.getCollection('movies').find({
       "filming_locations.city": "Rome",
       "filming_locations.country": "USA"
 })
 
 db.getCollection('movies').find({
       "filming_locations": {$elemMatch:{"city":"Rome", "country": "USA"}} 
 })
```

### 控制find返回的字段
- MongoDB中可以使用投影（projection）来控制返回指定的字段；
- _id字段必须明确指明不返回，否则默认返回；
- find可以指定只返回指定的字段
```
db.movies.find({"category": "action"}, {"_id":0,title:1}) 不返回_id，返回title
```

### 使用remove删除文档
- remove命令需要配合查询条件使用
- 匹配查询条件的文档会被删除
- 指定一个空文档条件会删除所有文档
```
db.testcol.remove({a:1}) // 删除a==1的记录
db.testcol.remove({a:{$lt:5}}) //删除a<5的记录
db.testcol.remove({}) // 删除所有记录
db.testcol.remove() //报错
```

### 使用update更新文档
- update操作执行格式：
```
db.<collection>.update(<查询字段>, <更新字段>)
```

```
db.fruit.insertMany([{name: "apple"}, {name: "pear"}, {name: "orange"}])

db.fruit.updateOne({name: "apple"}, {$set: {from: "China"}})
查询name为apple的记录   将找到记录的from设置为China
```

- 使用updateOne表示无论条件匹配多少条记录，始终只更新第一条
- 使用updateMany表示条件匹配多少条就更新多少条
- updateOne/updateMany方法要求更新条件部分必须具有以下之一，否则将会报错
    - $set/$unset
    - $push/$pushAll/$pop
    - $pull/$pullAll
    - $addToSet
- // 报错 db.fruit.updateOne({name:"apple"}, {from: "China"})

```
$push: 增加一个对象到数组底部
$pushAll：增加多个对象到数组底部
$pop：从数组底部删除一个对象
$pull：如果匹配指定的值，从数组中删除相应的对象
$pullAll：如果匹配任意的值，从数据中删除相应的对象
$addToSet：如果不存在则增加一个值到数组
```

### 使用drop删除集合
- 使用db.<collection>.drop()来删除一个集合
- 集合中的全部文档都会被删除
- 集合相关的索引也会被删除

### 使用dropDatabase删除数据库
- 使用db.dropDatabase()来删除数据库
- 数据库相应文件也会被删除，磁盘空间将会被释放
```
use tempDB
db.dropDatabase()
show collections //no collections
show dbs // the dbs is gone
```

## 聚合查询
可以用来做一些分析型统计型的场景，也可以用于复杂数据的计算和处理

### 什么是MongoDB聚合框架
MongoDB聚合框架(Aggregation Framework)是一个计算框架，它可以
- 作用在一个或几个集合上
- 对集合中的数据进行一系列运算
- 将这些数据转化为期望的形式

从效果而言，聚合框架相当于SQL查询中的GROUP BY  LEFT OUTER JOIN AS等

### 管道(Pipeline)和步骤(Stage)
整个聚合运算过程称为管道(Pipeline)，它是由多个步骤(Stage)组成的，每个管道：
- 接受一系列文档(原始数据)
- 每个步骤对这些文档进行一系列运算
- 结果文档输出给下一个步骤
![image](https://user-images.githubusercontent.com/34932312/73507518-23454b80-4414-11ea-8a17-41feeb7d9b37.png)

### 聚合运算的基本格式
```
pipeline = [$stage1, $stage2, ...$stageN];
db.<collection>.aggregate(
    pipeline,
    {options}
);
```

![image](https://user-images.githubusercontent.com/34932312/73507698-c4340680-4414-11ea-9f5f-dbef0e9ecf30.png)

常见步骤中的运算法
![image](https://user-images.githubusercontent.com/34932312/73507729-dada5d80-4414-11ea-9c3e-d59a24d4c22e.png)

常见搜索
![image](https://user-images.githubusercontent.com/34932312/73507804-16752780-4415-11ea-8744-0ffd864cebc2.png)

### 聚合运算的使用场景
聚合查询可以用于OLAP和OLTP场景。例如：
OLTP:计算
OLAP:1、分析一段时间内的销售总额、均值 2、计算一段时间内的净利润 3、分析购买人的年龄分布 4、分析学生成绩分布 5、统计员工绩效

![image](https://user-images.githubusercontent.com/34932312/73507952-81266300-4415-11ea-9f07-b9326955994f.png)

![image](https://user-images.githubusercontent.com/34932312/73507986-9c916e00-4415-11ea-8f51-c3f543140509.png)

![image](https://user-images.githubusercontent.com/34932312/73508104-f6923380-4415-11ea-92c2-4070849e7173.png)

![image](https://user-images.githubusercontent.com/34932312/73508171-280aff00-4416-11ea-8b12-a02a6113bb92.png)

![image](https://user-images.githubusercontent.com/34932312/73508205-453fcd80-4416-11ea-9bf8-145b75e9fd7f.png)

## 聚合实战
聚合实验数据模型
![image](https://user-images.githubusercontent.com/34932312/73539539-8c56ae80-4468-11ea-8819-7cb512a8616b.png)

### 聚合实验一
计算到目前为止的所有订单的总销售额
```
db.orders.aggregate([
    {$group: 
        {
            _id: null,   //在哪个订单上进行分组 _id：null就是没有分组作为个大分组
            total: {$sum: "$total"}  
        }
    }
])
```
运行结果：
```
{ "_id" : null, "total" : NumberDecimal("44019609") }
```

### 聚合实验二
查询2019年第一季度已完成订单的订单总金额和订单总数
```
db.orders.aggregate([
    // 步骤1：匹配条件
    {$match: {status: "completed", orderDate: {$gte: ISODate("2019-01-01"), $lt: ISODate("2019-04-01")}}},
    
    // 步骤2：聚合订单总金额、总运费、总数量
    {$group: {_id: null, total: {$sum: "$total"}, shippingFee: {$sum: "$shippingFee"}, count: {$sum: 1}}},   
    
    // 步骤3：投影
    {$project: {
        // 计算总金额
        grandTotal: {$add: ["$total", "$shippingFee"]},
        count: 1, //显示
        _id: 0 // 不显示
    }}
])
```

结果
```
{"count": 5875, "grandTotal": NumberDecimal("2636376.00")}
```

可以使用MongoDB Compass工具辅助完成聚合运算！！！通过右边及时的输出可以及时看出聚合操作是否准确

## MongoDB 复制集
### 复制集的作用
- MongoDB复制集的主要意义在于实现服务的高可用
- 它的现实依赖于两个方面的功能：
    - 数据写入时将数据迅速复制到另一个独立节点上
    - 在接受写入的节点发生故障时自动选举出一个新的替代节点

- 在实现高可用的同时，复制集实现了其他几个附加作用：
    - 数据分发：将数据从一个区域复制到另一个区域，减少另一个区域的读延迟
    - 读写分离：不同类型的压力分别在不同的节点上执行
    - 异地容灾：在数据中心故障时候快速切换到异地

### 典型复制集结构
- 一个典型的复制集由3个以上具有投票权的节点组成，包括：
    - 一个主节点（PRIMARY）：接受写入操作和选举时投票
    - 两个（或多个）从节点（SECONDARY）：复制主节点上的新数据和选举时投票
    - 不推荐使用Arbiter（投票节点）  
![image](https://user-images.githubusercontent.com/34932312/73586286-f5c8d280-44e5-11ea-9292-41c5a4a29d81.png)

### 数据是如何复制的？
- 当一个修改操作，无论是插入、更新或删除，到达主节点时，它对数据的操作将被记录下来(经过一些必要的转换)，这些记录称为oplog。
- 从节点通过在主节点上打开一个 tailable 游标不断获取新进入主节点的 oplog，并在自己的数据上回放，以此保持跟主节点的数据一致。

![image](https://user-images.githubusercontent.com/34932312/73586324-899a9e80-44e6-11ea-8f9c-2b83b79ef5cd.png)

### 通过选举完成故障恢复
- 具有投票权的节点之间两两相互发送心跳；
- 当5次心跳未收到时判断为节点失联;
- 如果失联的是主节点，从节点会发起选举，选出新的主节点;
- 如果失联的是从节点则不会产生新的选举;
- 选举基于 RAFT一致性算法 实现，选举成功的必要条件是大多数投票节点存活;
- 复制集中最多可以有50个节点，但具有投票权的节点最多7个。
![image](https://user-images.githubusercontent.com/34932312/73587097-ea7ba400-44f1-11ea-81ca-5609aa163a2d.png)

### 影响选举的因素
- 整个集群必须有大多数节点存活;
- 被选举为主节点的节点必须:
    - 能够与多数节点建立连接
    - 具有较新的 oplog
    - 具有较高的优先级(如果有配置)

### 常见选项
- 复制集节点有以下常见的选配项
    - 是否具有投票权（v参数）：有则参与投票
    - 优先级（priority参数）：优先级越高的节点越优先成为主节点。优先级为0的节点无法成为主节点。
    - 隐藏（hidden参数）：复制数据，但对应用不可见。隐藏节点可以具有投票权，但优先级必须为0 （作为数据的额外的一份备份）
    - 延迟（slaveDelay参数）：复制n秒之前的数据，保持与主节点的时间差 （设置一个延迟节点，设置1个小时或者12个小时等，避免主节点误删数据造成的损失）

### 复制集注意事项
- 关于硬件
    - 因为正常的复制集节点都有可能成为主节点，它们的地位是一样的，因此硬件配置上必须一致
    - 为了保证节点不会同时宕机，各节点使用的硬件必须具有独立性。
- 关于软件
    - 复制集各节点软件版本必须一致，以避免出现不可预知的问题
- 增加节点不会增加系统写性能    

## 搭建MongoDB复制集
目标
- 本实验中，我们通过在一台机器上运行3个实例来搭建一个最简单的复制集。通过实验，我们将学会:
    - 如何启动一个 MongoDB 实例
    - 如何将3个 MongoDB 实例搭建成一个复制集
    - 如何对复制集运行参数做一些常规调整
    
1.准备
- 安装最新的 MongoDB 版本
- Windows 系统请事先配置好 MongoDB 可执行文件的环境变量
- Linux 和 Mac 系统请配置 PATH 变量
- 确保有 10GB 以上的硬盘空间

2.创建数据目录
- MongoDB 启动时将使用一个数据目录存放所有数据文件。我们将为3个复制集节点创建各自的数据目录。
- Linux/MacOS:
```
mkdir -p /data/db{1,2,3}
```   

3.准备配置文件

复制集的每个mongod进程应该位于不同的服务器。我们现在在一台机器上运行3个进程，因此要为它们各自配置:

- 不同的端口。示例中将使用28017/28018/28019
- 不同的数据目录。示例中将使用: /data/db1，/data/db2，/data/db3
- 不同日志文件路径。示例中将使用: /data/db1/mongod.log，/data/db2/mongod.log，/data/db3/mongod.log

Linux/MacOS
```
# /data/db1/mongod.conf
systemLog:
    destination: file
    path: /data/db1/mongod.log # log path
    logAppend: true
storage:
    dbPath: /data/db1 # data directory
net:
    bindIp: 0.0.0.0 //意为MongoDB将在所有网卡地址上监听，如果没有这个参数MongoDB默认只会在localhost上127.0.0.1上监听
    port: 28017   # port
replication:
    replSetName: rs0 
processManagement:
    fork: true // 意为此进程作为一个独立的后台进程运行     
``` 
4.启动MongoDB进程

Linux/Mac:
```
    mongod -f /data/db1/mongod.conf   // -f 表示配置文件 指向配置文件的路径
    mongod -f /data/db2/mongod.conf
    mongod -f /data/db3/mongod.conf
```

5.配置复制集

目前三个mongod实例是互相独立的

方法1：在一个节点上初始化，然后把其他两个节点分别加上去
```
# mongo --port 28017
> rs.initiate()
> rs.add(”HOSTNAME:28018")
> rs.add(”HOSTNAME:28019")

注意:此方式hostname需要能被解析
```
方法2：给配置文件，
```
# mongo --port 28017
> rs.initiate({
    _id: "rs0",
    members: [{
        _id: 0,
        host: "localhost:28017" 
    },{
        _id: 1,
        host: "localhost:28018" 
    },{
        _id: 2,
        host: "localhost:28019" 
    }]
})    
```
    
6.验证

MongoDB主节点进行写入
```
# mongo localhost:28017
> db.test.insert({a:1})
> db.test.insert({a:2})
```    

MongoDB从节点进行读
```
# mongdo localhost:28018
> rs.slaveOK()  // 允许从节点读
> db.test.find()
> db.test.find()
```    

## MongoDB 全家桶
- mongod MongoDB数据库软件
- mongo MongoDB命令行工具，管理MongoDB数据库
- mongos MongoDB路由进程，分片环境下使用
- mongodump/mongorestore 命令行数据库备份与恢复工具
- mongoexport/mongoimport CSV/JSON导入与导出，主要用于不同系统间数据迁移
- Compass MongoDB GUI 管理工具
- Ops Manager（企业版） MongoDB集群管理软件
- BI Connector（企业版）SQL解释器/BI套接件
- MongoDB Charts(企业版) MongoDB可视化软件
- Atlas（付费及免费） MongoDB云托管服务，包括永久免费云数据库

### Mongodump/mongorestore
- 类似于 MySQL 的 dump/restore 工具
- 可以完成全库 dump:不加条件
- 也可以根据条件 dump 部分数据:-q 参数
- Dump 的同时跟踪数据就更:--oplog
- Restore 是反操作，把 mongodump 的输出导入到 mongodb

最常见的用途：每天晚上备份，出了问题restore回来
```
mongodump -h 127.0.0.1:27017 -d test -c test // -d 指数据库 -c 指集合
mongorestore -h 127.0.0.1:27017 -d test -c test xxx.bson
```

### Atlas-MongoDB公有云托管服务
![image](https://user-images.githubusercontent.com/34932312/73588641-844d4c00-4506-11ea-9fd8-3849298b67a8.png)

### MongoDB BI Connector
提供MySQL语法兼容的SQL解释器

### MongoDB Ops Manager-集群管理平台
![image](https://user-images.githubusercontent.com/34932312/73588775-00945f00-4508-11ea-8699-e4d81956759f.png)

### MongoDB Charts
![image](https://user-images.githubusercontent.com/34932312/73588794-2588d200-4508-11ea-9aae-e0ff545b28fa.png)
