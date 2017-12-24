## npm
npm设置淘宝镜像
```
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
```
---
## yarn
Yarn是Facebook提供的替代npm的工具，可以加速node模块的下载。React Native的命令行工具用于执行创建、初始化、更新项目、运行打包服务（packager）等任务

Yarn 相比 npm，它有两个显著特点。

（1）安装速度较快。

Yarn 采用平行安装模式，而 npm 采用的是线性模式，只有前一个模块安装完，才会安装下一个。

（2）默认开启“版本锁定”功能

Yarn 希望安装依赖时，所有依赖的版本在不同机器都保持相同。为了达到这个目的，第一次安装依赖时，它默认生成一个锁定文件yarn.lock，将这个文件放到代码库之中，下次安装时就能保证，总是安装相同版本的依赖。这与npm shrinkwrap命令生成的npm-shrinkwrap.json的作用相似，只不过 Yarn 默认就可以生成这个文件。

安装
```
brew install yarn
```

设置
```
yarn config set registry https://registry.npm.taobao.org --global
yarn config set disturl https://npm.taobao.org/dist --global
```

 升级
```
brew upgrade yarn
```

初始化新项目
```
yarn init
```

添加依赖包
```
yarn add [package]
yarn add [package]@[version]
yarn add [package]@[tag]
```

将依赖项添加到不同依赖项类别
分别添加到 `devDependencies`、`peerDependencies` 和` optionalDependencies`:
```
yarn add [package] --dev
yarn add [package] --peer
yarn add [package] --optional
```

升级
```
yarn upgrade [package]
yarn upgrade [package]@[version]
yarn upgrade [package]@[version]
```

移除依赖包
```
yarn remove [package]
```

安装项目的全部依赖
```
yarn 或者 yarn install
```

安装选项
```
安装所有依赖：yarn 或 yarn install
安装一个包的单一版本：yarn install --flat
强制重新下载所有包：yarn install --force
只安装生产环境依赖：yarn install --production
```
具体说明----[yarn官网](https://yarnpkg.com/zh-Hans/docs/getting-started)