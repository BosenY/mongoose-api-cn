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

```



