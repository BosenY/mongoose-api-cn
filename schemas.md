Schemas（架构）

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

如果我们还想添加其他的key，那么可以去使用Schema的add方法。

```js
blogSchema.add（{
    commentNum: Number,
    readNum: Number
}）
```

这里我们给上面的博客的schema添加了评论总数和阅读总数两个key，具体的schema上面的add方法是如何实现的，请自己去看官方的文档上面的[add方法](http://www.nodeclass.com/api/mongoose.html#schema_Schema-add)。



