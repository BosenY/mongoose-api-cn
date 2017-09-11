# Schemas（架构）

定义：

Mongoose中的一切都是从schema开始的，每个schema映射了一个MongoDB集合并且定义了它的数据文档实例的形状。

在关系型数据库当中，你可以将schema理解成一张表，一个collocation。

```js
let mongoose = require('mongoose');
let Schema = mongoose.Schema;
let blogSchema = new Schema({
  title:  String,
  author: String,
  body:   String,
  comments: [{ body: String, date: Date }],
  date: { type: Date, default: Date.now },
  hidden: Boolean,
  meta: {
    votes: Number,
    favs:  Number
  }
});
```

这里定义了一个博客的schema，它的数据结构如上面所示，有标题、作者、内容、评论、日期、隐藏以及标签等。

每一个键都代表了一个属性，这些属性会被最终转化为相应的SchemaType。例如title会被转化为String SchemaType，而date会被转化为Date SchemaType，键值也可以像对象一个被嵌套，就像上面定义的meta属性。

这里我们允许定义的SchemaType有：

* String
* Number
* Date
* Buffer
* Boolean
* Mixed
* ObjectId
* Array

如果我们还想添加其他的key，那么可以去使用Schema的add方法。

```js
blogSchema.add（{
    commentNum: Number,
    readNum: Number
}）
```

这里我们给上面的博客的schema添加了评论总数和阅读总数两个key，具体的schema上面的add方法是如何实现的，请自己去看官方的文档上面的[add方法](http://www.nodeclass.com/api/mongoose.html#schema_Schema-add)。

## 创建model\(模型\) {#create-model}

schema只是定义了一种数据结构，具体的表名还有增删查改需要一个model来实现

要使用我们的schemas来定义，需要我们自己去把blogSchema转化为一个我们可以使用的model，为此，我们使用mongoose.model\(modelName, schema\)来定义一个blogSchema的实例：

```js
let Blog = mongoose.model('Blog', blogSchema) //Blog就是表名
// ready to go!
```

## 实例方法

一个model\(模型\)的实例就是一个document\(文件\)，文件有许多内置方法，当然我们也可以自己去写一些自定义方法

```js
//定义一个schema
let animalSchema = new mongoose.Schema({name: String, type: String})
//为schema添加一个自定义方法
animalSchema.methods.findSimlarTypes = (cb) => {
    return this.model('Animal').find({type: this.type}, cb)
}
```

现在我们所有定义的animalmode实例都可以使用这个自定义方法：

```js
let Animal = mongoose.model('Animal', animalSchema)
let dog = new Animal({ type: 'dog' })
dog.findSimilarTypes( (err, dogs) => {
  console.log(dogs); // dog
});
```



