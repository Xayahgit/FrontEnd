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

