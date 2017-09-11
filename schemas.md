# Schemas\(架构\)

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

最好不要去覆盖默认方法，否则会发生不可预知的错误。

## static\(静态\)

为模型添加静态方法也是很简单的：

```js
// assign a function to the "statics" object of our animalSchema
animalSchema.statics.findByName = (name, cb) => {
  this.find({ name: new RegExp(name, 'i') }, cb);
}
let Animal = mongoose.model('Animal', animalSchema);
Animal.findByName('fido', (err, animals) => {
  console.log(animals); //'fido'
});
```

## Index\(索引\)

MongoDB是支持二级索引的，使用了mongoose，我们可以在schema中定义索引的层级，创建复合索引时，我们必须去定义索引的层级。

```js
let animalSchema = new Schema({
  name: String,
  type: String,
  tags: { type: [String], index: true } // field level
});
animalSchema.index({ name: 1, type: -1 }); // schema level
```

在应用启动的时候，Mongoose会自动去调用ensureIndex模式中的每个索引。虽然这样在开发过程当中很好，但是在生产环境中建议禁用这种行为，因为创建索引可能会导致性能的重大影响。 可以通过设置autoIndex模式的选项来禁用改行为：

```js
animalSchema.set('autoIndex', false)
// or
new Schema({..}, { autoIndex: false })
```

## virtuals\(虚拟属性\)

virtuals是你可以用来获取和设置的文档属性，但是不能保存到MongoDB。可用于格式化或者组合字段，在把单个值设置为多个值的时候很有用处。

```js
// define a schema
let personSchema = new Schema({
  name: {
    first: String,
    last: String
  }
})

// compile our model
let Person = mongoose.model('Person', personSchema)

// create a document
let bad = new Person({
    name: { first: 'Walter', last: 'White' }
})
```

假设我们想打印bad的全名。我们可以这样做:

```js
console.log(bad.name.first + ' ' + bad.name.last); // Walter White
```

或者我们可以在personschema定义[虚拟属性的getter](http://mongoosejs.com/docs/api.html#virtualtype_VirtualType-get)，这样我们不需要每次写出这个字符串的拼接：

```js
personSchema.virtual('name.full').get( () => {
  return this.name.first + ' ' + this.name.last
})
```

现在，我们进入我们的虚拟”的`"name.full"`资源的时候，我们将调用getter函数的返回值：

```js
console.log('%s is insane', bad.name.full) // Walter White is insane
```

注意，如果产生的记录转换为一个对象或JSON，virtuals不包括默认。通过`virtuals`：`true`是`toobject()`或`tojson()`他们返回值。

这也是不错的，能够通过`this.name.full`设置`this.name.first`和`this.name.last`。例如，如果我们想respectively改变`bad`的`name.first`和`name.last`为`'Breaking'`和`'Bad'`，只需要：

```js
bad.name.full = 'Breaking Bad'
```

Mongoose让你这样做也是通过[虚拟属性的setter](http://mongoosejs.com/docs/api.html#virtualtype_VirtualType-set)

```js
personSchema.virtual('name.full').set( (name) => {
  let split = name.split(' ')
  this.name.first = split[0]
  this.name.last = split[1]
});

...

mad.name.full = 'Breaking Bad'
console.log(mad.name.first) // Breaking
console.log(mad.name.last)  // Bad
```

`[虚拟属性的setter`在其他验证之前使用。因此，上面的例子仍然可以工作，即使第一个和最后一个name字段是必需的。

只有非虚拟属性工作作为查询的一部分和字段选择。

## 选项（Options）

Schemas有几个可配置的选项，可以直接传递给构造函数或设置：

```js
new Schema({..}, options);

// or

var schema = new Schema({..});
schema.set(option, value);
```

有效的选项：

* [autoIndex](http://mongoosejs.com/docs/guide.html#autoIndex)
* [capped](http://mongoosejs.com/docs/guide.html#capped)
* [collection](http://mongoosejs.com/docs/guide.html#collection)
* [emitIndexErrors](http://mongoosejs.com/docs/guide.html#emitIndexErrors)
* [id](http://mongoosejs.com/docs/guide.html#id)
* [\_id](http://mongoosejs.com/docs/guide.html#_id)
* [minimize](http://mongoosejs.com/docs/guide.html#minimize)
* [read](http://mongoosejs.com/docs/guide.html#read)
* [safe](http://mongoosejs.com/docs/guide.html#safe)
* [shardKey](http://mongoosejs.com/docs/guide.html#shardKey)
* [strict](http://mongoosejs.com/docs/guide.html#strict)
* [toJSON](http://mongoosejs.com/docs/guide.html#toJSON)
* [toObject](http://mongoosejs.com/docs/guide.html#toObject)
* [typeKey](http://mongoosejs.com/docs/guide.html#typeKey)
* [validateBeforeSave](http://mongoosejs.com/docs/guide.html#validateBeforeSave)
* [versionKey](http://mongoosejs.com/docs/guide.html#versionKey)
* [skipVersioning](http://mongoosejs.com/docs/guide.html#versionKey)
* [timestamps](http://mongoosejs.com/docs/guide.html#versionKey)

#### 选项: autoIndex {#选项-autoindex}

在应用程序启动时，Mongoose在你的Schema为每一个索引声明发送一个`ensureIndex`命令。在Mongoose V3版本时，索引是默认在后台创建。如果你想禁用自动创建和手动创建索引时，将你的Schemas自动索引（autoIndex）选项设置为`false`和在你的模型使用`ensureindexes`方法。

```js
var schema = new Schema({..}, { autoIndex: false });
var Clock = mongoose.model('Clock', schema);
Clock.ensureIndexes(callback);
```

#### 选项: bufferCommands {#选项-buffercommands}

默认情况下，mongoose缓冲命令一直存在直到驱动设法重新连接。禁用缓冲，设置`buffercommands`为`false`。

```js
var schema = new Schema({..}, { bufferCommands: false });
```

#### 选项: capped {#选项-capped}

Mongoose支持 MongoDBs[capped](http://www.mongodb.org/display/DOCS/Capped+Collections)集合。指定的MongoDB集合被封顶、设置封顶（capped）选项为文档的最大[字节](http://www.mongodb.org/display/DOCS/Capped+Collections#CappedCollections-size.)数。

```js
new Schema({..}, { capped: 1024 });
```



