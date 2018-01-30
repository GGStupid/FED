### 初始化

安装`dva-cli`:

```
npm i -g dva-cli
 或者
yarn add global dva-cli
```

创建项目:

```
dva new dva-demo

cd dva-demo

npm i
```

### 目录结构

```
|- mock
|- node_modules
|- package.json
|- public
|- src
    |- asserts
    |- components
    |- models
    |- routes
    |- services
    |- utils
    |- router.js
    |- index.js
    |- index.css
|- .editorconfig
|- .eslintrc
|- .gitignore
|- .roadhogrc.mock.js
|- .webpackrc
```

mock 存放用于 mock 数据的文件

public 一般用于存放静态文件，打包时会被直接复制到输出目录(./dist)

src 文件夹用于存放项目源代码

asserts 用于存放静态资源，打包时会经过 webpack 处理

components 用于存放 React 组件，一般是该项目公用的无状态组件

models 用于存放模型文件

routes 用于存放需要 connect model 的路由组件

services 用于存放服务文件，一般是网络请求等

utils 工具类库

router.js 路由文件

index.js 项目的入口文件

index.css 一般是共用的样式

.editorconfig 编辑器配置文件

.eslintrc ESLint配置文件

.gitignore Git忽略文件

.roadhogrc.mock.js Mock配置文件

.webpackrc 自定义的webpack配置文件，JSON格式，如果需要 JS 格式，可修改为 .webpackrc.js

### antd按需引入

```
npm i antd babel-plugin-import -S
// 后者
yarn add antd babel-plugin-import
```

在`.webpackrc`添加：
```
{
  "extraBabelPlugins": [
    ["import", { "libraryName": "antd", "libraryDirectory": "es", "style": "css" }]
  ]
}

```

### 开发代理
在`.webpackrc`配置：
```
{
  "proxy": {
    "/api": {
      "target": "http://your-api-server",
      "changeOrigin": true
    }
  }
}
```

### HMR
HMR，即模块热替换，在修改代码后不需要刷新整个页面，方便开发时的调试。可以在 `.webpackrc` 中添加如下配置以使用 HMR:
```
{
  "env": {
    "development": {
      "extraBabelPlugins": [
        "dva-hmr"
      ]
    }
  }
}
```
如果无效，请尝试更新一下`babel-plugin-dva-hmr`

### 组件动态加载
```
import dynamic from 'dva/dynamic';
const UserPageComponent = dynamic({
  app,
  models: () => [
    import('./models/users'),
  ],
  component: () => import('./routes/UserPage'),
})
```

### dva-loading
`dva-loading` 是一个用于处理 loading 状态的 dva 插件，基于 dva 的管理 effects 执行的 hook 实现，它会在 state 中添加一个 loading 字段（该字段可自定义），自动帮你处理网络请求的状态，不需要自己再去写 showLoading 和 hideLoading 方法。

在 ./src/index.js 中引入使用即可：
```
import createLoading from 'dva-loading';
const app = dva();
app.use(createLoading(opts));
```
opts 仅有一个 namespace 字段，默认为 loading

### Model
Model 是 dva 最重要的部分，可以理解为 redux、react-redux、redux-saga 的封装。

通常项目中一个模块对应一个 model，一个基本的 model 如下:
```
import { fetchUsers } from '../services/user';
export default {
  namespace: 'user',
  state: {
    list: [],
  },
  reducers: {
    save(state, action) {
      return {
        ...state,
        list: action.data,
      };
    },
  },
  effects: {
    *fetch(action, { put, call }) {
      const users = yield put(fetchUsers, action.data);
      yield put({ type: 'save', data: users });
    },
  },
  subscriptions: {
    setup({ dispatch, history }) {
      return history.listen(({ pathname }) => {
        if (pathname === '/user') {
          dispatch({ type: 'fetch' });
        }
      });
    },
  },
}
```
namespace 是该 model 的命名空间，同时也是全局 state 上的一个属性，只能是字符串，不支持使用 . 创建多层命名空间。

