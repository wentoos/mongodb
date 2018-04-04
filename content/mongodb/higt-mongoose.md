---
title: MONGOOSE 高阶技巧
---

# 高级查询


## 条件查询

常用操作符，通过操作符的使用，我们就可对数据进行更细致性的查询，一起来看一下吧。

1. "$lt" (小于)
2. "$lte" (小于等于)
3. "$gt" (大于)
4. "$gte" (大于等于)
5. "$ne" (不等于)
6. "$in" (可单值和多个值的匹配)
7. "$or" (查询多个键值的任意给定值)
8. "$exists"(表示是否存在的意思)

---

## $gt、$lt简述

查询时我们经常会碰到要根据某些字段进行条件筛选查询，比如说Number类型，怎么办呢，我们就可以使用$gt(>)、$lt(<)、$lte(<=)、$gte(>=)操作符进行排除性的查询，如下示例：

```
Model.find({"age":{"$gt":18}},function(error,docs){
   //查询所有nage大于18的数据
});
 
Model.find({"age":{"$lt":60}},function(error,docs){
   //查询所有nage小于60的数据
});
 
Model.find({"age":{"$gt":18,"$lt":60}},function(error,docs){
   //查询所有nage大于18小于60的数据
});
```

## $ne $in简述

$ne(!=)操作符的含义相当于不等于、不包含，查询时我们可通过它进行条件判定，具体使用方法如下：

```
Model.find({ age:{ $ne:24}},function(error,docs){
    //查询age不等于24的所有数据
});
 
Model.find({name:{$ne:"tom"},age:{$gte:18}},function(error,docs){
  //查询name不等于tom、age>=18的所有数据
});
```

$ne可以匹配单个值，也可以匹配不同类型的值。

和$ne操作符相反，$in相当于包含、等于，查询时查找包含于指定字段条件的数据。具体使用方法如下：

```
Model.find({ age:{ $in: 20}},function(error,docs){
   //查询age等于20的所有数据
});
 
Model.find({ age:{$in:[20,30]}},function(error,docs){
  //可以把多个值组织成一个数组
}); 
```

## $or简述

$or操作符，可以查询多个键值的任意给定值，只要满足其中一个就可返回，用于存在多个条件判定的情况下使用，如下示例：

```
Model.find({"$or":[{"name":"yaya"},{"age":28}]},function(error,docs){
  //查询name为yaya或age为28的全部文档
});
```

---


## $exists简述

$exists操作符，可用于判断某些关键字段是否存在来进行条件查询。如下示例：

```
Model.find({name: {$exists: true}},function(error,docs){
  //查询所有存在name属性的文档
});
Model.find({telephone: {$exists: false}},function(error,docs){
  //查询所有不存在telephone属性的文档
});
```

*简单说明*

　　$gt(>),$lt(<),$lte(<=),$gte(>=)操作符：针对Number类型的查询具体超强的排除性。

　　$ne(!=)操作符：相当于不等于、不包含，查询时可根据单个或多个属性进行结果排除。

　　$in操作符：和$ne操作符用法相同，但意义相反。

　　$or操作符：可查询多个条件，只要满足其中一个就可返回结果值。

　　$exists操作符：主要用于判断某些属性是否存在。

---

# 游标 (分页)

## 简单介绍

　数据库使用游标返回find的执行结果。客户端对游标的实现通常能够对最终结果进行有效的控制。可以限制结果的数量，略过部分结果，根据任意键按任意顺序的组合对结果进行各种排序，或者是执行其他一些强的操作。

　　最常用的查询选项就是限制返回结果的数量(limit函数)、忽略一点数量的结果(skip函数)以及排序(sort函数)。所有这些选项一点要在查询被发送到服务器之前指定。



### limit函数的基本用法

在查询操作中，有时数据量会很大，这时我们就需要对返回结果的数量进行限制，那么我们就可以使用limit函数，通过它来限制结果数量。

+ 限制数量：find(Conditions,fields,options,callback);

```
Model.find({},null,{limit:20},function(err,docs){
    console.log(docs);
});
```

_注:_  如果匹配的结果不到20个，则返回匹配数量的结果，也就是说limit函数指定的是上限而非下限。


 
### skip函数的基本用法

skip函数和limit类似，都是对返回结果数量进行操作，不同的是skip函数的功能是略过指定数量的匹配结果，返回余下的查询结果。如下示例：

