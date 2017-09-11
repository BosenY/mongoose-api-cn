入门

首先确保你已经安装了[MongoDB](https://www.gitbook.com/book/boseny/mongoose-api-cn/edit#)和[Node.js](https://www.gitbook.com/book/boseny/mongoose-api-cn/edit#)接下来使用npm来安装Mongoose：

`$ npm install mongoose`

然后我们所需要做的第一件事情就是去在我们的项目当中使用mongoose去连接我们本地的MongoDB数据库

```js
letmongoose=require('mongoose')
mongoose.connect('mongodb://localhost/test')
```

现在假设我们有一个挂起在本地的测试数据库。如果连接成功或发生连接错误，我们现在需要去收到通知：

```js
let db = mongoose.connection
db.on('error',(error) => {
    console.log('connection error!')
})
db.once('open',(cb) => {
    //yay!
})
```

一旦我们的连接成功后，我们的回调将会被调用。为了简介起见，我们假设所有以下代码都在此回调之内。

使用Mongoose，一切都源于[Schema\(表单\)](https://www.gitbook.com/book/boseny/mongoose-api-cn/edit#)。让我们来使用它定义一个小猫：

```js
let kittySchema = mongoose.Schema({
    name: String
})
```

现在我们设置了一个属性name，它的类型是string，接下来我们要将我们的Schema编译成模型。

```js
var Kitten = mongoose.model('Kitten', kittySchema)
```



