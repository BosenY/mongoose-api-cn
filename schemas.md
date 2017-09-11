Schemas（架构）

定义：

Mongoose中的一切都是从schema开始的，每个schema映射了一个MongoDB集合并且定义了它的数据文档实例的形状。

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



