## npm
npm设置淘宝镜像
```
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
```
---
## npm的缺点

1. `npm install`的时候巨慢。特别是新的项目拉下来要等半天，删除node_modules，重新install的时候依旧如此。

2. 同一个项目，安装的时候无法保持一致性。由于package.json文件中版本号的特点，下面三个版本号在安装的时候代表不同的含义。
```
"5.0.3",
"~5.0.3",
"^5.0.3"
```
“5.0.3”表示安装指定的5.0.3版本，“～5.0.3”表示安装5.0.X中最新的版本，“^5.0.3”表示安装5.X.X中最新的版本。这就麻烦了，常常会出现同一个项目，有的同事是OK的，有的同事会由于安装的版本不一致出现bug。

3. 安装的时候，包会在同一时间下载和安装，中途某个时候，一个包抛出了一个错误，但是npm会继续下载和安装包。因为npm会把所有的日志输出到终端，有关错误包的错误信息就会在一大堆npm打印的警告中丢失掉，并且你甚至永远不会注意到实际发生的错误。

## yarn
Yarn是Facebook提供的替代npm的工具，可以加速node模块的下载。React Native的命令行工具用于执行创建、初始化、更新项目、运行打包服务（packager）等任务

Yarn 相比 npm，它有两个显著特点。

（1）安装速度较快。

并行安装：无论 npm 还是 Yarn 在执行包的安装时，都会执行一系列任务。npm 是按照队列执行每个 package，也就是说必须要等到当前 package 安装完成之后，才能继续后面的安装。而 Yarn 是同步执行所有任务，提高了性能。

离线模式：如果之前已经安装过一个软件包，用Yarn再次安装时之间从缓存中获取，就不用像npm那样再从网络下载了。

（2）默认开启“版本锁定”功能

安装版本统一：为了防止拉取到不同的版本，Yarn 有一个锁定文件 (lock file) 记录了被确切安装上的模块的版本号。每次只要新增了一个模块，Yarn 就会创建（或更新）yarn.lock 这个文件。这么做就保证了，每一次拉取同一个项目依赖时，使用的都是一样的模块版本。npm 其实也有办法实现处处使用相同版本的 packages，但需要开发者执行 npm shrinkwrap 命令。这个命令将会生成一个锁定文件，在执行 npm install 的时候，该锁定文件会先被读取，和 Yarn 读取 yarn.lock 文件一个道理。npm 和 Yarn 两者的不同之处在于，Yarn 默认会生成这样的锁定文件，而 npm 要通过 shrinkwrap 命令生成 npm-shrinkwrap.json 文件，只有当这个文件存在的时候，packages 版本信息才会被记录和更新。

 (3) 更简洁的输出
 
npm 的输出信息比较冗长。在执行 npm install  的时候，命令行里会不断地打印出所有被安装上的依赖。相比之下，Yarn 简洁太多：默认情况下，结合了 emoji直观且直接地打印出必要的信息，也提供了一些命令供开发者查询额外的安装信息。

 (4) 多注册来源处理

所有的依赖包，不管他被不同的库间接关联引用多少次，安装这个包时，只会从一个注册来源去装，要么是 npm 要么是 bower, 防止出现混乱不一致。

 (5) 更好的语义化
  
yarn改变了一些npm命令的名称，比如 yarn add/remove，感觉上比 npm 原本的 install/uninstall 要更清晰。

## Yarn和npm命令对比

npm                            yarn

npm install                    yarn

npm install react --save       yarn add react

npm uninstall react --save     yarn remove react

npm install react --save-dev   yarn add react --dev

npm update --save              yarn upgrade


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