+ 跳过数量：find(Conditions,fields,options,callback);

```
Model.find({},null,{skip:4},function(err,docs){
    console.log(docs);
});
```

_注:_  如果查询结果数量中少于4个的话，则不会返回任何结果。


### sort函数的基本用法

sort函数可以将查询结果数据进行排序操作，该函数的参数是一个或多个键/值对，键代表要排序的键名，值代表排序的方向，1是升序，-1是降序。

+ 结果排序：find(Conditions,fields,options,callback);

```
Model.find({},null,{sort:{age:-1}},function(err,docs){
  //查询所有数据，并按照age降序顺序返回数据docs
});
```

_注:_  sort函数可根据用户自定义条件有选择性的来进行排序显示数据结果。


**简单回顾：**

　　limit函数：限制返回结果的数量。

　　skip函数：略过指定的返回结果数量。

　　sort函数：对返回结果进行有效排序。

　　---



## 属性方法

### ObjectId


存储在mongodb集合中的每个文档（document）都有一个默认的主键_id，这个主键名称是固定的，它可以是mongodb支持的任何数据类型，默认是ObjectId。该类型的值由系统自己生成，从某种意义上几乎不会重复，生成过程比较复杂

使用过MySQL等关系型数据库的友友们都知道，主键都是设置成自增的。但在分布式环境下，这种方法就不可行了，会产生冲突。为此，MongoDB采用了一个称之为ObjectId的类型来做主键。ObjectId是一个12字节的 BSON 类型字符串。按照字节顺序，一次代表：

4字节：UNIX时间戳
3字节：表示运行MongoDB的机器
2字节：表示生成此_id的进程
3字节：由一个随机数开始的计数器生成的值

```
var mongoose = require('mongoose');
var tSchema = new mongoose.Schema({}); //默认_id:ObjectId类型
```

每一个文档都有一个特殊的键“_id”，这个键在文档所属的集合中是唯一的。



### Schema添加属性值

前面已经讲述了如何定义一个Schema并赋予某些属性值,那能不能先定义后添加属性呢，答案是可以的，如下所示：

```
var mongoose = require('mongoose');
var TempSchema = new mongoose.Schema;
TempSchema.add({ name: 'String', email: 'String', age: 'Number' });
```

_个人觉得这样做的好处就是多封装继承 de 实例方法吧，可以写公用方法_

### 实例方法

有的时候，我们创造的Schema不仅要为后面的Model和Entity提供公共的属性，还要提供公共的方法.那怎么在Schema下创建一个实例方法呢，请看示例：

```
var mongoose = require('mongoose');

var saySchema = new mongoose.Schema({name : String});

saySchema.method('say', function () {

  console.log('Trouble Is A Friend');

})
var say = mongoose.model('say', saySchema);

var lenka = new say();

lenka.say(); //Trouble Is A Friend
```

### Schema静态方法

前面课程我们讲述了如何为Schema创建实例方法，接下来将讲述怎么为Schema创建静态方法。如下示例：

```
var mongoose = require("mongoose");
var db = mongoose.connect("mongodb://127.0.0.1:27017/test");
var TestSchema = new mongoose.Schema({
    name : { type:String },
    age  : { type:Number, default:0 },
    email: { type:String, default:"" },
    time : { type:Date, default:Date.now }
});
 
TestSchema.static('findByName', function (name, callback) {
    return this.find({ name: name }, callback);
});
 
var TestModel = db.model("test1", TestSchema );
TestModel.findByName('tom', function (err, docs) {
 //docs所有名字叫tom的文档结果集
});

```

### Schema追加方法

关于Schema的如何定义前面我们已经讲述过了，有时场景的需要，我们甚至可以为Schema模型追加方法。

为Schema模型追加speak方法，如下示例：

```
var mongoose = require("mongoose");
var db = mongoose.connect("mongodb://127.0.0.1:27017/test");
var TestSchema = new mongoose.Schema({
    name : { type:String },
    age  : { type:Number, default:0 },
    email: { type:String, default:"" },
    time : { type:Date, default:Date.now }
});
 
TestSchema.methods.speak = function(){
  console.log('我的名字叫'+this.name);
}
 
var TestModel = db.model('test1',TestSchema);
var TestEntity = new TestModel({name:'Lenka'});
TestEntity.speak();//我的名字叫Lenka

```