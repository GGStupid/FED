### 原因
Babel 的官网上在9月宣布 ES2015 / ES2016/ ES2017 等等 ES20xx 时代的 presets 通通被废弃（deprecated），取而代之的是 babel-preset-env，并且承诺它将成为“未来不会过时的（future-proof）”解决方案。

### 升级
```
// 卸载原来的preset，安装babel-preset-env
npm uninstall --save-dev babel-preset-es2015
npm install --save-dev babel-preset-env@next
```
### 修改.babelrc
```
{
  "presets": [ "env" ],
  ...
}
```

### targets选项
通过`targets`指定需要兼容的浏览器类型和版本（采用[ai/browserslist](https://github.com/ai/browserslist)查询语法):
```
{
  "presets" : [
    [
      "env", {
        "targets" : {
          "browsers" : [ "last 2 versions", "safari >= 7"]
        }
      }
    ]
  ]
}
```
通过指定更高的浏览器版本来减少插件和 polyfill 的代码量，并且直接使用原生 ES6 的新特性，特别适合 Electron 及移动端 App 或者那些已指定了浏览器的内网应用程序。例如，将浏览器设为 Chrome 较高的版本，Promise、Map、Set 等内建类均不会被 polyfill，而同时 class 等新语法也不会被 Babel 转译，转而使用 V8 自带的 ES6 Class。

如果开发`Node.js`应用，也可以制定`Node`的版本：
```
{
  "presets": [
    [
      "env", {
        "targets": {
          "node": "6.10"
        }
      }
    ]
  ]
}
```
为了方便，你也可以直接写成 `"node": "current"`，将自动采用你当前用来运行 `Babel` 的 `Node.js` 版本。

### modules选项
用来指定模块化方式，支持 AMD、UMD、SystemJS、CommonJS 等。当然在 Webpack 2/3 的时代，推荐将 modules 设置为 false，即交由 Webpack 来处理模块化，通过其 TreeShaking 特性将有效减少打包出来的 JS 文件大小：
```
{
  "presets": [
    ["env",{
      "modules": false,
      ...
    }]
  ]
}
```
从 Webpack 2 开始，已自动启用对 Native Import 的支持，因此无需对 Webpack 进行额外设置,详情:
[Henry Li：ECMAScript 6 的模块相比 CommonJS 的require (...)有什么优点？](https://www.zhihu.com/question/27917401/answer/223309781)

### useBuiltIns 选项与 Polyfill
首先安装 babel-polyfill:
```
npm install babel-ployfill@next --save
```
当 useBuiltIns 设置为 usage 时，Babel 会在你使用到 ES2015+ 新特性时，自动添加 babel-polyfill 的引用，并且是 partial 级别的引用。
请注意： usage 的行为类似 babel-transform-runtime，不会造成全局污染，因此也会不会对类似 Array.prototype.includes() 进行 polyfill。

很多人习惯于在 vendor 中一次性引入 babel-polyfill，在过去这将导致整个 babel-polyfill 包被打包到 vendor 中，在方便开发的同时失去了灵活性，而现在你可以将 useBuiltIns 设置为 entry，Babel 会自动进行优化：
例如：

vendor.js ：

```
import 'babel-polyfill';
```

index.js：
```
const promise = new Promise();
const map = new Map();
```
最终 vendor.js 会被转译为：
```
import "babel-polyfill/core-js/modules/es6.map";
import "babel-polyfill/core-js/modules/es6.promise";
```
