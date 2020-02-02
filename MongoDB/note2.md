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
![image](https://user-images.githubusercontent.com/34932312/73602519-3ee55900-45b0-11ea-8f59-46a17c504858.png)






