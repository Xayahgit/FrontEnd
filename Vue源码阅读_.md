##  Vue源码阅读
  
  
###  1.1使Object数据变得可观测
  
  
我们定义一个数据对象`car`
  
``` js
let car = {
  'brand':'BMW',
  'price':3000
}
```
  
使用Object.defineProperty()改写:使得car该数据对象变得可观测
  
```js
let car = {}
let val = 3000
Object.defineProperty(car, 'price', {
  enumerable: true,
  configurable: true,
    //读写使用set() & get()去拦截
  get(){
    console.log('price属性被读取了')
    return val
  },
  set(newVal){
    console.log('price属性被修改了')
    val = newVal
  }
})
```
  
**依赖收集**
  
我们给每个数据都建一个依赖数组(因为一个数据可能被多处使用)，谁依赖了这个数据(即谁用到了这个数据)我们就把谁放入这个依赖数组中，那么当这个数据发生变化的时候，我们就去它对应的依赖数组中，把每个依赖都通知一遍，告诉他们："你们依赖的数据变啦，你们该更新啦！"。这个过程就是依赖收集。
  
###  1.2监听数组的变化
  
  
**Array型数据还是在getter中收集依赖。**
  
可以在原型上定义新方法：
  
``` js
Array.prototype.mypush = function(val) {
    console.log('数据被修改')
    this.push(val)
}
```
  
`Array`原型中可以改变数组自身内容的方法有7个，分别是：`push`,`pop`,`shift`,`unshift`,`splice`,`sort`,`reverse`。
  
**使数组变得可观测**
  
`Object`的变化时通过`setter`来追踪的，只有某个数据发生了变化，就一定会触发这个数据上的`setter`。但是`Array`型数据没有`setter`，怎么办？
  
我们试想一下，要想让`Array`型数据发生变化，那必然是操作了`Array`，而`JS`中提供的操作数组的方法就那么几种，我们可以把这些方法都重写一遍，在不改变原有功能的前提下，我们为其新增一些其他功能，例如下面这个例子：
  
```javascript
let arr = [1,2,3]
arr.push(4)
Array.prototype.newPush = function(val){
  console.log('arr被修改了')
  this.push(val)
}
arr.newPush(4)
```
  
在上面这个例子中，我们针对数组的原生`push`方法定义个一个新的`newPush`方法，这个`newPush`方法内部调用了原生`push`方法，这样就保证了新的`newPush`方法跟原生`push`方法具有相同的功能，而且我们还可以在新的`newPush`方法内部干一些别的事情，比如通知变化。
  
  
  
**数组方法拦截器**
  
经过整理，`Array`原型中可以改变数组自身内容的方法有7个，分别是：`push`,`pop`,`shift`,`unshift`,`splice`,`sort`,`reverse`。那么源码中的拦截器代码如下：
  
```javascript
// 源码位置：/src/core/observer/array.js
  
const arrayProto = Array.prototype
// 创建一个对象作为拦截器
export const arrayMethods = Object.create(arrayProto)
  
// 改变数组自身内容的7个方法
const methodsToPatch = [
  'push',
  'pop',
  'shift',
  'unshift',
  'splice',
  'sort',
  'reverse'
]
  
/**
 * Intercept mutating methods and emit events
 */
methodsToPatch.forEach(function (method) {
  const original = arrayProto[method]      // 缓存原生方法
  Object.defineProperty(arrayMethods, method, {
    enumerable: false,
    configurable: true,
    writable: true,
    value:function mutator(...args){
      const result = original.apply(this, args)
      return result
    }
  })
})
```
  
在上面的代码中，首先创建了继承自`Array`原型的空对象`arrayMethods`，接着在`arrayMethods`上使用`object.defineProperty`方法将那些可以改变数组自身的7个方法遍历逐个进行封装。最后，当我们使用`push`方法的时候，其实用的是`arrayMethods.push`，而`arrayMethods.push`就是封装的新函数`mutator`，也就后说，实标上执行的是函数`mutator`，而`mutator`函数内部执行了`original`函数，这个`original`函数就是`Array.prototype`上对应的原生方法。 那么，接下来我们就可以在`mutato`r函数中做一些其他的事，比如说发送变化通知。
  
**应该在Observer类中收集依赖**
  
**在dev.notify()方法达到通知依赖**
  
