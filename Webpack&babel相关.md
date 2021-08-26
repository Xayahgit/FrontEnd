## Webpack基础

本质上，**webpack** 是一个用于现代 JavaScript 应用程序的 *静态模块打包工具*。当 webpack 处理应用程序时，它会在内部构建一个 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/)，此依赖图对应映射到项目所需的每个模块，并生成一个或多个 *bundle*。

![](C:\Users\shiyifei01_sx\AppData\Roaming\Typora\typora-user-images\image-20210727155939001.png)

### 1.入口(entry) 

**入口起点(entry point)** 指示 webpack 应该使用哪个模块，来作为构建其内部 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/) 的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

默认值是 `./src/index.js`

### 2.输出（output）

**output** 属性告诉 webpack 在哪里输出它所创建的 *bundle*，以及如何命名这些文件。主要输出文件的默认值是 `./dist/main.js`，其他生成文件默认放置在 `./dist` 文件夹中。

``` js
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js',
  },
};
```

### 3.loader

webpack 只能理解 JavaScript 和 JSON 文件，这是 webpack 开箱可用的自带能力。**loader** 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 [模块](https://webpack.docschina.org/concepts/modules)，以供应用程序使用，以及被添加到依赖图中

``` js
module.exports = {
  module: {
    rules: [
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.ts$/, use: 'ts-loader' },
    ],
  },
};

```



### 4.插件plugin

loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。

webpack **插件**是一个具有 [`apply`]方法的 JavaScript 对象。

## Webpack--bundle.js相关

### 1.webpack.config.js

这是webpack打包的配置文件

```js
const path = require('path')

module.exports = {
  mode: 'development',
  entry: './src/index.js',//入口文件
  output: {
    path: path.resolve(__dirname, '/dist') //输出路径
    filename: 'bundle.js' //输出文件名
  }
}
```

webpack会找到这些配置信息并根据这些信息去启动webpack生成对应文件夹和文件

**结论一：(前半部分)webpack首先会去获取配置信息， 根据配置信息启动Webpack， 执行构建。(后半部分)并根据配置信息生成对应文件夹和文件**

### 2.有关bundle.js

上部分是一个自执行的函数，形参是modules  下方是自执行函数的实参 

key：路径  value：作为一个自执行函数用eval包裹了我们的index.js中的代码

**结论二：webpack打包出的其实是一个自执行函数，其接收一个对象参数，该对象key值为路径，value值为一个函数体内用eval包裹着key值代表的路径的文件内容的自执行函数**

### 3.自执行函数内部的函数体

_webpack_require_其实是为了替代require  因为require并不被浏览器解析  所以webpack首先自己实现了一个require

```js
return _webpack_require__(_webpack_require__.s="./src/index.js");
```

也是这一句代码 造就了webpack是从入口文件开始分析

**结论三：(前半部分)webpack打包出的bundle.js中，自己实现了一个require函数，并返回以入口文件路径为参数的require函数的执行结果。(后半部分)并且webpack从入口文件开始分析**

### 4.有import情况下的bundle.js

上部分依然是自执行函数  实参产生变化 由之前的一对key:value变成了两对

**结论四：webpack会对import和import的文件的路径进行解析过滤，也就是获取到import路径的值**

**结论五：webpack不仅会解析import的路径，还会将其相对路径处理为根路径(比如./sayhi处理为./src/sayhi.js）**

**结论六：webpack会将那些浏览器不执行的给转换成浏览器可执行的函数(这跟结论三的自己实现一个require函数其实关联了)**

如果在一个a.js中import另一个js b.js，在b.js中引用c.js 

**结论七：webpack会深度查找并解析import和其路径的**

如果在a.js中同时引用import多个js   ——b.js  c.js

**结论八：webpack会对所有的import和其路径进行解析**

### 5.bundle.js的实现

需要获取webpack配置信息——webpack.config.js

之后根据这些配置信息进行打包和构建

### 6.打包细节

打包css文件的时候需要安装css-loader、在output后的module语块中来处理打包的cs

s文件  ps：如果要打包css文件 必须要在入口文件引入css文件。

### 7.根据业务逻辑分来打包

修改webpack.config.js

```
// entry是入口文件，可以多个，代表要编译那些js
 ``entry:[``'./src/main.js'``,``'./src/login.js'``,``'./src/reg.js'``],
```

这样就可以全部打包，最终生成./build/js/build.js

如果想要打包成不同的bundle.js

需要用到webpack的模块拆分

在base.config.js文件下：

```js
module.exports = {
    output: {filename:'bundle.js' ... }
}
```

```js
module.exports = {
  entry: {
    main: path.resolve(__dirname, 'src/index.js'),
    ProductList: path.resolve(__dirname, 'src/ProductList/ProductList.js'),
    ProductPage: path.resolve(__dirname, 'src/ProductPage/ProductPage.js'),
    Icon: path.resolve(__dirname, 'src/Icon/Icon.js'),
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[contenthash:8].js',
  },
  plugins: [
    new webpack.HashedModuleIdsPlugin(), // so that file hashes don't change unexpectedly
  ],
    ...
}
```

如果想要构建一个  name/name.js这样的结构

对output中的  filename：  [name]/[name].js 就可以生成 类似的目录结构了

## babel

### 1.babel到底做了什么 怎么做的

JavaScript 中 es2015/2016/2017/2046 的新语法转化为 es5，让低端运行环境(如浏览器和 node )能够认识并执行

### 2.babel的运行方式

总共分为三个阶段 

* 解析
* 转换
* 生成

**babel 本身不具有任何转化功能，它把转化的功能都分解到一个个 plugin 里面。因此当我们不配置任何插件时，经过 babel 的代码和输入是相同的。**

插件总共分为两种：

- 当我们添加 **语法插件** 之后，在解析这一步就使得 babel 能够解析更多的语法，比如callFoo(param1,param2,)这里第二个参数的逗号就是非法的

- 当我们添加 **转译插件** 之后，在转换这一步把源码转换并输出。这也是我们使用 babel 最本质的需求。

比起语法插件，转译插件其实更好理解，比如箭头函数 `(a) => a` 就会转化为 `function (a) {return a}`。

同一类语法可能同时存在语法插件版本和转译插件版本。**如果我们使用了转译插件，就不用再使用语法插件了**

### 3.配置文件

既然插件是 babel 的根本，那如何使用呢？总共分为 2 个步骤：

1. 将插件的名字增加到配置文件中 (根目录下创建 .babelrc 或者 package.json 的 `babel` 里面，格式相同)
2. 使用 `npm install babel-plugin-xxx` 进行安装

preset 分为以下几种：

- 官方内容，目前包括 env, react, flow, minify 等。这里最重要的是 env，后面会详细介绍。

- stage-x，这里面包含的都是当年最新规范的草案，每年更新。
  这里面还细分为

- - Stage 0 - 稻草人: 只是一个想法，经过 TC39 成员提出即可。
  - Stage 1 - 提案: 初步尝试。
  - Stage 2 - 初稿: 完成初步规范。
  - Stage 3 - 候选: 完成规范和浏览器初步实现。
  - Stage 4 - 完成: 将被添加到下一年度发布。

### 4.执行顺序

很简单的几条原则：

- Plugin 会运行在 Preset 之前。
- Plugin 会从前到后顺序执行。
- Preset 的顺序则 **刚好相反**(从后向前)。

### 5.env

env 的核心目的是通过配置得知目标环境的特点，然后只做必要的转换。例如目标浏览器支持 es2015，那么 es2015 这个 preset 其实是不需要的，于是代码就可以小一点(一般转化后的代码总是更长)，构建时间也可以缩短一些。

### 6.babel-loader配置

```js
module: {
  rules: [
    {
      test: /\.js$/,
      exclude: /(node_modules|bower_components)/,
      loader: 'babel-loader'
    }
  ]
}
//如果想在这里传入 babel 的配置项，也可以把改成：
// loader: 'babel-loader' 改成如下：
use: {
  loader: 'babel-loader',
  options: {
    // 配置项在这里
  }
}
```

