---
layout: post
title: MongoDB
date: '2015-12-02 00:24'
published: false
---

# 简介
[MongoDB](https://www.mongodb.org/)是一个基于分布式文件存储的NoSQL(Not only SQL)数据库，由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。文档存储一般以JSON形式存储，存储的内容为文档型。这样也就有有机会对某些字段建立索引，实现关系数据库的某些功能。

**基本概念**

在MongoDB中，collection对应Table，document相当于Row。

# 数据库 Database
**连接**

一个标准的MongoDB连接字符串是这样的：

```
mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]
```

```
> mongod
```

用来启动一个本地的MongoDB服务。

```
> mongo
```

连接本地MongoDB,连接成功后，使用 **help** 指令显示帮助文档。

```
> help
```

![image](/assets/postimages/db-cmd-help.png)

下面我们创建一个数据库

```
> use newdb
> db
// newdb
```

当使用 `use` 指定一个数据库，不存在时将自动新建，`db` 用来显示当前数据库。

删除数据库时，使用

```
dbname.dropDatabase()
```

# 集合 Collection
集合就是一组文档的组合。如果将文档类比成数据库中的行，那么集合就可以类比成数据库的表。当集合中第一条文档插入时，集合将自动创建。集合中存储的文档的数据结构可以是不同的，因为它是无模式的。

```
{"name":"a"}
{"Name":"a","sex":"male"}
```

集合必须满足以下几点：
1. 集合名不能是空字符串""。
2. 集合名不能含有\0字符（空字符)，这个字符表示集合名的结尾。
3. 集合名不能以"system."开头，这是为系统集合保留的前缀。
4. 用户创建的集合名字不能含有保留字符。有些驱动程序的确支持在集合名里面包含，这是因为某些系统生成的集合中包含该字符。除非你要访问这种系统创建的集合，否则千万不要在名字里出现$。　

# 文档 Document
文档是MongoDB中的最核心的概念，是其核心单元，我们可以将文档类比成关系型数据库中的每一行数据。 多个键及其关联的值有序的放置在一起就是文档。MongoDB使用了BSON这种结构来存储数据和网络数据交换。 BSON数据可以理解为在JSON的基础上添加了一些JSON中没有的数据类型。

文档必须满足以下几点：
1. 文档中的键/值对是有序的。
2. 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。
3. MongoDB区分类型和大小写。
4. MongoDB的文档不能有重复的键。
5. 文档的键是字符串。除了少数例外情况，键可以使用任意UTF-8字符。

文档键命名规范：
- 键不能含有\0 (空字符)。这个字符用来表示键的结尾。
- .和$有特别的意义，只有在特定环境下才能使用。
- 以下划线"_"开头的键是保留的(不是严格要求的)。

**操作符**

在MongoDB中进行查询，更新操作时，可以使用操作符进行快速的定位数据。

常用操作符有：
1. $gt 大于
2. $lt 小于
3. $gte 大于等于
4. $lte 小于等于

**插入文档**

```
db.collectionname.insert({key:value,key1:[val1,val2,val3]})
```

**删除文档**

清空集合：

```
db.collectionname.remove({})
```

删除符合条件的数据：

```
db.collectionname.remove({key:value})
```

删除符合大于（或者其他比较条件）的数据时，我们可以这样做：

```
db.collectionname.remove({
  age:{$lt:200}
})
```

**更新文档**

```
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,        //更新对象不存在时是否自动创建
     multi: <boolean>,         //是否更新所有匹配对象
     writeConcern: <document>  //抛出异常的级别
   }
)
```

**查询文档**

```
db.collectionname.find().pretty()
```

在MongoDB中使用使用sort()方法对数据进行排序，sort()方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排序，而-1是用于降序排列。

```
db.collectionname.find().sort({key:[1 | -1 ]})
```

**复合查询**

$and

查询年龄大于30岁并且名字为bushuai的数据

```
db.person.find({$and:[
  {
    age:{$gt:30}
  },
  {
    name:'bushuai'
  }
]});
```

$or

查询年龄大于30岁或者名字为bushuai的数据

```
db.person.find({$or:[
  {
    age:{$gt:30}
  },
  {
    name:'bushuai'
  }
]});
```

**限制条件 - 返回条数**

查询文档时，可以通过 `limit(NUMBER)` 指定返回条数，使用 `skip(NUMBER)` 可返回跳过 `NUMBER` 条数据的结果。

```
db.collectionname.find().limit(2).skip(2)
//返回从第三条开始的两条数据。
```

**限制条件 - 结果集**

需要输出结果集中指定的字段时，可以使用 `db.collectionname.find(查询条件，字段条件)`，下面的语句标识查询`person`表中名字为`bushuai`的文档，只输出`age`和`addr`字段。

```
db.person.find({name:'bushuai'},{age:1,addr:1})
```

**元数据**

`db.system.*` 下包含多种系统信息的特殊集合。

# 索引
索引通常能够极大的提高查询的效率，如果没有索引，MongoDB在读取数据时必须扫描集合中的每个文件并选取那些符合查询条件的记录。

**创建索引**

```
db.collectionname.ensureIndex(({key:[1 | -1]})+)
```

MongoDB中聚合主要用于处理数据(诸如统计平均值,求和等)，并返回计算后的数据结果。有点类似sql语句中的 count(_)。在MongoDB中使用 _*aggreate()__

**游标 Cursor**

`find` 指令不直接返回结果，而是返回一个结果集的迭代器，即游标。

如果我们想要获取数据，可以使用 `next` 方法遍历游标，如：

```
var icursor = db.person.find({
  age:{
    $lt:30
  }});
  var doc = icursor.hasNext()?icursor.next():null;
  if(doc){
    var item = doc.item;
    conosle.log(item);
  }
```

也可以使用`forEach` 进行遍历

```
var icursor = db.person.find();
icursor.forEach(function(doc){
  console.log(doc.item);
  });
```