**数组变化侦测是通过拦截器实现的，也就是说只要是通过数组原型上的方法对数组进行操作就都可以侦测到**
  
###  2.1虚拟DOM
  
  
1. 什么是虚拟DOM？
  
   所谓虚拟DOM，就**是用一个`JS`对象来描述一个`DOM`节点**，像如下示例：
  
   ```javascript
   <div class="a" id="b">我是内容</div>
  
   {
     tag:'div',        // 元素标签
     attrs:{           // 属性
       class:'a',
       id:'b'
     },
     text:'我是内容',  // 文本内容
     children:[]       // 子元素
   }
   ```
  
   我们把组成一个`DOM`节点的必要东西通过一个`JS`对象表示出来，那么这个`JS`对象就可以用来描述这个`DOM`节点，我们把这个`JS`对象就称为是这个真实`DOM`节点的虚拟`DOM`节点。
  
在更新视图的时候难免要操作`DOM`,而操作真实`DOM`又是非常耗费性能的,我们可以用`JS`的计算性能来换取操作`DOM`所消耗的性能
  
我们可以用`JS`模拟出一个`DOM`节点，称之为虚拟`DOM`节点。当数据发生变化时，我们**对比变化前后的虚拟`DOM`节点**，通过`DOM-Diff`算法计算出需要更新的地方，然后去更新需要更新的视图。
  
###  2.2Vnode
  
  
`VNode`类中包含了描述一个真实`DOM`节点所需要的一系列属性，如`tag`表示节点的标签名，`text`表示节点中包含的文本，`children`表示该节点包含的子节点等。通过属性之间不同的搭配，就可以描述出各种类型的真实`DOM`节点
  
```javascript
export default class VNode {
  constructor (
    tag?: string,
    data?: VNodeData,
    children?: ?Array<VNode>,
    text?: string,
    elm?: Node,
    context?: Component,
    componentOptions?: VNodeComponentOptions,
    asyncFactory?: Function
  ) {
    this.tag = tag                                /*当前节点的标签名*/
    this.data = data        /*当前节点对应的对象，包含了具体的一些数据信息，是一个VNodeData类型，可以参考VNodeData类型中的数据信息*/
    this.children = children  /*当前节点的子节点，是一个数组*/
    this.text = text     /*当前节点的文本*/
    this.elm = elm       /*当前虚拟节点对应的真实dom节点*/
    this.ns = undefined            /*当前节点的名字空间*/
    this.context = context  
        ....
```
  
其实`VNode`的作用是相当大的。**我们在视图渲染之前，把写好的`template`模板先编译成`VNode`并缓存下来**，等到数据发生变化页面需要重新渲染的时候，我们把数据发生变化后生成的`VNode`与前一次缓存下来的`VNode`进行对比，找出差异，然后有差异的`VNode`对应的真实`DOM`节点就是需要重新渲染的节点，最后根据有差异的`VNode`创建出真实的`DOM`节点再插入到视图中，最终完成一次视图更新。
  
###  2.3DOM-diff
  
  
**patch**
  
在`Vue`中，把 `DOM-Diff`过程叫做`patch`过程。patch,意为“补丁”，即指对旧的`VNode`修补，打补丁从而得到新的`VNode`
  
所谓旧的`VNode`(即`oldVNode`)就是数据变化之前视图所对应的虚拟`DOM`节点，而新的`VNode`是数据变化之后将要渲染的新的视图所对应的虚拟`DOM`节点，所以我们要以生成的新的`VNode`为基准，对比旧的`oldVNode`，如果新的`VNode`上有的节点而旧的`oldVNode`上没有，那么就在旧的`oldVNode`上加上去；如果新的`VNode`上没有的节点而旧的`oldVNode`上有，那么就在旧的`oldVNode`上去掉；如果某些节点在新的`VNode`和旧的`oldVNode`上都有，那么就以新的`VNode`为准，更新旧的`oldVNode`，从而让新旧`VNode`相同。
  
**以新的VNode为基准，改造旧的oldVNode使之成为跟新的VNode一样，这就是patch过程要干的事**。
  
patch的三件事
  
- **创建节点**：新的`VNode`中有而旧的`oldVNode`中没有，就在旧的`oldVNode`中创建。
- 删除节点：新的`VNode`中没有而旧的`oldVNode`中有，就从旧的`oldVNode`中删除。
- 更新节点：新的`VNode`和旧的`oldVNode`中都有，就以新的`VNode`为准，更新旧的`oldVNode`。
  
