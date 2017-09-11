# 入门

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
let Kitten = mongoose.model('Kitten', kittySchema)
```

模型就是一个我们用来构建文档的一个类，在这种情况下，每一个文档都将会是一个小猫，它的属性和行为会在我们的model当中被声明。我们去创建一个小猫的实例

```js
let jony = new Kitten({name: 'jony'})
console.log(jony.name) //'jony'
```

我们还可以给小猫加上说话的功能：

```js
// NOTE: methods must be added to the schema before compiling it with mongoose.model()
kittySchema.methods.speak = () => {
  let greeting = this.name
    ? "Meow name is " + this.name
    : "I don't have a name"
  console.log(greeting);
}
let Kitten = mongoose.model('Kitten', kittySchema)
```

将speak方法添加到method属性上然后编译成model原型并且在每个文档上面去公开

```js
let axx = new Kitten({name: 'axx'})
axx.speak() // 'Meow name is axx'
```

现在我们有了可以让小猫说话的功能，但是我们仍然没有保存任何东西到我们的MongoDB，每一个实例文档都可以去调用其save方法来讲这个实例保存到数据库，callback的第一个参数是error，如果有的话：

```js
axx.save((err, axx) => {
    if(err) return console.error(err)
    axx.speak()
})
```



