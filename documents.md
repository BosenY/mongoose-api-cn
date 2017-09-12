# Domcuments（文档）

Mongoose[文档](http://mongoosejs.com/docs/api.html#document-js)代表了文档存储在MongoDB的一对一映射。每个文档都是它的[模型](http://mongoosejs.com/docs/models.html)的一个实例。

## 索引

有许多方法从MongoDB文档检索。我们不会涵盖在在这一部分中。查看查询详细信息的[‘querying’](http://mongoosejs.com/docs/queries.html)章节

### Updating（更新）

有很多的方法可以用来更新文档。我们可以先来看一下[findById](http://mongoosejs.com/docs/api.html#model_Model.findById)的传统方法：

```js
Tank.findById(id,  (err, tank) => {
  if (err) return handleError(err);

  tank.size = 'large';
  tank.save( (err, updatedTank) => {
    if (err) return handleError(err);
    res.send(updatedTank);
  });
});
```

这种方法包括首先检索文档从Mongo，然后发出更新命令（通过触发调用保存）。然而，如果我们不需要的文档在我们的应用程序中返回，只需要更新一个属性直接在数据库、`Model#update`对我们是正确的：

```js
Tank.update({ _id: id }, { $set: { size: 'large' }}, callback);
```