![创建节点](C:/Users/shiyifei01_sx/Desktop/FrontEnd/FrontEnd/2.02d5c7b1.png )
  
**删除节点**非常简单，只需在要删除节点的父元素上调用`removeChild`方法即可。源码如下
  
**更新节点**
  
![](C:/Users/shiyifei01_sx/Desktop/FrontEnd/FrontEnd/3.7b0442aa.png )
  
创建节点之后 更新的位置需要考究**合适的位置是所有未处理节点之前，而并非所有已处理节点之后**。
  
###  3.1模板编译
  
  
你可以这么理解：**把用户写的模板进行编译，就会产生`VNode`。**
  
或者写一些`Vue`指令，如`v-on`、`v-if`等。而这些东西都是在原生`HTML`语法中不存在的，不被接受的。但是事实上我们确实这么写了，也被正确识别了，页面也正常显示了，这又是为什么呢？
  
这就归功于`Vue`的模板编译了，`Vue`会把用户在`<template></template>`标签中写的类似于原生`HTML`的内容进行编译，把原生`HTML`的内容找出来，再把非原生`HTML`找出来，经过一系列的逻辑处理生成渲染函数，也就是`render`函数，而`render`函数会将模板内容生成对应的`VNode`，而`VNode`再经过前几篇文章介绍的`patch`过程从而得到将要渲染的视图中的`VNode`，最后根据`VNode`创建真实的`DOM`节点并插入到视图中， 最终完成视图的渲染更新。
  
而把用户在`<template></template>`标签中写的类似于原生`HTML`的内容进行编译，把原生`HTML`的内容找出来，再把非原生`HTML`找出来，经过一系列的逻辑处理生成渲染函数，也就是`render`函数的这一段过程称之为模板编译过程
  
![](C:/Users/shiyifei01_sx/Desktop/FrontEnd/FrontEnd/1.f0570125.png )
  
###  3.2生成render函数
  
  
将一堆字符串模板解析成抽象语法树`AST`后，我们就可以对其进行各种操作处理了，处理完后用处理后的`AST`来生成`render`函数。其具体流程可大致分为三个阶段：
  
1. 模板解析阶段：将一堆模板字符串用正则等方式解析成抽象语法树`AST`；
2. 优化阶段：遍历`AST`，找出其中的静态节点，并打上标记；这样DOM-diff算法会直接跳过静态的节点，从而减少了比较的过程，优化了patch的性能
3. 代码生成阶段：将`AST`转换成渲染函数； AST => render（）
  
![](C:/Users/shiyifei01_sx/Desktop/FrontEnd/FrontEnd/3.15d9566b.png )
  
###  3.3总结
  
  
它的最终目的就是：把用户所写的**模板转化成**供`Vue`实例在挂载**时可调用的`render`函数**。或者你可以这样简单的理解为：模板编译就是一台机器，给它输入模板字符串，它就输出对应的`render`函数。
  
###  4.1生命周期--初始化阶段
  
  
new Vue()都干了什么：new的过程实际上是执行了Vue的构造函数
  
```javascript
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)
  ) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)  //构造函数核心代码
}
```
  
`new Vue()`会执行`Vue`类的构造函数，构造函数内部会执行`_init`方法，所以`new Vue()`所干的事情其实就是`_init`方法所干的事情，那么我们着重来分析下`_init`方法都干了哪些事情。
  
把`Vue`实例赋值给变量`vm`，并且把用户传递的`options`选项与当前构造函数的`options`属性及其父级构造函数的`options`属性进行合并（关于属性如何合并的问题下面会介绍），得到一个新的`options`选项赋值给`<img src="https://latex.codecogs.com/gif.latex?options`属性，并将`"/>options`属性挂载到`Vue`实例上
  
在上文中，`_init`方法里首先会调用`mergeOptions`函数来进行属性合并，如下：
  
这里值得一提的是 `mergeField` 函数，它不是简单的把属性从一个对象里复制到另外一个对象里，而是根据被合并的不同的选项有着不同的合并策略。例如，对于`data`有`data`的合并策略，即该文件中的`strats.data`函数；对于`watch`有`watch`的合并策略，即该文件中的`strats.watch`函数等等。这就是设计模式中非常典型的**策略模式**。
  
触发钩子函数
  
