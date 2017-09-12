# sub docs（子文档）

子文档是嵌入其他文档的文档。在Mongoose中，这意味着您可以将model嵌套在其他的model中，Mongoose有两个不同的子文档概念：子文档数组和单个嵌套子文档。

```js
var childSchema = new Schema({ name: 'string' });

var parentSchema = new Schema({
  // Array of subdocuments
  children: [childSchema],
  // Single nested subdocuments. Caveat: single nested subdocs only work
  // in mongoose >= 4.2.0
  child: childSchema
});
```

子文件类似于正常文档。嵌套模式可以具有[中间件](http://mongoosejs.com/docs/middleware.html)，[自定义验证逻辑](http://mongoosejs.com/docs/middleware.html)，虚拟和任何其他功能顶级模式可以使用。

主要区别在于子文档不是单独保存，每当保存顶级父文档时，都会保存它们。