state 是状态的初始值。

reducer 类似于 redux 中的 reducer，它是一个纯函数，用于处理同步操作，是唯一可以修改 state 的地方，由 action 触发，它有 state 和 action 两个参数。

effects 用于处理异步操作，不能直接修改 state，由 action 触发，也可触发 action。它只能是 generator 函数，并且有 action 和 effects 两个参数。第二个参数 effects 包含 put、call 和 select 三个字段，put 用于触发 action，call 用于调用异步处理逻辑，select 用于从 state 中获取数据。

subscriptions 用于订阅某些数据源，并根据情况 dispatch 某些 action，格式为 ({ dispatch, history }, done) => unlistenFunction。

如上的一个 model，监听路由变化，当进入 /user 页面时，执行 effects 中的 fetch，以从服务端获取用户列表，然后 fetch 中触发 reducers 中的 save 将从服务端获取到的数据保存到 state 中。

注意，在 model 中触发这个 model 中的 action 时不需要写命名空间，比如在 fetch 中触发 save 时是 { type: 'save' }。而在组件中触发 action 时就需要带上命名空间了，比如在某个组件中触发 fetch 时，应该是 { type: 'user/fetch' }。

### app

在`index.js`看到如下代码：

```
import dva from 'dva';
const app = dva();
```

app 就是 dva 实例，创建实例时 dva 方法可以传入一些参数，如下：

```
history
initialState
onError
onAction
onStateChange
onReducer
onEffect
onHmr
extraReducers
extraEnhancers
```

history 是给路由用的，默认为 hashHistory，如果想要使用 browserHistory，需要安装 history，然后在 ./src/index.js 引入使用：

```
import dva from 'dva';
import createHistory from 'history/createBrowserHistory';
const app = dva({
  history: createHistory(),
});
```

initialState 是 state 的初始数据，优先级高于 model 中的 state，默认为 {}。

具体API参考[dvaAPI](https://github.com/dvajs/dva/blob/master/docs/API_zh-CN.md#dva-api)

### connect

当写完 model 和组件后，需要将 model 和组件连接起来。dva 提供了 connect 方法，其实它就是 react-redux 的 connect。用法如下:

```
import React from 'react';
import { connect } from 'dva';
const User = ({ dispatch, user }) => {
  return (
    <div></div>
  )
}
export default connect(({ user }) => {
  return user;
})(User);
```
connect 后的组件除了可以获取到 dispatch 和 state，还可以获取到 location 和 history。

### 错误处理

effects 和 subscriptions 抛出的错误都会经过 onError 钩子函数，所以可以在 onError 中进行全局错误处理。

```
const app = dva({
  onError(err, dispatch) {
    console.error(err);
  },
});
```
如果需要对某些 effects 进行特殊的错误处理，可以使用 try catch。

### 异步请求

dva 集成了 isomorphic-fetch 用于处理异步请求，并且使用 dva-cli 初始化的项目中，已经在 ./src/utils/request.js 中对 fetch 进行了简单的封装，可以在这里根据服务端 API 的数据结构进行统一的错误处理。

当然如果你不想用 fetch，完全可以引入自己喜欢的第三方库，没有任何影响，打包时也不会将 isomorphic-fetch 打包进去。

参考如下：

[dva-started](https://pengtikui.cn/dva.js-get-started/)

[babel-plugin-import](https://github.com/ant-design/babel-plugin-import)

[dva-knowledgemap](https://github.com/dvajs/dva-knowledgemap)

[dva-api](https://github.com/dvajs/dva/blob/master/docs/API_zh-CN.md#dva-api)

[dva.js 概念](https://github.com/dvajs/dva/blob/master/docs/Concepts_zh-CN.md)

[项目实战](https://ant.design/docs/react/practical-projects-cn)