# Models\(模型\)

模型是从我们的Schema定义的构造函数编译。这些模型代表[文档](http://mongoosejs.com/docs/documents.html)可以从我们的数据库中保存和检索。从数据库中依靠这些模型来操作所有文档创建和检索。

#### 编译你的第一个模型

```js
var schema = new mongoose.Schema({ name: 'string', size: 'string' });
var Tank = mongoose.model('Tank', schema);
```

第一个参数是你的模型集合的单数名称。Mongoose会自动寻找你的模型名称的复数形式。因此，对于上面的示例，模型`Tank`是用于数据库中的`tanks`集合的。.model\(\) 功能使得到schema的副本。确信你已经添加了你想要的一切在调用.model\(\)之前！

通俗的说就是这里命名的Tank模型，到了MongoDB当中就变成了tanks，因为是以复数的形式存储的。

## 构建文档

文档是我们模型的实例。创建它们并保存到数据库很简单。

```js
var Tank = mongoose.model('Tank', yourSchema);

var small = new Tank({ size: 'small' });
small.save(function (err) {
  if (err) return handleError(err);
  // saved!
})

// or

Tank.create({ size: 'small' }, function (err, small) {
  if (err) return handleError(err);
  // saved!
})
```

请注意，没有`tanks`将创建/删除，直到连接您的模型使用是打开的。每一个模型都有一个关联的连接。当你使用`mongoose.model()`。你的模型将要使用默认mongoose连接。

```js
mongoose.connect('localhost', 'gettingstarted');
```

如果你创建一个自定义的连接，使用连接的model\(\)函数来代替。

```js
var connection = mongoose.createConnection('mongodb://localhost:27017/test');
var Tank = connection.model('Tank', yourSchema);
```

### Querying（查询）

Mongoose查找文档很容易，它支持MongoDB的[丰富的](http://www.mongodb.org/display/DOCS/Advanced+Queries)查询语法。文档可以在每个模型中各自使用[find](http://mongoosejs.com/docs/api.html#model_Model.find),[findById](http://mongoosejs.com/docs/api.html#model_Model.findById),[findOne](http://mongoosejs.com/docs/api.html#model_Model.findOne), 或者[where](http://mongoosejs.com/docs/api.html#model_Model.where)

这些标准方法。

```js
Tank.find({ size: 'small' }).where('createdDate').gt(oneYearAgo).exec(callback);
```

查看关于如何使用[查询](http://mongoosejs.com/docs/api.html#query-js)API的详细信息的[querying](http://mongoosejs.com/docs/queries.html)章节。

### Removing\(删除\)

模型有一个静态的remove方法用来删除匹配的所有文档。

```js
Tank.remove({ size: 'large' }, function (err) {
  if (err) return handleError(err);
  // removed!
});
```

### Updating（更新）

每个模型都有它自己的uodate方法来修改自己数据库当中的文档，而不返回到您的应用程序。有关更多详细信息，请参考[API](http://mongoosejs.com/docs/api.html#model_Model.update)

文档。

如果你想要更新一个文档数据库，并将结果返回给你的应用程序，使用[`findOneAndUpdate`](http://mongoosejs.com/docs/api.html#model_Model.findOneAndUpdate)代替。

### more更多的

API文档包含了许多额外的方法，像[count](http://mongoosejs.com/docs/api.html#model_Model.count)，[mapReduce](http://mongoosejs.com/docs/api.html#model_Model.mapReduce)，[aggregate](http://mongoosejs.com/docs/api.html#model_Model.aggregate)，[更多](http://mongoosejs.com/docs/api.html#model_Model.findOneAndRemove)。

