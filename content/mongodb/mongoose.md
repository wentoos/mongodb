---
title: MONGOOSE 简介
---
# Mongoose

### Mongoose是什么？

Mongoose是MongoDB的一个对象模型工具，是基于node-mongodb-native开发的MongoDB nodejs驱动，可以在异步的环境下执行。同时它也是针对MongoDB操作的一个对象模型库，封装了MongoDB对文档的的一些增删改查等常用方法，让NodeJS操作Mongodb数据库变得更加灵活简单。

### Mongoose能做什么？

Mongoose，因为封装了对MongoDB对文档操作的常用处理方法，让NodeJS操作Mongodb数据库变得easy、easy、So easy!

学习了上面的介绍，相信你已经对Mongoose有了初步的认识和了解，千里之行，始于足下，奔跑吧，少年！

<!-- toc -->

# Mongoose and MongoDB

## 连接MongoDB

### 安装驱动
既然是驱动程序就需要安装插件
```
npm i --save mongoose
```
* 在项目中引入 mongoose 驱动

```
const mongoose = require('mongoose')
```
* 连接 mongodb

```
mongoose.connect('mongodb://username:password@localhost:9098/data-api')
```
* 检测是否连接成功

```
const db = mongoose.connection;
db.on('error',console.log)
db.once('open',()=>{
    console.log("------数据库连接成功！------");
})
```

## 集合

### 啥是集合

首先，我们再次简单介绍一下MongoDB数据库。

　　MongoDB —— 是一个对象数据库，没有表、行等概念，也没有固定的模式和结构，所有的数据以Document(以下简称文档)的形式存储(Document，就是一个关联数组式的对象，它的内部由属性组成，一个属性对应的值可能是一个数、字符串、日期、数组，甚至是一个嵌套的文档。)，后面我们会学习如何创建文档并插入内容。

　　在MongoDB中，多个Document可以组成Collection(以下简称集合)，多个集合又可以组成数据库。

　　我们想要操作MongoDB数据，那就得先要具备上面所说的包含数据的“文档”，文档又是什么意思呢，请看如下介绍。

　　文档 —— 是MongoDB的核心概念，是键值对的一个有序集，在JavaScript里文档被表示成对象。同时它也是MongoDB中数据的基本单元，非常类似于关系型数据库管理系统中的行，但更具表现力。

　　集合 —— 由一组文档组成，如果将MongoDB中的一个文档比喻成关系型数据库中的一行，那么一个集合就相当于一张表。

　　如果我们要通过Mongoose去创建一个“集合”并对其进行增删改查，该怎么实现呢，到这里我们就要先了解Schema(数据属性模型)、Model、Entity了。

### Schema简述


**Schema** —— 一种以文件形式存储的数据库模型骨架，无法直接通往数据库端，也就是说它不具备对数据库的操作能力，仅仅只是数据库模型在程序片段中的一种表现，可以说是数据属性模型(传统意义的表结构)，又或着是“集合”的模型骨架。

　　那如何去定义一个Schema呢，请看示例：

```
var mongoose = require("mongoose");
var TestSchema = new mongoose.Schema({

    name : { type:String }, //属性name,类型为String
    age  : { type:Number, default:0 }, //属性age,类型为Number,默认为0
    time : { type:Date, default:Date.now },
    email: { type:String,default:''}

})
```

基本属性类型有：字符串、日期型、数值型、布尔型(Boolean)、null、数组、内嵌文档等。

简单得说 **Schema** 设置了document的结构，不能与数据库进行通信。

### Model 简介

Model —— 由Schema构造生成的模型，除了Schema定义的数据库骨架以外，还具有数据库操作的行为，类似于管理数据库属性、行为的类。

如何通过Schema来创建Model呢，如下示例：

```
    //...
var TestModel = mongoose.model("test", TestSchema);

```

__test:__ 数据库中的集合名称,当我们对其添加数据时如果test已经存在，则会保存到其目录下，如果未存在，则会创建test集合，然后在保存数据。

　　拥有了Model，我们也就拥有了操作数据库的金钥匙，使用Model来进行增删改查的具体操作，所以，一定要熟悉他的创建格式哟！

__Model__ 有直接操作数据库的能力

```
    TestModel.create({name:'haha'},(err,doc)=>{
        if err console.log(err)
        else console.log(doc)
    })
```
<!-- 虽然Model有保存数据的能力，但感觉很怪，不是吗？ -->


### entity

__Entity简述__

Entity —— 由Model创建的实体，使用save方法保存数据，Model和Entity都有能影响数据库的操作，但Model比Entity更具操作性。

使用Model创建Entity，如下示例：

```
var TestEntity = new TestModel({
       name : "Lenka",
       age  : 36,
       email: "lenka@qq.com"
});
```

创建成功之后，Schema属性就变成了Model和Entity的公共属性了。

__保存 Entity__

```
Entity.save(function(error,doc) {
    if(error) {
        console.log(error);
    } else {
        console.log(doc);
    }
})
```

_值得注意的是，model调用的是create方法，entity调用的是save方法。_


### 更新数据

学习了数据的保存，接下来我们就开始学习对数据的更新吧！

+ 示例：obj.update(查询条件,更新对象,callback);

```
var conditions = {name : 'test_update'};
var update = {$set : { age : 16 }};
TestModel.update(conditions, update, function(error){
    if(error) {
        console.log(error);
    } else {
        console.log('Update success!');
    }
})
```

更新后find一下，此时数据已经修改成功了！

###删除数据

有了数据的保存、更新，就差删除了，下面我们就来学习它吧！

+ 示例：obj.remove(查询条件,callback);

```
var conditions = { name: 'tom' };\
TestModel.remove(conditions, function(error){
    if(error) {
        console.log(error);
    } else {
        console.log('Delete success!');
    }
})
```

和update类似吧，有了remove方法我们就可以进行删除操作了

### 数据查询

查询就是返回一个集合中的文档的子集，Mongoose 模型提供了find、findOne、和findById方法用于文档查询。

#### 简单查询

##### _find 查询_

    find查询： obj.find(查询条件,callback);

```
Model.find({},function(error,docs){
   //若没有向find传递参数，默认的是显示所有文档
});
 
Model.find({ "age": 28 }, function (error, docs) {
  if(error){
    console.log("error :" + error);
  }else{
    console.log(docs); //docs: age为28的所有文档
  }
}); 
```

##### _find 过滤查询_

    属性过滤 find(Conditions,field,callback);

    field省略或为Null，则返回所有属性。

```
//返回只包含一个键值name、age的所有记录
Model.find({},{name:1, age:1, _id:0}，function(err,docs){
   //docs 查询结果集
})

```

*说明：*我们只需要把显示的属性设置为大于零的数就可以，当然1是最好理解的，`_id`是默认返回，如果不要显示加上`("_id":0)`，但是，对其他不需要显示的属性且不是`_id`，如果设置为0的话将会抛异常或查询无果。

##### _findOne查询_

　　与find相同，但只返回单个文档，也就说当查询到即一个符合条件的数据时，将停止继续查询，并返回查询结果。

    1.单条数据 findOne(Conditions,callback);

```
TestModel.findOne({ age: 27}, function (err, doc){
   // 查询符合age等于27的第一条数据
   // doc是查询结果
});
```
findOne方法，只返回第一个符合条件的文档数据。

##### _findById查询_

    与findOne相同，但它只接收文档的_id作为参数，返回单个文档。

    1.单条数据 findById(_id, callback);

```
TestModel.findById('obj._id', function (err, doc){
 //doc 查询结果文档
}); 
```  
同样是单条数据，findById和findOne还是有些区别的。