关于`callHook`函数如何触发钩子函数的问题，我们只需看一下该函数的实现源码即可，该函数的源码位于`src/core/instance/lifecycle.js` 中，如下：
  
```javascript
export function callHook (vm: Component, hook: string) {
  const handlers = vm.$options[hook]
  if (handlers) {
    for (let i = 0, j = handlers.length; i < j; i++) {
      try {
        handlers[i].call(vm)
      } catch (e) {
        handleError(e, vm, `${hook} hook`)
      }
    }
  }
}
```
  
  
  
可以看到，`callHook`函数逻辑非常简单。首先从实例的`<img src="https://latex.codecogs.com/gif.latex?options`中获取到需要触发的钩子名称所对应的钩子函数数组`handlers`，我们说过，每个生命周期钩子名称都对应了一个钩子函数数组。然后遍历该数组，将数组中的每个钩子函数都执行一遍。分析了`new%20Vue()`时其内部都干了些什么。其主要逻辑就是：合并配置，调用一些初始化函数，触发生命周期钩子函数，调用`"/>mount`开启下一个阶段。
  
  
  
**initEvents：**
  
回到之前的模板编译中  遇到开始标签后会开始解析开始的标签
  
还会调用`processAttrs` 方法解析标签中的属性，`processAttrs` 方法位于源码的
  
**initState**：
  
在`Vue`组件中会写一些如`props`、`data`、`methods`、`computed`、`watch`选项，我们把这些选项称为实例的状态选项。也就是说，`initState`函数就是用来初始化这些状态的
  
给实例上新增了一个属性`_watchers`，用来存储当前实例中所有的`watcher`实例，无论是使用`vm.<img src="https://latex.codecogs.com/gif.latex?watch`注册的`watcher`实例还是使用`watch`选项注册的`watcher`实例，都会被保存到该属性中。所以从`Vue%202.0`版本起，`Vue`不再对所有数据都进行侦测，而是将侦测粒度提高到了组件层面，对每个组件进行侦测，所以在每个组件上新增了`vm._watchers`属性，用来存放这个组件内用到的所有状态的依赖，当其中一个状态发生变化时，就会通知到组件，然后由组件内部使用虚拟`DOM`进行数据比对，从而降低内存开销，提高性能。**watch**```js%20%20watch:%20{%20%20%20%20a:%20function%20(val,%20oldVal)%20{%20%20%20%20%20%20console.log(&#x27;new:%20%s,%20old:%20%s&#x27;,%20val,%20oldVal)%20%20%20%20},%20%20%20%20&#x2F;&#x2F;%20methods选项中的方法名%20%20%20%20b:%20&#x27;someMethod&#x27;,%20%20%20%20&#x2F;&#x2F;%20深度侦听，该回调会在任何被侦听的对象的%20property%20改变时被调用，不论其被嵌套多深%20%20%20%20c:%20{%20%20%20%20%20%20handler:%20function%20(val,%20oldVal)%20{%20&#x2F;*%20...%20*&#x2F;%20},%20%20%20%20%20%20deep:%20true%20%20%20%20},%20%20%20%20&#x2F;&#x2F;%20该回调将会在侦听开始之后被立即调用%20%20%20%20d:%20{%20%20%20%20%20%20handler:%20&#x27;someMethod&#x27;,	%20%20immediate:%20true%20%20%20%20},```初始化顺序：props%20=&gt;methods=&gt;data=&gt;computed=&gt;watch###%20%204.2模板编译阶段该阶段所做的主要工作是获取到用户传入的模板内容并将其编译成渲染函数。从用户传入的`el`选项和`template`选项中获取到用户传入的内部或外部模板，然后将获取到的模板编译成渲染函数。###%20%204.3挂载阶段要工作是创建`Vue`实例并用其替换`el`选项对应的`DOM`元素，同时还要开启对模板中数据（状态）的监控，当数据（状态）发生变化时通知其依赖进行视图更新。在mounted阶段接下来创建了一个`Watcher`实例，并将定义好的`updateComponent`函数传入。要想开启对模板中数据（状态）的监控，这一段代码是关键，如下：```javascriptnew%20Watcher(%20%20%20%20vm,%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20&#x2F;&#x2F;%20第一个参数%20%20%20%20updateComponent,%20%20%20%20%20%20%20&#x2F;&#x2F;%20第二个参数%20可以读到getter和setter进行依赖缓存%20%20%20%20noop,%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20&#x2F;&#x2F;%20第三个参数%20%20%20%20{%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20&#x2F;&#x2F;%20第四个参数%20%20%20%20%20%20%20%20before%20()%20{%20%20%20%20%20%20%20%20%20%20if%20(vm._isMounted)%20{%20%20%20%20%20%20%20%20%20%20%20%20callHook(vm,%20&#x27;beforeUpdate&#x27;)%20%20%20%20%20%20%20%20%20%20}%20%20%20%20%20%20%20%20}	},%20%20%20%20true%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20&#x2F;&#x2F;%20第五个参数)```如果是函数，会执行这个函数。一旦读取了数据或者执行了函数，就会触发数据或者函数内数据的`getter`方法，而在`getter`方法中会将`watcher`实例添加到该数据的依赖列表中，当该数据发生变化时就会通知依赖列表中所有的依赖，依赖接收到通知后就会调用第四个参数回调函数去更新视图。上面代码中把`updateComponent`函数作为第二个参数传给`Watcher`类从而创建了`watcher`实例，**那么`updateComponent`函数中**读取的所有数据都将被`watcher`所监控，这些数据中只要有任何一个发生了变化，那么`watcher`都将会得到通知，从而会去调用第四个参数回调函数去更新视图，如此反复，直到实例被销毁。###%20%205.1数据相关的方法分别是`vm."/>set`、`vm.<img src="https://latex.codecogs.com/gif.latex?delete`和`vm."/>watch`。
  
