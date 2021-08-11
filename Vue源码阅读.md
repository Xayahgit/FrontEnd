## Vue源码阅读

### 1.1使Object数据变得可观测

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

### 1.2监听数组的变化

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

### 2.1虚拟DOM

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

### 2.2Vnode

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

其实`VNode`的作用是相当大的。我们在视图渲染之前，把写好的`template`模板先编译成`VNode`并缓存下来，等到数据发生变化页面需要重新渲染的时候，我们把数据发生变化后生成的`VNode`与前一次缓存下来的`VNode`进行对比，找出差异，然后有差异的`VNode`对应的真实`DOM`节点就是需要重新渲染的节点，最后根据有差异的`VNode`创建出真实的`DOM`节点再插入到视图中，最终完成一次视图更新。

### 2.3DOM-diff

**patch**

在`Vue`中，把 `DOM-Diff`过程叫做`patch`过程。patch,意为“补丁”，即指对旧的`VNode`修补，打补丁从而得到新的`VNode`

所谓旧的`VNode`(即`oldVNode`)就是数据变化之前视图所对应的虚拟`DOM`节点，而新的`VNode`是数据变化之后将要渲染的新的视图所对应的虚拟`DOM`节点，所以我们要以生成的新的`VNode`为基准，对比旧的`oldVNode`，如果新的`VNode`上有的节点而旧的`oldVNode`上没有，那么就在旧的`oldVNode`上加上去；如果新的`VNode`上没有的节点而旧的`oldVNode`上有，那么就在旧的`oldVNode`上去掉；如果某些节点在新的`VNode`和旧的`oldVNode`上都有，那么就以新的`VNode`为准，更新旧的`oldVNode`，从而让新旧`VNode`相同。

**以新的VNode为基准，改造旧的oldVNode使之成为跟新的VNode一样，这就是patch过程要干的事**。

patch的三件事

- **创建节点**：新的`VNode`中有而旧的`oldVNode`中没有，就在旧的`oldVNode`中创建。
- 删除节点：新的`VNode`中没有而旧的`oldVNode`中有，就从旧的`oldVNode`中删除。
- 更新节点：新的`VNode`和旧的`oldVNode`中都有，就以新的`VNode`为准，更新旧的`oldVNode`。

![创建节点](C:\Users\shiyifei01_sx\Desktop\FrontEnd\FrontEnd\2.02d5c7b1.png)

**删除节点**非常简单，只需在要删除节点的父元素上调用`removeChild`方法即可。源码如下

**更新节点**

![](C:\Users\shiyifei01_sx\Desktop\FrontEnd\FrontEnd\3.7b0442aa.png)

创建节点之后 更新的位置需要考究**合适的位置是所有未处理节点之前，而并非所有已处理节点之后**。

