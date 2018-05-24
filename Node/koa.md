# koa中请求数据获取
在web应用中，最常见的就是`get`和`post`两种请求方式，请求都是浏览器通过`URL`地址访问服务器的某个具体的服务，返回对应的业务数据。

所以我们需要根据不同的`URL`匹配不同的服务，即实现路由功能。在`koa`中，可以使用`koa-router`中间件实现路由。

### koa2 原生路由实现
```
const Koa = require('koa')
const app = new Koa()

app.use( async ( ctx ) => {
  let url = ctx.request.url
  let html
  switch ( url ) {
      case '/':
        html = '<h1>首页</h1>'
        break
      case '/404':
        html = '<h1>404</h1>'
        break
      default:
        break
  }
  ctx.body = html
})
app.listen(3000)
console.log('app started at port 3000...')
```

访问不同的`URL`,页面会输出不同的内容，其本质就是根据`ctx.request.url`通过一定的判断或者正则匹配就可以定制出所需要的路由。

### koa-router中间件
```
// 安装
npm install --save koa-router

// 示例
const Koa = require('koa')
const app = new Koa()

const Router = require('koa-router')

// controller1
let home = new Router()
home.get('/', async ( ctx )=>{
  let html = `
    <ul>
      <li><a href="/page/page1">/page/page1</a></li>
      <li><a href="/page/404">/page/404</a></li>
    </ul>
  `
  ctx.body = html
})

// controller2
let page = new Router()
page.get('/404', async ( ctx )=>{
  ctx.body = '404 page!'
}).get('/page1', async ( ctx )=>{
  ctx.body = 'page1'
})

// 加载controller
let router = new Router()
router.use('/', home.routes(), home.allowedMethods())
router.use('/page', page.routes(), page.allowedMethods())

// 加载路由中间件
app.use(router.routes()).use(router.allowedMethods())

app.listen(3000)
console.log('app started at port 3000...')
```
更多的`koa-router`使用说明具体参考[官网](https://www.npmjs.com/package/koa-router)

### GET请求数据获取

获取`GET`请求中数据的方法是使用`koa`中`request`对象中的`query`方法或`querystring`方法，`query`返回是格式化好的参数对象，`querystring`返回的是请求字符串，由于`ctx`对`request`的`API`有直接引用的方式，所以获取GET请求数据有两个途径:

1. 是从上下文中直接获取
请求对象ctx.query，返回:`{ a:1, b:2 }`
请求字符串 ctx.querystring，返回:`a=1&b=2`

2. 是从上下文的request对象中获取
请求对象ctx.request.query，返回如 { a:1, b:2 }
请求字符串 ctx.request.querystring，返回如 a=1&b=2

配合`koa-router`使用：
```
const Koa = require('koa');
const Router = require('koa-router')
const router = new Router()
const app = new Koa()

app.use(async (ctx, next) => {
  console.log(`Process ${ctx.request.method} ${ctx.request.url}...`)
  await next()
})
// get
router.get('/api/test1/:name', async (ctx, next) => {
  var name = ctx.params.name;
  ctx.body = {
    success: true,
    data: {
      hello: name
    },
    msg: '成功',
    code: 200
  }
})
// get
router.get('/api/test2', async (ctx, next) => {
  ctx.body = {
    success: true,
    data: {
      hello: '哈哈'
    },
    msg: '成功',
    code: 200
  }
})

app.use(router.routes())

app.listen(3000)
console.log('app started at port 3000...')
```

### POST请求参数获取

对于`POST`请求的处理，`koa2`没有封装获取参数的方法，需要通过解析上下文`context`中的原生`node.js`请求对象`req`，将`POST`表单数据解析成`query string`（例如：`a=1&b=2`），再将`query string`解析成`JSON`格式（例如：`{"a":"1", "b":"2"}`）

在`koa`中，`ctx.request`是`context`经过封装的请求对象，`ctx.req`是`context`提供的`node.js`原生`HTTP`请求对象，同理`ctx.response`是`context`经过封装的响应对象，`ctx.res`是`context`提供的`node.js`原生`HTTP`请求对象。

对于POST请求的处理，`koa-bodyparser`中间件可以把`koa2`上下文的`formData`数据解析到`ctx.request.body`中

配合`koa-router`使用：
```
const Koa = require('koa');
const Router = require('koa-router')
const bodyParser = require('koa-bodyparser')
const router = new Router()
const app = new Koa()

app.use(bodyParser())

app.use(async (ctx, next) => {
  console.log(`Process ${ctx.request.method} ${ctx.request.url}...`)
  await next()
})
// get
router.get('/api/test1/:name', async (ctx, next) => {
  var name = ctx.params.name;
  ctx.body = {
    success: true,
    data: {
      hello: name
    },
    msg: '成功',
    code: 200
  }
})
// post
router.post('/api/test2', async (ctx, next) => {
    let postData = ctx.request.body
    ctx.body = {
      success: true,
      data: {
        hello: postData
      },
      msg: '成功',
      code: 200
    }
})

app.use(router.routes())

app.listen(3000)
console.log('app started at port 3000...')
```

### 总结

在`koa`中，可以利用`koa-router`和`koa-bodyparser`中间件，简单的实现`restful`接口。