```js
export default class Watcher {
    constructor (/* ... */) {
        // ...
        this.value = this.get()
    }
    get () {
		// ...
        // "touch" every property so they are all tracked as
      	// dependencies for deep watching
        if (this.deep) {
            traverse(value)
        }
        return value
    }
}
```
  
还记得我们在介绍数据变化侦测的时候说过，对于`object`型数据，当我们向`object`数据里添加一对新的`key/value`或删除一对已有的`key/value`时，`Vue`是无法观测到的；而对于`Array`型数据，当我们通过数组下标修改数组中的数据时，`Vue`也是是无法观测到的
  
**nextTick方法**
  
Vue对DOM的更新策略：
  
`Vue` 在更新 `DOM` 时是**异步**执行的。只要侦听到数据变化，`Vue` 将开启一个事件队列，并缓冲在同一事件循环中发生的所有数据变更。如果同一个 `watcher` 被多次触发，只会被推入到事件队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 `DOM` 操作是非常重要的。然后，在下一个的事件循环“tick”中，`Vue` 刷新事件队列并执行实际 (已去重的) 工作。
  
###  6.1全局API
  
  
Vue.use(plugin)
  
安装 Vue.js 插件。如果插件是一个对象，必须提供 `install` 方法。如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 `Vue` 作为参数传入。
  
###  7.1自定义指令
  
  
一种是使用全局API——`Vue.directive`来定义全局指令，这种方式定义的指令会被存放在`Vue.options['directives']`中；另一种是在组件内的`directive`选项中定义专为该组件使用的局部指令，这种方式定义的指令会被存放在`vm.$options['directives']`中。
  
写法：
  
```js
// 注册一个全局自定义指令 `v-focus`
Vue.directive('focus', {
    // 当被绑定的元素插入到 DOM 中时……
    inserted: function (el) {
        // 聚焦元素
        el.focus()
    }
})
```
  
```html
<input v-focus>
```
  
**<keep-alive>原理**
  
在 `created` 钩子函数里定义并初始化了两个属性： `this.cache` 和 `this.keys`。
  
```javascript
created () {
    this.cache = Object.create(null)
    this.keys = []
}
```
  
  
  
`this.cache`是一个对象，用来存储需要缓存的组件，它将以如下形式存储：
  
```javascript
this.cache = {
    'key1':'组件1',
    'key2':'组件2',
    // ...
}
```
  
`this.keys`是一个数组，用来存储每个需要缓存的组件的`key`，即对应`this.cache`对象中的键值。
  
**destroy**
  
当`<keep-alive>`组件被销毁时，此时会调用`destroyed`钩子函数，在该钩子函数里会遍历`this.cache`对象，然后将那些被缓存的并且当前没有处于被渲染状态的组件都销毁掉并将其从`this.cache`对象中剔除。
  