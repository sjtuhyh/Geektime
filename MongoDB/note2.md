# 从熟练到精通的开发之路

## 模型设计基础

### 数据模型
什么是数据模型？

数据模型是一组符号、文本组成的集合，用以准确表达信息，达到有效交流、沟通的目的。

Steve Hoberman 霍伯曼.数据建模经典教程

### 数据模型设计的元素
![image](https://user-images.githubusercontent.com/34932312/73591702-e620ac80-452c-11ea-8eed-361ffb5aaf17.png)

### 传统模型设计：从概念到逻辑到物理
![image](https://user-images.githubusercontent.com/34932312/73591741-69420280-452d-11ea-96c0-7cb2b5b423ab.png)

![image](https://user-images.githubusercontent.com/34932312/73591797-20d71480-452e-11ea-8502-5fcea7bc271e.png)
![image](https://user-images.githubusercontent.com/34932312/73591799-27658c00-452e-11ea-9d54-189c77ec912a.png)
![image](https://user-images.githubusercontent.com/34932312/73591802-32b8b780-452e-11ea-9377-be034262b0b2.png)

### 模型设计小结
数据模型的三要素：实体、属性、关系

数据模型的三层深度：概念模型、逻辑模型、物理模型，一个模型逐步细化的过程

## JSON文档模型设计特点
### MongoDB 文档模型设计的三个误区
- 不需要模型设计
- MongoDB应该用一个超级大文档来组织所有数据
- MongoDB不支持关联或者事务
以上陈述均为错！

![image](https://user-images.githubusercontent.com/34932312/73591875-0f423c80-452f-11ea-98b6-7849a3f25611.png)
![image](https://user-images.githubusercontent.com/34932312/73591880-2123df80-452f-11ea-84a7-71240248f803.png)

![image](https://user-images.githubusercontent.com/34932312/73591908-6ea04c80-452f-11ea-82a0-607dfeca52ef.png)
![image](https://user-images.githubusercontent.com/34932312/73591919-8c6db180-452f-11ea-8902-7d3d30083266.png)
![image](https://user-images.githubusercontent.com/34932312/73591920-92fc2900-452f-11ea-8194-e680d6b1cff8.png)

## 文档模型设计之一：基础设计
![image](https://user-images.githubusercontent.com/34932312/73592092-6ba65b80-4531-11ea-8f11-5a9b66a16c88.png)

第一步:建立基础文档模型

1. 根据概念模型或者业务需求推导出逻辑模型 – 找到对象

2. 列出实体之间的关系(及基数) - 明确关系

3. 套用逻辑设计原则来决定内嵌方式 – 进行建模

4. 完成基础模型构建

一个联系人管理应用的例子
![image](https://user-images.githubusercontent.com/34932312/73592123-d6579700-4531-11ea-9abf-d3a09b3e1da5.png)

![image](https://user-images.githubusercontent.com/34932312/73592266-4dd9f600-4533-11ea-8741-54f6e80cc0ae.png)
![image](https://user-images.githubusercontent.com/34932312/73592280-6f3ae200-4533-11ea-830f-7f8522deaa2e.png)
![image](https://user-images.githubusercontent.com/34932312/73592285-81b51b80-4533-11ea-94e5-91446067f9b3.png)

基础建模小结

90:10 规则: 大部分时候你会使用内嵌来表示 1-1，1-N，N-N

内嵌类似于预先聚合(关联)

内嵌后对读操作通常有优势(减少关联)

## 文档模型设计之二：工况细化
第二步:根据读写工况细化
![image](https://user-images.githubusercontent.com/34932312/73592339-233c6d00-4534-11ea-9907-be244821712e.png)

![image](https://user-images.githubusercontent.com/34932312/73592411-12402b80-4535-11ea-9c07-ff3593674dc0.png)

解决方案：Group使用单独的集合
![image](https://user-images.githubusercontent.com/34932312/73592506-381a0000-4536-11ea-9676-38787424fb1c.png)

![image](https://user-images.githubusercontent.com/34932312/73592592-03f30f00-4537-11ea-87c3-78c0bbe8bb7c.png)

![image](https://user-images.githubusercontent.com/34932312/73592615-356bda80-4537-11ea-95e6-aaaf1fc4e53e.png)

### 什么时候该使用引用方式？
- 内嵌文档太大，数MB或者超过16MB
- 内嵌文档或数组元素会频繁修改
- 内嵌数组元素会持续增长且没有封顶

![image](https://user-images.githubusercontent.com/34932312/73592699-d8245900-4537-11ea-97f1-cf29f8ff6262.png)

## 文档模型设计之三：模式套用
第三步:套用设计模式
![image](https://user-images.githubusercontent.com/34932312/73592738-436e2b00-4538-11ea-95b5-9f1ed6ad7992.png)

### 问题：物联网场景下的海量数据处理-飞机监控数据
```
{
    "_id" : "20160101050000:CA2790",
    "icao" : "CA2790",
    "callsign" : "CA2790",
    "ts" : ISODate("2016-01-01T05:00:00.000+0000"), 
    "events" : {
        "a" : 31418,
        "b" : 173,
        "p" : [115, -134],
        "s" : 91,
        "v" : 80
    } 
}
```

![image](https://user-images.githubusercontent.com/34932312/73592803-0c4c4980-4539-11ea-932c-20f0149e1ba5.png)

解决方案: 分桶设计

![image](https://user-images.githubusercontent.com/34932312/73592847-a57b6000-4539-11ea-8d74-f22aa8a2121a.png)
![image](https://user-images.githubusercontent.com/34932312/73592852-b3c97c00-4539-11ea-95e3-85785bab22fa.png)

### 模式小结：分桶
![image](https://user-images.githubusercontent.com/34932312/73592874-e3788400-4539-11ea-805a-0dad723c8874.png)
![image](https://user-images.githubusercontent.com/34932312/73592877-f723ea80-4539-11ea-98ab-8645e098ee88.png)

## 设计模式集锦
### 大文档，很多字段，很多索引
```
{
    title: "Dunkirk",
    ...
    release_USA: "2017/07/23",
    release_UK: "2017/08/01",
    release_France: "2017/08/01",
    release_Festival_San_Jose: "2017/07/22"
}
```

```
需要很多索引
{ release_USA: 1 }
{ release_UK: 1 }
{ release_France: 1 }
...
{ release_Festival_San_Jose: 1 }
...
```

索引在做更新维护的时候一条写操作会放大成十倍几十倍，完全取决于有多少个索引字段。索引提高查询性能，但也惩罚写性能。

解决方案：列转行
![image](https://user-images.githubusercontent.com/34932312/73602068-2f621200-45a8-11ea-9204-49c40cba5046.png)

模式小结: 列转行
- 场景：产品属性'color', 'size', 'dimensions', ...; 多语言(多国家)属性
- 痛点：文档中有很多类似的字段； 会用于组合查询搜索，需要见很多索引
- 设计模式方案及优点：转化为数组，一个索引解决所有查询问题


### 模型灵活了，如何管理文档不同版本? 
2019.01 版本1.0
```
{
    "_id" : ObjectId("5de26f197edd62c5d388babb"), 
    "name" : "TJ",
    "company" : "Tapdata",
}
```

2019.03 版本2.0
```
{
    "_id" : ObjectId("5de26f197edd62c5d388babb"), 
    "name" : "TJ",
    "company" : "Tapdata",
    “wechat”: “tjtang826”
}
```

解决方案: 增加一个版本字段

2019.01 版本1.0
```
{
    "_id" : ObjectId("5de26f197edd62c5d388babb"), 
    "name" : "TJ",
    "company" : "Tapdata",
}
```

2019.03 版本2.0
```
{
    "_id" : ObjectId("5de26f197edd62c5d388babb"), 
    "name" : "TJ",
    "company" : "Tapdata",
    “wechat”: “tjtang826”,
    “schema_version”: “2.0”
}
```

模式小结: 版本字段
![image](https://user-images.githubusercontent.com/34932312/73602209-fb3c2080-45aa-11ea-8fc9-e54e2464982b.png)

### 统计网页点击流量
![image](https://user-images.githubusercontent.com/34932312/73602218-3b9b9e80-45ab-11ea-831b-1fc4a2f530c3.png)

解决方案：用近似计算
![image](https://user-images.githubusercontent.com/34932312/73602236-7f8ea380-45ab-11ea-8fb0-df2d63a77e3c.png)

模式小结: 近似计算
![image](https://user-images.githubusercontent.com/34932312/73602257-bb296d80-45ab-11ea-9ff0-2dec842a6541.png)

### 业绩排名，游戏排名，商品统计等精确统计
热销榜:某个商品今天卖了多少，这个星期卖了多少，这个月卖了多少?

电影排行:观影者，场次统计
 
传统解决方案:通过聚合计算 

痛点:消耗资源多，聚合计算时间长

解决方案: 用预聚合字段
```
{
    product: ”Bike",
    sku: “abc123456”,
    quantitiy: 20394,
    daily_sales: 40,
    weekly_sales: 302,
    monthly_sales: 1419
}
```

```
db.inventory.update({_id:123},
    {$inc: {
        quantity: -1, 
        daily_sales: 1, 
        weekly_sales: 1， 
        monthly_sales: 1
        }
    }
}    
```

模式小结:预聚合
![image](https://user-images.githubusercontent.com/34932312/73602299-8bc73080-45ac-11ea-9d0e-05dfdc02a3ac.png)

## 事务开发：写操作事务
### 什么是writeConcern？
写入关注（writeConcern），写入关注指的是MongoDB连接报告写入操作成功时的写入保障等级，写入关注程度越强，保障等级越高。

writeConcern其概念是，写入关注程度较高时，MongoDB必须等到数据完全写入磁盘后才做出响应，当写入程度较低时，MongoDB会在成功调用写入的修改后就会做出响应。从这一句话我们可以分析出其优缺点：当写入关注程度高时，MongoDB必须等待一段时间才能响应，因此此类的关注等级适合那些数据非常重要，必须保证数据的正确性的数据，该类数据可以承受数据的时效慢的问题，比如关于金钱的修改操作等。而写入关注程度较低时，则是不关注写入操纵是否成功，直接返回结果，此类写入关注适合哪些要求时效性高，数据不太重要的操作，比如日志记录等等。
                  
writeConcern决定一个写操作落到多少个节点上才算成功。writeConcern的取值包括：
- 0：发起写操作，不关心是否成功
- 1：集群最大数据节点数：写操作需要被复制到指定节点数才算成功
- majority：写操作需要被复制到大多数节点上才算成功。
发起写操作的程序将阻塞到写操作到达指定的节点数为止。

MongoDB的写入关注等级表
- -1：忽略网络错误
- 0：不要求进行写入确认
- 1：要求进行写入确认
- 2：要求以写入到副本集的主服务器和一个备用服务器
- majority：要求已写入到副本集中的大多数服务器中

### 默认行为
3节点复制集不作任何特别设定（默认值）：
![image](https://user-images.githubusercontent.com/34932312/73602428-d2b62580-45ae-11ea-8f36-638403684e64.png)

三节点（Primary, Secondary1, Secondary2），应用程序客户端试图写入一条程序x=1，首先进到MongoDB的Primary节点，如果没有做任何设置writeconcern写一个节点，一旦写到primary就会返回表示成功，
甚至没有写到盘里，只是写到primary的内存里就会向客户端响应acknowledged，我已经成功提交客户端给的操作。
两条到Secondary1和Secondary2的虚线表明异步的把x=1数据复制到两个节点，同步的线程是后台线程完成的，并没有参与主的写入线程，所以当你写完的时候数据可能还没有进到secondary1和secondary2，这个时候
丢数据的场景是有可能发生的。写入Primary返回给客户端完成，这个时候主节点crash，导致Secondary1, Secondary2都没有时间把这条数据复制过来，接下来Secondary1, Secondary2会选一个新的主节点出来服务客户端，
在Secondary1, Secondary2上其实没有x=1这条数据，这种情况下就导致了所谓的丢数据。实际并没有丢，MongoDB会把它写入到rollback的备用文件里。

如何解决呢？只需要设置一下writeconcern！
使用w：majority
![image](https://user-images.githubusercontent.com/34932312/73602519-3ee55900-45b0-11ea-8f59-46a17c504858.png)


全面节点确认模式：
![image](https://user-images.githubusercontent.com/34932312/73618506-3f8af780-4663-11ea-9525-9d3fea2547cc.png)

### j:true
writeConcern可以决定写操作到达多少个节点才算成功，journal则定义如何才算成功。取值包括:
- true:写操作落到journal文件中才算成功;
- false:写操作到达内存即算作成功。
![image](https://user-images.githubusercontent.com/34932312/73618584-a4465200-4663-11ea-9fe2-62d6388b9206.png)

journal即日志，作用：给一个数据库节点在宕机的时候能够快速的恢复刚才的写操作，所以数据库一般都会先写日志文件，然后再写数据文件，MongoDB也有这样的日志文件。默认情况下，当一个操作进到primary内存时，其实并不会等写日志就返回了，如果写操作非常重要，一定要先落盘才返回，这种方式也可以通过writeConcern的机制来完成，
做法加j:true。这样x=1数据到了primary后，数据写到primary的日志文件，写完以后才返回成功，这是进一步增加数据安全的方法。

### writeConcern的意义
对于5个节点的复制集来说，写操作落到多少个节点上才算是安全的？
- 1
- 2
- 3
- 4
- 5
- majority

答案是：3，4，5，majority

### writeConcern实验
在复制集测试writeConcern参数
```
db.test.insert({count: 1}, {weiteConcern: {w: "majority"}})
db.test.insert({count: 1}, {weiteConcern: {w: 3}})
db.test.insert({count: 1}, {weiteConcern: {w: 4}})
```

```
搭建复制集
rs.status() >> members

db.test.insert({count: 1})
WriteResult({"nInserted": 1})

db.test.insert({count: 1}, {writeConcern: {w: 3}})
WriteResult({"nInserted": 1})

db.test.insert({count: 1}, {writeConcern: {w: 4}}) // 表示写到4个数据节点才给返回
WriteResult({
    "nInserted": 1,
    "writeConcernError": {
        "code": 100,
        "codeName": "UnsatisfiableWriteConcern",
        "errmsg": "not enough data-bearing nodes"
    }    
}）    

db.test.insert({count: 1}, {weiteConcern: {w: "majority"}})
WriteResult({"nInserted": 1})
```

配置延迟节点，模拟网络延迟（复制延迟）
```
conf=rs.conf()
conf.members[2].slaveDelay = 5
conf.members[2].priority = 0
rs.reconfig(conf)
```

```
把某一个节点设置为数据延迟节点
conf.members[2].slaveDelay = 10
conf.members[2].priority = 0 // 如果延迟节点不可以参与选举

db.test.insert({count: 2}, {writeConcern: {w: 3}})
过了10s, 写成功 
WriteResult({"nInserted": 1})
```

观察复制延迟下的写入，以及timeout参数
```
db.test.insert({count: 1}, {weiteConcern: {w: 3}})
db.test.insert({count: 1}, {weiteConcern: {w: 3, wtimeout: 3000}})
```

```
db.test.insert({count: 1}, {weiteConcern: {w: 3, wtimeout: 3000}}) 
WriteResult({
    "nInserted": 1,
    "writeConcernError": {
        "code": 64,
        "codeName": "writeConcernFailed",
        "errmsg": "waiting for replication timed out"
        "errInfo": {
            "wtimeout": true
        }
    }    
}）   
```

### 注意事项
- 虽然多于半数的writeConcern都是安全的，但通常只会设置majority，因为这是等待写入延迟时间最短的选择;
- 不要设置writeConcern等于总节点数，因为一旦有一个节点故障，所有写操作都将失败;
- writeConcern虽然会增加写操作延迟时间，但并不会显著增加集群压力，因此无论是否等待，写操作最终都会复制到所有节点上。设置 writeConcern 只是让写操作等待复制后再返回而已;
- 应对重要数据应用{w: "majority"}，普通数据可以应用{w:1}以确保最佳性能。

## 事务开发：读操作事务
### 综述
在读取数据的过程中我们需要关注以下两个问题：
- 从哪里读？关注数据节点位置
- 什么样的数据可以读？关注数据的隔离性

第一个问题是由readPreference来解决
第二个问题是由readConcern来解决

### readPreference
readPreference决定使用哪一个节点来满足正在发起的读请求。可选值包括:
- primary:只选择主节点
- primaryPreferred:优先选择主节点，如果不可用则选择从节点;
- secondary:只选择从节点;
- secondaryPreferred:优先选择从节点，如果从节点不可用则选择主节点;
- nearest:选择最近的节点;

### readPreference 场景举例
- 用户下订单后马上将用户转到订单详情页——primary/primaryPreferred。因为此时从节点可能还没复制到新订单;
- 用户查询自己下过的订单——secondary/secondaryPreferred。查询历史订单对时效性通常没有太高要求;
- 生成报表——secondary。报表对时效性要求不高，但资源需求大，可以在从节点单独处理，避免对线上用户造成影响;
- 将用户上传的图片分发到全世界，让各地用户能够就近读取——nearest。每个地区的应用选择最近的节点读取数据。


### readPreference 与 Tag
readPreference只能控制使用一类节点。Tag则可以将节点选择控制到一个或几个节点。

考虑以下场景:
- 一个5个节点的复制集;
- 3个节点硬件较好，专用于服务线上客户;
- 2个节点硬件较差，专用于生成报表; 

可以使用 Tag 来达到这样的控制目的:
- 为3个较好的节点打上{purpose:"online"};
- 为2个较差的节点打上{purpose:"analyse"};
- 在线应用读取时指定online，报表读取时指定reporting。

更多信息请参考文档: https://docs.mongodb.com/manual/core/read-preference/

![image](https://user-images.githubusercontent.com/34932312/73643010-b0570180-46ad-11ea-87c7-b7a0086f524c.png)

### readPreference 配置
通过 MongoDB 的连接串参数：
```
mongodb://host1:27107,host2:27107,host3:27017/?replicaSet=rs&readPreference=secondary
```

通过 MongoDB 驱动程序 API:
```
MongoCollection.withReadPreference(ReadPreferencereadPref)
```

Mongo Shell:
```
db.collection.find({}).readPref(“secondary”)
```

### readPreference实验: 从节点读
- 主节点写入{x:1},观察该条数据在各个节点均可见
- 在两个从节点分别执行 db.fsyncLock() 来锁定写入(同步) // 可以读不可以写，通过这种方式模拟同步被阻碍，或者网络延迟，一时半会数据改不来了
- 主节点写入{x:2}
    - db.test.find({a:123})
    - db.test.find({a:123}).readPref(“secondary”)  // 虽然连的是主节点，但是指定从从节点上读
- 解除从节点锁定db.fsyncUnlock()
    - db.test.find({a:123}).readPref(“secondary”)

### readPreference注意事项
- 指定readPreference时也应注意`高可用问题`。例如将readPreference指定primary，则发生故障转移不存在 primary 期间将没有节点可读。如果业务允许，则应选择 primaryPreferred
- 使用Tag时也会遇到同样的问题，如果只有一个节点拥有一个特定Tag，则在这个节点失效时将无节点可读。这在有时候是期望的结果，有时候不是。例如:
    - 如果报表使用的节点失效，即使不生成报表，通常也不希望将报表负载转移到其他节点上，此时只有一个节点有报表 Tag 是合理的选择;
    - 如果线上节点失效，通常希望有替代节点，所以应该保持多个节点有同样的Tag;
- Tag有时需要与优先级、选举权综合考虑。例如做报表的节点通常不会希望它成为主节点，则优先级应为 0。

## readConcern
### 什么是readConcern？
在readPreference选定了指定的节点后，readConcern决定这个节点上的数据哪些是可读的，类似于关系型数据库的隔离级别。可选值包括：
- available 读取所有可用的数据
- local 读取所有可用且属于当前分片的数据（默认值）
- majority 读取在大多数节点上提交完成的数据
- linearizable 可线性化读取文档
- snapshot 读取最近快照中的数据

### readConcern：local和available
在复制集中loacl和available是没有区别的。

两者的区别主要体现在分片集上。考虑以下场景chunk迁移数据迁移：
- 一个chunk x 正在从shard1向shard2迁移（MongoDB在数据自动均衡的时候会把一部分数据从一个分片搬到另外一个分片，目的是为了不同分片上的数据保持大致平衡的状态，这样子起到一个比较好的集群性能）
- 整个迁移过程中chunk x中的部分数据会在shard1和shard2中同时存在，但源分片shard1仍然是chunk x的负责方：
    - 所有对chunk x的读写操作仍然进入shard1；
    - config中记录的信息chunk x仍然属于shard1；
- 此时如果读shard2，则会体现出local和available的区别:
    - local:只取应该由shard2负责的数据(不包括x);
    - available:shard2上有什么就读什么(包括x);

![image](https://user-images.githubusercontent.com/34932312/73902118-bfb59500-48cf-11ea-8405-2535e1f5dbd7.png)

注意事项：
- 虽然看上去总是应该选择local，但毕竟对结果集进行过滤会造成额外消耗。在一些 无关紧要的场景(例如统计)下，也可以考虑 available;
- MongoDB<=3.6不支持对从节点使用{readConcern:"local"};
- 从主节点读取数据时默认readConcern是local，从从节点读取数据时默认 readConcern 是 available(向前兼容原因)。

### readConcern：majority
readConcern：majority  只读取大多数据节点上都提交了的数据
考虑如下场景: 集合中原有文档 {x: 0}; 将x值更新为 1;
![image](https://user-images.githubusercontent.com/34932312/73902382-a6f9af00-48d0-11ea-8c11-a9da9e33e165.png)

t0时间点把x=0更新成x=1，t1时间点数据复制到S1，t2到S2，t3时间点S1响应主节点已经拿到，t4 S2响应主节点，t5主节点告诉S1这条数据在我和Primary上都存在，t6知道在三个节点上全部写完。

如果在各节点上应用下场景: {readConcern: “majority”} 来读取数据:
![image](https://user-images.githubusercontent.com/34932312/73903041-cbef2180-48d2-11ea-9397-fbf42c3cd551.png)
t1时刻S1只知道自己有数据，t3时刻Primary知道自己和S1节点有x=1数据，

### readConcern：majority实现方式
考虑 t3 时刻的 Secondary1，此时:
- 对于要求majority的读操作，它将返回x=0;
- 对于不要求majority的读操作，它将返回x=1;

如何实现？
节点上维护多个x版本，MVCC机制
![image](https://user-images.githubusercontent.com/34932312/73903617-86335880-48d4-11ea-929c-10d8698dd2fa.png)

MongoDB 通过维护多个快照来链接不同的版本:
- 每个被大多数节点确认过的版本都将是一个快照;
- 快照持续到没有人使用为止才被删除;

### 实验：readConcern："majority" vs "local"
- 安装3节点复制集
- 注意配置文件内server参数enableMajorityReadConcern
```
replication：
    replSetName：rs0
    enableMajorityReadConcern：true
```
- 将复制集中的两个从节点使用db.fsyncLock()锁住写入（模拟同步延迟）

readConcern验证
```
db.test.save({“A”:1})
db.test.find().readConcern(“local”)
db.test.find().readConcern(“majority”)
在某一个从节点上执行db.fsyncUnlock()
```
结论: 
- 使用local参数，则可以直接查询到写入数据； 
- 使用majority，只能查询到已经被多数节点确认过的数据； 
- update与remove与上同理。

### readConcern：majority与脏读
MongoDB中回滚：
- 写操作到达大多数节点之前都是不安全的，一旦主节点奔溃，而从节点还没复制到该次操作，刚才的写操作就丢失了；
- 把一次写操作视为一个事务，从事务的角度，可以认为事务被回滚了。
所以从分布式系统的角度来看，事务的提交被提升到了分布式集群的多个节点级别的"提交"，而不是单个节点上的"提交"。

在可能发生回滚的前提下考虑脏读问题：
- 如果在一次写操作到达大多数节点前读取了这个写操作，然后因为系统故障该操作回滚了，则发生了脏读问题；

使用{readConcern: "majority"}可以有效避免脏读

### readConcern：如何实现安全的读写分离

考虑如下场景：（读写分离场景，主节点上用来做提交写入，从节点上用来读或做一些分析）向主节点写入一条数据；立即从从节点读取该条数据。如何保证自己能够读到刚刚写入的数据？

下述方式有可能读不到刚写入的订单
```
db.orders.insert({oid: 101, sku: "kite", q:1})
db.orders.find({oid: 101}).readPref("secondary")
```
使用writeConcern+readConcern majority来解决
```
db.orders.insert({oid: 101, sku:"kiteboar", q: 1}, {writeConcern: {w: "majority"}})
db.orders.find({oid: 101}).readPref("secondary").readConcern("majority")
```

### 小测试
readConcern主要关注读的隔离性，ACID中的Isolation，但是是分布式数据库里特有的概念。

readConcern：majority对应事务中隔离级别中的哪一级？  Read Committed

Read Uncommited、Read Committed、Repeatable Read、Serizable

### readConcern：linearizable
只读取大多数节点确认过的数据。和majority最大差别是保证绝对的操作线性顺序，在写操作自然时间后面的发生的读，一定可以读到之前的写
- 只对读取单个文档时有效；
- 可能导致非常慢的读，因此总是建议配合使用maxTimeMS；
![image](https://user-images.githubusercontent.com/34932312/74080657-655c3600-4a81-11ea-86dd-f2a5533d8825.png)

### readConcern：snapshot
{readConcern: "snapshot"}只在多文档事务中生效。将一个事务的readConcern设置为snapshot，将保证在事务中的读：
- 不出现脏读；
- 不出现不可重复读；
- 不出现幻读。
因为所有的读都将使用同一个快照，直到事务提交为止该快照才被释放。

参考博客：https://blog.csdn.net/slqgenius/article/details/83577020

## 事务开发：多文档事务
MongoDB 虽然已经在4.2开始全面支持了多文档事务，但并不代表大家应该毫无节制地使用它。相反，对事务的使用原则应该是:能不用尽量不用。
通过合理地设计文档模型，可以规避绝大部分使用事务的必要性 为什么? 事务=锁，节点协调，额外开销，性能影响

### MongoDB ACID 多文档事务支持
Atomocity原子性   单表单文档：1.x就支持 复制集多表多行：4.0复制集 分片集群多表多行4.2
- 单表单文档指一个文档里面几十个字段，在更新这里面几十个字段的时候不会看到部分的现象，要么看到几十个字段集体被更新被写入，要么一个都看不到
- 多行多文档多表 比如写订单表、发邮件、扣款，里面会涉及到三张表，或者我要做个批量的更新，4.0中开始支持，4.0只支持复制集还不跨分片，4.2支持分片，完整的ACID多文档事务

Consistency 一致性 writeConcern，readConcern（3.2）

Isolation 隔离性 readConcern(3.2)

Durability 持久性 Journal and Replication

### 使用方法
MongoDB多文档事务的使用方式与关系数据库非常相似：
```
try (ClientSession clientSession = client.startSession()) {  //client是MongoDB连接的一个句柄
    clientSession.startTransaction();  //start
    collection.insertOne(clientSession, docOne);
    collection.insertOne(clientSession, docTwo);
    clientSession.commitTransaction(); // commit
}
```

### 事务的隔离级别
- 事务完成之前，事务外的操作对该事务所做的修改不可访问
- 如果事务内使用{readConcern："snapshot"}, 则可以达到可重复读Repeatable Read

实验：启动事务后的隔离性
```
db.tx.insertMany([{x: 1}, {x: 2}]);
var session = db.getMongo().startSession();
session.startTransaction();
var coll = session.getDatabase("test").getCollection("tx");
coll.updateOne({x:1}, {$set: {y:1}});   // 事务内的操作{x:1, y:1}
coll.findOne({x:1});        // 事务内的操作{x:1, y:1}
db.tx.findOne({x:1});       // 事务外的操作{x:1}
session.abortTrasaction();
```

实验: 可重复读Repeatable Read
```
var session = db.getMongo().startSession();
session.startTransaction({
    readConcern:{level: "snapshot"},
    writeConcern: {w: "majority"}    
});
var coll = session.getDatabase("test").getCollection("tx");

coll.findOne({x:1}) // 返回{x:1}
db.tx.updateOne({x: 1}, {$set: {y:1}});
db.tx.findOne({x:1}); // 返回{x:1, y:1}
coll.findOne({x:1}); // 返回{x:1}
session.abortTransaction();
```

### 事务写机制
MongoDB的事务错误处理机制不同于关系数据库:
- 当一个事务开始后，如果事务要修改的文档在事务外部被修改过，则事务修改这个文档时会触发 Abort 错误，因为此时的修改冲突了;
- 这种情况下，只需要简单地重做事务就可以了;
- 如果一个事务已经开始修改一个文档，在事务以外尝试修改同一个文档，则事务以 外的修改会等待事务完成才能继续进行(write-wait.md实验)。

实验:写冲突
```
继续使用上个实验的tx集合
开两个 mongo shell 均执行下述语句
var session = db.getMongo().startSession(); 
session.startTransaction({ readConcern: {level: "snapshot"}, writeConcern: {w: "majority"}});
var coll = session.getDatabase('test').getCollection("tx");
```
窗口一：coll.updateOne({x:1}, {$set: {y:1}}); 正常结束

窗口二：coll.updateOne({x:1}, {$set: {y:2}}); 异常 – 解决方案:重启事务

### 注意事项
- 可以实现和关系型数据库类似的事务场景，必须使用与MongoDB4.2兼容的驱动;
- 事务默认必须在60秒(可调)内完成，否则将被取消;
- 涉及事务的分片不能使用仲裁节点;
- 事务会影响chunk迁移效率。正在迁移的chunk也可能造成事务提交失败(重试即可);
- 多文档事务中的读操作必须使用主节点读;
- readConcern只应该在事务级别设置，不能设置在每次读写操作上。

## Change Stream
### 什么是Change Stream？
Change Stream是MongoDB用于实现变更追踪的解决方案，类似于关系数据库的触发器，但原理不完全相同：
![image](https://user-images.githubusercontent.com/34932312/74094682-1ca78a00-4b20-11ea-8720-70beb0361eb3.png)

触发方式在关系型数据库中是一种同步触发的机制，即处理逻辑和原先的数据库操作在一个事务里完成的，比如根据某一条插入往另外一个表中进行一条插入。MongoDB的Change Stream是异步操作，在原先的数据库操作完成之后才会调用下一个事件。

### Change Stream的实现原理
Change Stream 是基于 oplog 实现的。它在 oplog 上开启一个 tailable cursor 来追踪所有复制集上的变更操作，最终调用应用中定义的回调函数。 被追踪的变更事件主要包括: 
- insert/update/delete:插入、更新、删除; 
- drop:集合被删除; 
- rename:集合被重命名; 
- dropDatabase:数据库被删除; 
- invalidate:drop/rename/dropDatabase将导致invalidate被触发， 并关闭 change stream; 

### Change Stream与可重复读
Change Stream 只推送已经在大多数节点上提交的变更操作。即“可重复读”的变更。这个验证是通过 {readConcern: “majority”} 实现的。因此:
- 未开启majorityreadConcern的集群无法使用ChangeStream;
- 当集群无法满足{w:“majority”}时，不会触发ChangeStream(例如PSA架构 中的 S 因故障宕机)。

### Change Stream 变更过滤
如果只对某些类型的变更事件感兴趣，可以使用使用聚合管道的过滤步骤过滤事件。比如只对插入和删除感兴趣，对更新不感兴趣，更新的只是个计数counter，对我来讲没有实质上的意义。
```
var cs = db.collection.watch([{
    $match: {  // match就是一个过滤器，只满足这个条件的事件才推送
        operationType: {
            $in: ['insert', 'delete']  // 只对insert、delete这两种操作进行一些处理
        }
    }    
}])    
```

### Change Stream示例
```
> db.collection.watch([],  // 第一个参数是一个过滤器，可以选择是否需要过滤还是所有事件
{maxAwaitTimeMS: 30000}).pretty()  //maxAwaitTimeMS: 多长时间没有事件可以退出
```

```
> db.collection.insert({
    _id: 1,
    text: "hello"
  })
```

结果：
```
{
    _id : (resumeToken), 
    operationType : "insert", ...
    fullDocument : {
        _id : 1,
        text: "hello" 
    }
}
```

### Change Stream 故障恢复
假设在一系列写入操作的过程中，订阅 Change Stream 的应用在接收到“写3”之后于 t0 时刻崩溃，重启后后续的变更怎么办?
![image](https://user-images.githubusercontent.com/34932312/74095447-ecfe7f00-4b2b-11ea-92b3-1d41cd456d01.png)

![image](https://user-images.githubusercontent.com/34932312/74095458-3058ed80-4b2c-11ea-8944-fe94bbae0de1.png)

### Change Stream 使用场景
- 跨集群的变更复制——在源集群中订阅ChangeStream，一旦得到任何变更立即写入目标集群。
- 微服务联动——当一个微服务变更数据库时，其他微服务得到通知并做出相应的变更。
- 其他任何需要系统联动的场景。

### 注意事项
- ChangeStream依赖于oplog，因此中断时间不可超过oplog回收的最大时间窗; 
- 在执行update操作时，如果只更新了部分数据，那么ChangeStream通知的也是增量部分;
- 同理，删除数据时通知的仅是删除数据的_id。

## MongoDB开发最佳实践










