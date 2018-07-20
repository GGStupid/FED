### 环境
node v10.4.0
MongoDB  v3.4.9
vscode  1.19.3

## 使用koa和egg去建一个简易的blog
博客的前端页面公用一套，逻辑也是一样的，完成后页面大致如下：
![登录注册页](http://p4i4zcg0d.bkt.clouddn.com/1529552694215/login.png)


## 使用koa和egg去建一个简易的blog
博客的前端页面公用一套，服务端逻辑也是一样的，只有写法上有点差距，完成后页面大致如下：
#### 登录注册
![登录注册页](http://p4i4zcg0d.bkt.clouddn.com/1529552694215/login.png)
#### 主页
![主页](http://p4i4zcg0d.bkt.clouddn.com/1529552694215/homeindex.png)
#### 文章管理
![文章管理](http://p4i4zcg0d.bkt.clouddn.com/1529552694215/postmanage.png)
#### 写文章和更新文章
![文章管理](http://p4i4zcg0d.bkt.clouddn.com/1529552694215/addpost.png)

### 关于前端页面这里就不做详细介绍了
具体查看[地址](https://gitee.com/lvjinlong/node-koa-egg-vue.git)
```
git clone https://gitee.com/lvjinlong/node-koa-egg-vue.git
```
克隆下来后在`web-view`目录就是前端的一些页面。
### 先使用koa做后端服务
目录结构如下：
```
├── controllers  
│   ├── post.js
│   └── user.js
├── middle
│   └── tokenMid.js
├── models
│   ├── post.js
│   └── user.js
├── services
│   ├── post.js
│   └── user.js
├── index.js
├── .gitignore
├── package-lock.json	
├── package.json
├── readme.md
```
我把目录结构分为四个文件夹：
controllers:对应的url交给特定的controller处理,
middle:中间件管理,
models:定义需要用到的model
services:操作数据库的各种service,
这其实和egg框架的目录结构差不多，目录很清晰，知道去什么地方找什么文件。



### 新建目录结构
``` bash
mkdir koa-demo && cd koa-demo

npm init

 mkdir models && mkdir services && mkdir middle && mkdir controllers && mkdir config && touch index.js

```

├── config
├── controllers
├── middle
├── models
├── services
├── index.js
└──package.json

npm i koa koa-bodyparser koa-router mongoose jsonwebtoken -S

npm i validator -S

npm i bcrypt -S

修改index.js,用koa起个服务
``` javascript
const Koa = require('koa')
const app = new Koa()

app.use(async ctx => {
  ctx.body = 'hello world'
})

app.listen(3000, () => {
  console.log('server is running')
})

```

安装nodemon,然后修改package.json的scripts:
``` bash
npm  i nodemon -D
"dev": "nodemon ./index.js"
```
然后就可以用npm run dev 进行开发了，不用每次修改文件都重启服务

链接MongoDB,修改index.js
``` javascript
const Koa = require('koa')
const app = new Koa()

const mongoose = require('mongoose')

mongoose.connect('mongodb://127.0.0.1:27017/koa-demo')

const db = mongoose.connection

db.on('error', (e) => {
  console.log(e)
})

db.on('open', (e) => {
  console.log('db connect success')
})


app.use(async ctx => {
  ctx.body = 'hello world'
})

app.listen(3000, () => {
  console.log('server is running')
})
```

在modles新建user.js:
``` javascript
const mongoose = require('mongoose')
const Schema = mongoose.Schema

const UserSchema = new Schema({
  name: {
    type: String,
    required: true,
    unique: true
  },
  password: {
    type: 'string',
    required: true
  },
  meta: {
    createAt: {
      type: Date,
      default: Date.now()
    }
  }
})

module.exports = mongoose.model('User', UserSchema)
```

在service新建user.js:
``` javascript
const UserModel = require('../models/user')

module.exports = {
  // 通过用户名找用户
  getUserByLoginName (name) {
    const query = { name }
    return UserModel.findOne(query).exec()
  },
  getUserById (id) {
    if (!id) {
      return null;
    }
    return UserModel.findOne({ _id: id }).exec()
  },
  newAndSave (name, password, token) {
    const user = new UserModel({
      name,
      password
    })
    return user.save()
  }
}

```

在controllers新建user.js,安装validator，bcrypt
``` javascript
const userService = require('../services/user.js')
const validator = require('validator')
const bcrypt = require('bcrypt')

module.exports = {
  async login (ctx) {
    let name = ctx.request.body.name
    let password = ctx.request.body.password
    if (!name || !password) {
      return ctx.body = {
        code: 401,
        data: "",
        msg: '请输入用户名和密码',
        success: false
      }
    }
    const user = await userService.getUserByLoginName(name)
    const isEqual = bcrypt.compareSync(password, user.password)
    if (isEqual) {
      return ctx.body = {
        code: 200,
        data: user,
        msg: '成功',
        success: true
      }
    } else {
      return ctx.body = {
        code: 401,
        data: '',
        msg: '登录失败',
        success: false
      }
    }
  },
  async register (ctx) {
    let name = ctx.request.body.name
    let password = ctx.request.body.password
    let repassword = ctx.request.body.repassword
    if (password !== repassword) {
      return ctx.body = {
        code: 400,
        data: '',
        msg: '密码不一致',
        success: false
      }
    }

    let user = await userService.getUserByLoginName(name)
    if (user && user.name === name) {
      return ctx.body = {
        code: 401,
        data: '',
        msg: '用户名已存在',
        success: false
      }
    }
    password = bcrypt.hashSync(password, 10)
    await userService.newAndSave(name, password)
    return ctx.body = {
      code: 200,
      data: '注册成功',
      msg: '成功',
      success: false
    }
  }
}
```

修改index.js如下：
``` javascript
const Koa = require('koa')
const app = new Koa()
const bodyParser = require('koa-bodyparser')
app.use(bodyParser())

const mongoose = require('mongoose')
mongoose.connect('mongodb://127.0.0.1:27017/koa-demo')
const db = mongoose.connection
db.on('error', (e) => {
  console.log('ttdfgsfst', e)
})
db.on('open', (e) => {
  console.log('db connect success')
})


app.use(async (ctx, next) => {
  const start = new Date();
  await next();
  const ms = new Date() - start;
  console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
})

const Router = require('koa-router')
const router = new Router()
router.prefix('/api')

const userController = require('./controllers/user.js')

router.post('/user/login', userController.login)
router.post('/user/register', userController.register)

app.use(router.routes()).use(router.allowedMethods());

app.listen(3000, () => {
  console.log('server is running')
})
```

在middle下面新建tokenMid文件，进行token验证:
``` javascript
const jwt = require('jsonwebtoken')

module.exports = {
  createToekn (id) {
    const token = jwt.sign({ id }, 'app', { expiresIn: '60s' })
    return token
  },
  async  checkToken (ctx, next) {
    const token = ctx.get('Authorization')
    if (!token) {
      ctx.throw(ctx.throw(401, 'no Authorization in http header'))
    }
    try {
      let tokenContent = await jwt.verify(token, 'app')
    } catch (err) {
      ctx.throw(402, 'invalid token')
    }
    await next()
  }
} 
```

然后修改controllers下的user.js,如下登录生成token并返回：
``` javascript
const validator = require('validator')
const bcrypt = require('bcrypt')
const userService = require('../services/user.js')
const tokenMid = require('../middle/tokenMid.js')

module.exports = {
  async login (ctx) {
    let name = ctx.request.body.name
    let password = ctx.request.body.password
    if (!name || !password) {
      return ctx.body = {
        code: 401,
        data: "",
        msg: '请输入用户名和密码',
        success: false
      }
    }
    const user = await userService.getUserByLoginName(name)
    console.log(user)
    const isEqual = bcrypt.compareSync(password, user.password)
    if (isEqual) {
      let token = tokenMid.createToekn(user._id)
      return ctx.body = {
        code: 200,
        data: {
          token,
          name: user.name
        },
        msg: '成功',
        success: true
      }
    } else {
      return ctx.body = {
        code: 401,
        data: '',
        msg: '登录失败',
        success: false
      }
    }
  },
  async register (ctx) {
    let name = ctx.request.body.name
    let password = ctx.request.body.password
    let repassword = ctx.request.body.repassword
    if (password !== repassword) {
      return ctx.body = {
        code: 400,
        data: '',
        msg: '密码不一致',
        success: false
      }
    }

    let user = await userService.getUserByLoginName(name)
    if (user && user.name === name) {
      return ctx.body = {
        code: 401,
        data: '',
        msg: '用户名已存在',
        success: false
      }
    }
    password = bcrypt.hashSync(password, 10)
    await userService.newAndSave(name, password)
    return ctx.body = {
      code: 200,
      data: '注册成功',
      msg: '成功',
      success: false
    }
  }
}
```

### 参考

[koa2](https://www.jianshu.com/p/6b816c609669)

### 安装node环境
1. 直接在[官网下载](https://nodejs.org/zh-cn/),然后双击即可安装完成
2. 通过nvm管理安装node,如下：
``` bash
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.6/install.sh | bash

$ echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.zshrc
$ echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" # This loads nvm' >> ~/.zshrc
$ source ~/.zshrc

$ nvm install v8.11.1
$ node -v  #验证

```

### 安装MongoDB
[官网下载](https://www.mongodb.com/download-center?jmp=nav#community)，双击解压

进入bin目录，启动mongodb
sudo ./mongod

[各种情况下的MongoDB的启动与关闭](http://www.360doc.com/content/17/0820/19/36471792_680678706.shtml)
