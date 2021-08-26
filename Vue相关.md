# Vue相关面试题

## Vue的基本使用

#### **Class 与 Style 如何动态绑定？**

Class 和 Style 均可以通过对象语法和数组语法进行动态绑定。

- 对象语法：·

```·
<div v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>
data: {  isActive: true,  hasError: false}
```

- 数组语法：
·
```
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
data: {  activeClass: 'active',  errorClass: 'text-danger'}
```

- 对象语法：

```
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
data: {  activeColor: 'red',  fontSize: 30}
```

- 数组语法：

```
<div v-bind:style="[styleColor, styleSize]"></div>
data: {  styleColor: {     color: 'red'   },  styleSize:{     fontSize:'23px'  }}
```

#### computed 和 watch 的区别和应用场景

- computed：是计算属性，**依赖于其他的属性值**，**具有缓存**，只有依赖发生改变，下一次获取该computed值才会触发重新计算computed的值
- watch：主要是**观察**的作用，类似用某些数据的**监听回调**，每当监听的数据发生变化就会触发相应的监听回调函数

**使用场景**：

- 需要依赖某一个值进行数值运算时使用computed，可以利用到缓存的特性，避免每次获取该计算值时进行了重复的计算
- 当我们需要再某个数据变化是执行**异步操作或者开销较大的操作**时，应该使用watch。使用 watch 选项允许我们执行**异步操作** ( 访问一个 API )，**限制我们执行该操作的频率**，并在我们得到最终结果前，设置**中间状态**。这是计算属性无法做到的。

**注意**：

- 在data中没有直接声明出要计算的变量，也可以直接在computed中写入。
- 如果在data中没有相应的属性的话，是不能watch的

#### vue自定义组件v-model双向绑定

**使用场景**：

vue组件使用 v-model 时可以定制 prop 和 event。**默认情况下，一个组件上的 v-model 会把 value 用作 prop 且把 input 用作 event**，但是一些输入类型比如单选框和复选框按钮可能想使用 value prop 来达到不同的目的。使用 model 选项可以回避这些情况产生的冲突。 **例子**:

```
Vue.component('my-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    // this allows using the `value` prop for a different purpose
    value: String,
    // use `checked` as the prop which take the place of `value`
    checked: {
      type: Number,
      default: 0
    }
  },
  // ...
})
```

#### 具名插槽和作用域插槽的区别

**具名插槽**：

**使用场景**：有时我们需要组件提供多个插槽，向特定的插槽传入特定的内容。

**使用方式**：`<slot>` 元素有一个特殊的 attribute：`name`。这个 attribute 可以用来定义额外的插槽：

```
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

在向具名插槽提供内容的时候，我们可以在一个 元素上使用 v-slot 指令，并以 v-slot 的参数的形式提供其名称：

```
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

**作用域插槽**：

**使用场景**：父组件通过插槽内容访问子组件中才有的数据

**使用方式**：

1. 将子组件的某数据，如 `user`， 通过 `<slot>` 元素的一个 attribute 绑定上去：

```
<span>
  <slot v-bind:user="user">
    {{ user.lastName }}
  </slot>
</span>
```

1. 绑定在 `<slot>` 元素上的 attribute 被称为**插槽 prop**。在父级作用域中，可以使用带值的 `v-slot` 来定义提供的插槽 prop 的名字，如slotProps：

```
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

**两者区别**：

1. 两者都是父组件通过来使用子组件提供的插槽，作用域插槽使用的是`v-slot:default`，具名插槽使用的是`v-slot:具名插槽名`。
2. 作用域插槽的作用是获取子组件的数据，具名插槽的作用是将特定内容在子组件的特定位置渲染。

#### 谈谈对keep-alive的理解

keep-alive是一个Vue内置的组件，功能是使插槽内的组件**保留状态，避免重新渲染**，有以下的特性：

- 一般**结合路由和动态组件一起使用**，用于**缓存组件**，状态保留在内存中；
- 提供 **include** 和 **exclude** 属性，两者都支持字符串或正则表达式， include 表示只有名称匹配的组件会被缓存，exclude 表示任何名称匹配的组件都不会被缓存 ，**其中 exclude 的优先级比 include 高**；
- 对应**两个钩子函数 activated 和 deactivated** ，当组件被激活时，触发钩子函数 activated，当组件被移除时，触发钩子函数 deactivated。

```
<!-- 基本 -->
<keep-alive>
  <component :is="view"></component>
</keep-alive>

<!-- 多个条件判断的子组件 -->
<keep-alive>
  <comp-a v-if="a > 1"></comp-a>
  <comp-b v-else></comp-b>
</keep-alive>

<!-- 和 `<transition>` 一起使用 -->
<transition>
  <keep-alive>
    <component :is="view"></component>
  </keep-alive>
</transition>
```

**注意**：

`<keep-alive>` 是用在其一个直属的子组件被开关的情形。如果你在其中有 `v-for` 则不会工作。如果有上述的多个条件性的子元素，`<keep-alive>` 要求同时只有一个子元素被渲染。

**keep-alive的原理**

在具体实现上，keep-alive在内部维护了**一个key数组和一个缓存对象**

```
created () {
    this.cache = Object.create(null);
    this.keys = [];
}
```

- **key数组用于记录目前缓存的组件key值**，如果组件没有指定key值，会自动生成一个唯一的key值
- **cache对象会以key值为键，vnode为值，用于缓存组件对应的虚拟DOM**

> 在keep-alive的渲染函数中，其基本逻辑是判断当前渲染的vnode是否有对应的缓存，如果有，会从缓存中读取到对应的组件实例，如果没有就会把它缓存。

当缓存的数量超过max设置的数值时，keep-alive会移除key数组中的第一个元素

```
    const slot = this.$slots.default; //获取默认插槽
    const vnode = getFirstComponentChild(slot); //得到插槽中第一个组件的vnode
    const name = getComponentName(vnode.componentOptions); //获取组件名字
   
    const { cache, keys } = this; //获取当前的混村内对象和key数组
    const key: ?string = vnode.key == null
        ? componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')
        : vnode.key; //获取组件的key值，如果没有key值，会按照规则自动生成
      if (cache[key]) {
      //有缓存
      //重用组件实例
        vnode.componentInstance = cache[key].componentInstance    
        remove(keys, key); //删除key值
        //将key值加入到数组末尾，这样是为了保证最近使用的组件在数组中靠后，主要是为了方便设置的max值删除很久没使用的组件
        keys.push(key)
      } else {
      //没有缓存的则进行缓存
        cache[key] = vnode
        keys.push(key)
        // prune oldest entry
        if (this.max && keys.length > parseInt(this.max)) {
        //超过最大缓存数量，移除第一个key对应的缓存
          pruneCacheEntry(cache, keys[0], keys, this._vnode)
        }
      }
 
      vnode.data.keepAlive = true
    }
    return vnode || (slot && slot[0])
  }
```

#### 何时使用异步组件

**什么是异步组件：**

定义的时候什么都不做，**只在组件需要渲染**（组件第一次显示）**的时候进行加载渲染并缓存**，缓存是以备下次访问。（需要渲染的时候才**异步加载**并**缓存**）

**使用场景：**

- 加载大组件（编辑器和图表等）
- 路由异步加载（切换路由时才进行加载）
- 前端的优化（将页面核心功能（音、视频播放、文章、商品等等）打包成一个核心模块，通过框架优先加载。 其他的一些周边功能打包后，通过服务器异步加载，从而解决业务需求越来越多导致的系统难维护、访问慢问题。）

**具体使用方式：**

- vue实现**按需加载的方式**：官方推荐使用**结合webpack的代码分割功能**进行。定义为异步加载的组件，在打包的时候，会**打包成单独的js文件**存储在static/js文件夹里面，在**调用时使用ajax请求回来插入到html**中。

- **webpack的代码分割实现方式**：

  - **AMD规范**的引入方式：resolve是全部引入成功以后的回调函数，第一个参数是依赖，require会先引入依赖模块，再执行回调函数。

    ```
    {
    	path: '/',
    	name: 'Helloworld',
    	component: resolve => require(['@/components/HelloWorld'], resolve)
    }
    ```

  - **ES6模块化规范**的引入方式：webpack > 2.4。import()方法是动态加载，返回一个Promise对象，then方法的参数是加载到的模块。类似于Node.js的require方法，主要import()方法是异步加载的。

    ```
    const HelloWorld = () => import("@/components/HelloWorld");
    ```

    在路由配置的时候直接调用HelloWorld就可以了。

    import是一个运算符，返回一个Promise，模块加载完成，则Promise resolve vue组件。

    好像就是定义了一个函数，函数不调用的时候就不执行啊。只要在什么地方调用了，才会执行。在路由配置的时候调用，才会去执行，去加载这个模块。

  - **多个路由指定相同的chunkName**的引入方式

    指定为相同chunkName的组件会打包成一个js文件。vue官网推荐使用webpack的代码分割进行懒加载。此方法依赖于Promise。

    ```
    {
        path: '/',
    	name: 'HelloWorld',
    	component: r => require.ensure([], () => r(require('../components/HelloWorld')), 'Helloworld' );
    }
    ```

    webpack编译时，会静态的解析代码中的require.ensure()，同时将模块添加到一个分开的chunk中，新的chunk会被webpack通过jsonp来按需加载。此方法内部依赖于Promise。

    参数1：是字符串数组，模块的依赖，会提前加载。一般都是空的。

    参数2：依赖项加载完成之后的回调函数

    所有的依赖加载完成之后，webpack会执行这个回调函数，require对象的一个实现会作为一个参数传递给回调函数，因此，可以require依赖和其他模块提供下一步的执行。

    参数3：chunk名称

    相同chunk名称的文件 所有依赖都会被放进相同文件束。

#### 自定义指令

## Vue组件相关

#### Vue的单向数据流怎么理解

所有的 prop 都使得其父子 prop 之间形成了一个**单向下行**绑定：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。**每次父级组件发生更新时，子组件中所有的 prop 都将会刷新为最新的值**。

**意义**：**防止从子组件意外改变父级组件的状态**，从而导致你的应用的数据流向难以理解。

**反问**：如果需要改变prop？

1. 定义一个本地的 data 属性并将这个 prop 用作其初始值：

```
props: ['initialCounter'],
data: function () {
return {    
		counter: this.initialCounter
	}
}
```

1. 这个 prop 以一种原始的值传入且需要**进行转换**。 在这种情况下，最好使用这个 prop 的值来定义一个计算属性

```
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```

#### 对Vue生命周期的理解

每个Vue实例都有一个完整的生命周期，包括从**开始创建->初始化数据->编译模板->挂载DOM->渲染->更新-> 渲染->卸载**等一系列过程。

| 生命周期      | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| beforeCreate  | 组件实例被创建之初，组件的属性生效之前                       |
| created       | 组件实例已经完全创建，属性也绑定，但真实 dom 还没有生成，**$el 还不可用** |
| beforeMount   | 在挂载开始之前被调用：相关的 render 函数首次被调用           |
| mounted       | el 被新创建的 vm.$el **替换**，并挂载到实例上去之后调用该钩子 |
| beforeUpdate  | 组件数据更新之前调用，发生在虚拟 DOM 打补丁之前              |
| update        | 组件数据更新之后                                             |
| activited     | keep-alive 专属，组件被**激活**时调用                        |
| deactivated   | keep-alive 专属，组件被**销毁**时调用                        |
| beforeDestory | 组件销毁前调用（扫地僧一般的角色）                           |
| destoryed     | 组件销毁后调用                                               |

**何时使用beforeDestory？**

- 解绑自定义事件event.$off
- 清除定时器
- 解绑自定义的DOM事件，如window scroll等

#### **Vue 的父组件和子组件生命周期钩子函数执行顺序？**

> 加载渲染过程

**父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate** -> 子 created -> 子 beforeMount -> 子 mounted -> 父 mounted

#### **在哪个生命周期内调用异步请求？**

在钩子函数 created、beforeMount、mounted 中都可，因为data 已经创建，可以将服务端端返回的数据进行赋值。

在 created 钩子函数中调用异步请求有以下优点：

- 能更快获取到服务端数据，减少页面 loading 时间；
- ssr 不支持 beforeMount 、mounted 钩子函数，所以放在 created 中有助于一致性；

#### **组件中 data 为什么是一个函数？**

- 确保同一个组件的不同实例的数据是不同的，利用闭包的特性
- 定义的`.vue`文件是一个类，每个地方去使用这个组件的时候**实际上是创建这个类的实例**，**使用同一个组件的时候实际上是复用同一个Vue实例**
- **因为组件是用来复用的**，**也就会创建多个实例**，**本质上，这些实例用的都是同一个构造函数**。JS 里对象是**引用关系**，如果组件中 data 是一个对象，那么这样作用域没有隔离，**子组件中的 data 属性值会相互影响**；如果组件中 data 选项是一个函数，那么每个实例可以**维护一份被返回对象的独立的拷贝**，组件实例之间的 data 属性值不会互相影响。
- **而 new Vue 的实例，是不会被复用的，因此不存在引用对象的问题。**

#### Vue组件间通信有哪几种方式

Vue间组件主要有3类通信方式：**父子组件通信、隔代通信、兄弟组件通信**

> props / $emit 适用于**父子组件通信**

> ref 与 parent/parent/children 适用于**父子组件通信**

- ref：如果在**普通的 DOM 元素**上使用，引用指向的就是 DOM 元素；如果用在**子组件**上，引用就指向组件实例
- parent/parent/children：访问**父 / 子实例**
- （不推荐通过这两种方式修改data，会有警告）

> EventBus (emit/emit/on) 适用于**父子、隔代、兄弟组件通信**

通过一个**空的Vue实例**作为**中央事件总线**（事件中心），用他来**触发事件**和**监听事件**，从而实现任何组件间的通信。

1. 新建一个js文件，来创建出我们的eventBus，我们把它命名为bus.js

   ```
   import Vue from 'vue';  
   export default new Vue(); 
   ```

2. 触发一个事件：

   ```
   methods: {  
      addCart(event) {  
      		Bus.$emit('getTarget', event.target);   
      }  
   }  
   ```

3. 在show组件中的created()钩子中调用bus监听这个事件，并接收参数：

   ```
   created() {  
       Bus.$on('getTarget', target => {  
           console.log(target);  
       });  
   }  
   ```

> attrs/attrs/listeners 适用于**隔代组件通信**

- attrs：包含了父作用域中不被prop所识别(且获取)的特性绑定(class和style除外)。当一个组件没有声明任何prop时，这里会包含所有父作用域的绑定(class和style除外)，并且可以通过v−bind="attrs：包含了父作用域中不被prop所识别(且获取)的特性绑定(class和style除外)。当一个组件没有声明任何prop时，这里会包含所有父作用域的绑定(class和style除外)，并且可以通过v−bind="attrs" 传入内部组件。通常配合 inheritAttrs 选项一起使用。
- listeners：包含了父作用域中的(不含.native修饰器的)v−on事件监听器。它可以通过v−on="listeners：包含了父作用域中的(不含.native修饰器的)v−on事件监听器。它可以通过v−on="listeners" 传入内部组件

[详见](https://www.jianshu.com/p/a388d38f8c69)

> provide / inject 适用于**隔代组件通信**

祖先组件中通过 **provider 来提供变量**，然后在子孙组件中通过 **inject 来注入变量**。provide / inject API 主要解决了跨级组件间的通信问题，不过它的使用场景，**主要是子组件获取上级组件的状态**，跨级组件间建立了一种**主动提供与依赖注入的关系**。

使用provide传递当前组件实例

```
provide(){
    return{
        test:this
    }
},
```

在后代组件中接收组件

```
inject:['test'],
mounted(){
    console.log('---')
    console.log(this.test)
},
computed:{
    myzz(){ //使用计算属性动态监听上(n)级组件的某个属性变化
        return this.test.zz
    }
},
methods:{  //更改属性，同时我们的计算属性也会得到更新
    changeProvide(){
        this.test.zz = 'changed'
    }
},
```

> Vuex 适用于**父子、隔代、兄弟组件通信**

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。每一个 Vuex 应用的核心就是 store（仓库）。“store” 基本上就是一个容器，它包含着你的应用中大部分的状态 ( state )。

- Vuex 的**状态存储是响应式的**。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
- **改变 store 中的状态的唯一途径就是显式地提交** (commit) mutation。这样使得我们可以**方便地跟踪每一个状态的变化**。

主要包含下列五个模块：

- State：定义了应用状态的**数据结构**，可以在这里**设置默认的初始状态**。
- Getter：允许组件从 Store 中获取数据，mapGetters 辅助函数仅仅是**将 store 中的 getter 映射到局部计算属性**。
- Mutation：是**唯一更改 store 中状态的方法**，且必须是**同步函数**。
- Action：用于**提交 mutation**，而不是直接变更状态，可以**包含任意异步操作**。
- Module：允许**将单一的 Store 拆分为多个 store** 且**同时保存在单一的状态树中**。

#### 如何将组件所有的props传递给子组件

使用$props语法指代组件所有的props

具体使用方式：

```
<User v-bind="$props" />
```

#### 多组件有相同逻辑，如何抽离

混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个混入对象可以包含任意组件选项。**当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项**。

- 选项合并（当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。）

  - **数据对象**在内部会进行**递归合并**，并在**发生冲突时以组件数据优先**。
  - **同名钩子函数将合并为一个数组**，因此都将被调用。另外，混入对象的钩子将在组件自身钩子**之前**调用。
  - **值为对象的选项**，例如 `methods`、`components` 和 `directives`，将**被合并为同一个对象**。两个对象键名冲突时，取组件对象的键值对。
  - 混入**也可以进行全局注册**。使用时格外小心！一旦使用全局混入，它将影响**每一个**之后创建的 Vue 实例。

- mixin的一些缺点（容易乱）

  - 命名冲突

  - 隐含的依赖关系(mixin和使用它的组件之间没有层次关系。组件里的变量名称修改后，mixin里没改)

  - ```
    那具体哪个好，就得看具体框架和具体场景
    
    比如 class，HOC 比较合适
    
    比如 function，hooks 就比较合适，因为本质上 hooks 是利用了函数的闭包
    
    比如 object，尤其是生命周期，就很适合 mixin
    ```

#### 组件渲染和更新过程

分为3大部分，第一部分是响应式，监听数据变化，通过observer对数据的getter和setter进行设置；第二部分是complie，模板渲染部分，生成virtual tree，并触发touch，对监听数据的getter和setter进行设置，在getter中收集依赖到watch中，数据setter的触发会通知watcher执行相应的re-render函数；第三部分是watcher，作为一个中枢，接收到observer发来的通知，并执行compile中相应的更新方法

**组件首次渲染**： obeserver触发响应式，通过设置getter和setter劫持监听数据变化；complie将模板解析为render函数，向watcher收集相关依赖，订阅数据的变化，并绑定更新函数；执行render函数，生成vnode，然后patch(el, vnode)

**组件更新过程**：

修改 data， 触发 setter （此前在getter中已被监听）；watcher根据修改数据的订阅依赖关系，重新执行相应 render 函数，生成 newVnode，patch(vnode, newVnode)

#### 为什么在v-for中使用key

1. 为了提高Vue的渲染效率必须使用key，而且不能是index或者random树
2. diff在比较同层级节点的时候需要用到tag和key来提高比较的效率，是否是sameNode
3. 目的是提高渲染效率，减少渲染时间

## Vuex 和 vue-router相关

#### 有使用过Vuex吗

参考Vue组件通信的Vuex

#### Vuex 中 action 和 mutation 有啥区别

- mutation 中对 store 中的状态进行**原子操作**
- action 中可以进行**异步操作**，mutation中不可以
- action 中可以整合**多个** mutation

#### Vue-router的路由模式有哪几种

vue-router 有 3 种路由模式：**hash、history、abstract**，对应的源码如下所示：

```
switch (mode) {
  case 'history':
  this.history = new HTML5History(this, options.base)
  break
  case 'hash':
  this.history = new HashHistory(this, options.base, this.fallback)
  break
  case 'abstract':
  this.history = new AbstractHistory(this, options.base)
  break
  default:
  if (process.env.NODE_ENV !== 'production') {
    assert(false, `invalid mode: ${mode}`)
  }
}
```

- hash: 使用 **URL hash** 值来作路由。**支持所有浏览器**，包括不支持 HTML5 History Api 的浏览器；
- history : 依赖 **HTML5 History API** 和服务器配置。（对浏览器版本有要求，不支持低版本浏览器）具体可以查看 HTML5 History 模式；
- abstract : 支持**所有 JavaScript 运行环境**，如 Node.js 服务器端。**如果发现没有浏览器的 API，路由会自动强制进入这个模式**.

#### hash 和 history 路由模式的实现原理

> hash路由的实现模式

基于`location.hash`实现，`location.hash`的值是URL中`#`后面的内容，如`https://www.word.com#search`

hash 路由模式的实现主要是基于下面**几个特性**：

- URL 中 hash 值只是**客户端的一种状态**，也就是说当向服务器端发出请求时，hash 部分不会被发送；
- hash 值的**改变**，都会在浏览器的**访问历史中增加一个记录**。因此我们能**通过浏览器的回退、前进按钮控制hash 的切换**；
- 我们可以**使用 `hashchange` 事件来监听 hash 值的变化**，从而对页面进行跳转（渲染）。

触发 hash 值**改变的方式**：

- 通过 a 标签，并**设置 href 属性**，当用户点击这个标签后，URL 的 hash 值会发生改变；
- **使用 JavaScript 来对`location.hash` 进行赋值**，改变 URL 的 hash 值；

[详见](https://developer.mozilla.org/zh-CN/docs/Web/API/Location)

简易**实现原理**：

我们先定义一个父类 `BaseRouter`，用于实现 `Hash` 路由和 `History` 路由的一些共有方法；

```
export class BaseRouter {
  // list 表示路由表
  constructor(list) {
    this.list = list;
  }
  // 页面渲染函数
  render(state) {
    let ele = this.list.find(ele => ele.path === state);
    ele = ele ? ele : this.list.find(ele => ele.path === '*');
    ELEMENT.innerText = ele.component;
  }
}
```

我们简单实现了 `push` 压入功能、`go` 前进/后退功能，相关代码的注释都已经标上，简单易懂，就不在一 一介绍，参见如下：

```
export class HashRouter extends BaseRouter {
  constructor(list) {
    super(list);
    this.handler();
    // 监听 hashchange 事件
    window.addEventListener('hashchange', e => {
      this.handler();
    });
  }
  // hash 改变时，重新渲染页面
  handler() {
    this.render(this.getState());
  }
  // 获取 hash 值
  getState() {
    const hash = window.location.hash;
    return hash ? hash.slice(1) : '/';
  }
  // push 新的页面
  push(path) {
    window.location.hash = path;
  }
  // 获取 默认页 url
  getUrl(path) {
    const href = window.location.href;
    const i = href.indexOf('#');
    const base = i >= 0 ? href.slice(0, i) : href;
    return base +'#'+ path;
  }
  // 替换页面
  replace(path) {
    window.location.replace(this.getUrl(path));
  }
  // 前进 or 后退浏览历史
  go(n) {
    window.history.go(n);
  }
}
```

> history路由的实现模式

HTML5提供了`History API`来实现URL的变化。其中最重要的是`history.pushState()` 和 `history.repalceState()`两个API。借助这两个API，可以实现在**不进行刷新的情况下操作浏览器的历史记录**，区别是，前者是**新增一个历史记录**，后者是直接**替换当前的历史记录**，如

```
window.history.pushState(null, null, path);
window.history.replaceState(null, null, path);
```

history 路由模式的实现主要基于存在下面**几个特性**：

- `pushState` 和 `repalceState` 两个 API 来操作**实现 URL 的变化** ；
- 我们可以使用 `popstate` 事件来监听 url 的变化（限于用户点击浏览器的前进、后退操作和手动调用history的back、forward和go方法），从而**对页面进行跳转**（渲染）；
- `history.pushState()` 或 `history.replaceState() `不会触发 popstate 事件，这时我们**需要手动触发页面跳转（渲染）**。

[详见](https://developer.mozilla.org/zh-CN/docs/Web/API/History)

简易**实现原理**：

```
export class HistoryRouter extends BaseRouter {
  constructor(list) {
    super(list);
    this.handler();
    // 监听 popstate 事件
    window.addEventListener('popstate', e => {
      console.log('触发 popstate。。。');
      this.handler();
    });
  }
  // 渲染页面
  handler() {
    this.render(this.getState());
  }
  // 获取 url 
  getState() {
    const path = window.location.pathname;
    return path ? path : '/';
  }
  // push 页面
  push(path) {
    history.pushState(null, null, path);
    this.handler();
  }
  // replace 页面
  replace(path) {
    history.replaceState(null, null, path);
    this.handler();
  }
   // 前进 or 后退浏览历史
  go(n) {
    window.history.go(n);
  }
}
```

> 两者比较

| 对比点 | Hash 模式               | History 模式                     |
| ------ | ----------------------- | -------------------------------- |
| 美观性 | 带着 # 字符，较丑       | 简洁美观                         |
| 兼容性 | >= ie 8，其它主流浏览器 | >= ie 10，其它主流浏览器         |
| 实用性 | 不需要对服务端做改动    | 需要服务端对路由进行相应配合设置 |

## Vue原理

#### **直接给一个数组项赋值，Vue 能检测到变化吗？**

因为JS的限制，Vue中不能检测到以下方式的数组变动：

- 当你**利用索引直接设置一个数组项**时，例如：`vm.items[indexOfItem] = newValue`
- 当你**修改数组的长度**时，例如：`vm.items.length = newLength`

第一个问题的解决方式：使用`Vue.set`或者`Vue.splice` API，避免直接操作数组本身

```
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
// vm.$set，Vue.set的一个别名
vm.$set(vm.items, indexOfItem, newValue)
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

第二个问题的解决方式：使用`Vue.splice` API来设置长度

```
// Array.prototype.splice
vm.items.splice(newLength)
```

#### Vue是如何实现数据的双向绑定的

Vue 数据双向绑定主要是指：数据变化更新视图，视图变化更新数据

其中，**View 变化更新 Data ，可以通过事件监听的方式来实现**，所以 Vue 的数据双向绑定的工作主要是**如何根据 Data 变化更新 View**。（响应式数据对象本身发生变化，会触发绑定了该对象的视图发生相应的改变）

**vue.js 采用数据劫持结合发布-订阅模式,通过 Object.defineproperty 来劫持各个属性的 setter,getter,在数据变动时发布消息给订阅者,触发响应的监听回调**，主要是由下面三大核心部分来实现的：

![image-20210319114747190](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210319114747190.png)

**核心实现类:**

Observer（数据响应式模块） : 它的作用是给对象的属性添加 getter 和 setter，用于**依赖收集**和**派发更新**，实现方式有`defineProperty` 和 `Proxy` 两种方式。

> Vue3为什么使用`Proxy`代替`defineProperty`
>
> - `defineProperty` 的方式需要对实现响应式的对象进行一次递归到底和遍历的方式进行配置操作，对性能的要求高。`Proxy`无需递归和遍历操作便可以劫持整个对象,并返回一个新的对象
> - `defineProperty` 无法监听数组元素的变化，只能给一些数组的api方法（push();pop();shift();unshift();splice();sort();reverse();）上加上数据更新的方法实现响应式。`Proxy`可以代理数组。
> - `defineProperty` 无法监听对象动态增加和删除的属性变化，`Proxy`可以。
> - `Proxy`无法通过polyfill实现兼容性

Dep : 用于**收集当前响应式对象的依赖关系**,每个响应式对象包括子对象都拥有一个 Dep 实例（里面 subs 是 Watcher 实例数组）,当数据有变更时,会通过 dep.notify()通知各个 watcher。

> - 创建的时机：初始化时对data属性进行数据劫持时创建
> - 数量：和data属性的数量一一对应，包括属性的嵌套属性
> - 结构：
>   - id（唯一标识）
>   - subs（记录被依赖的n个watcher容器）

Watcher : **观察者对象** , 实例分为**渲染 watcher** (render watcher),**计算属性 watcher** (computed watcher),**侦听器 watcher**（user watcher）（即vue中的watcher）三种

> - 创建时机：初始化时解析模板的大括号表达式 / 解析指令时
> - 数量：和模板表达式一一对应
> - 结构：
>   - cb（用于更新页面的回调函数）
>   - vm（订阅的响应式对象）
>   - exp（对应的表达式）
>   - depIds（依赖的n的dep实例）
>   - value（当前模板表达式对应的值）

**派发更新的实现**:

1. 组件中对响应式数据进行了修改，触发setter方法的执行
2. setter中会调用dep.notify方法
3. 该方法遍历subs的所有订阅者（Watcher实例），调用每个Watcher的update方法

**简单的响应式原理示例**

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
        // vue构造函数的大致流程
const Vue = function(options) {
    const self = this;

    // 将data赋值给this._data,源码这部分用的是Proxy，这里采用简单的方式实现
    if (options && typeof options.data === 'function') {
        this._data = options.data.apply(this);
    }

    // 数据响应式处理，监听_data
    observe(this._data);

    // 挂载函数
    this.mount = function () {
        new Watcher(self, self.render);
    },

    // 渲染函数，模拟模板解析后会触发读取text的操作
    this.render = function () {
        with (self) {
            return (_data.text + _data.laji); 
        }
    }
}

// 定义一个observer
function observe (data) {
    if (typeof data !== 'object') return;
    // 这里主要功能是循环遍历data的属性并劫持该属性
    const keys = Object.keys(data);
    keys.forEach(item => {
        reactive(data, item);
    })
}

// 定义observer需要的reactive属性
function reactive (obj, key) {
    // 若该key为对象则递归响应式该key
    observe(obj[key]);

    // 实例化一个dep实例，供 getter 和 setter 使用
    let dep = new Dep();

    // 获取当前属性值
    let value = obj[key];

    Object.defineProperty(obj, key, {
        enumerable: true,
        configurable: true,
        get () {
            console.log('in get', key);
            dep.depend();
            return value;
        },
        set (newValue) {
            console.log('in set', key);
            if (newValue !== value) {
                dep.notify();
                value = newValue;
            }
            return;
        }
    });
}

// 实现一个依赖收集器
const Dep = function () {
    const self = this;

    // 依赖收集器
    this.subs = [];
}

// 依赖目标
Dep.target = null;

Dep.prototype = {
    // 搜集依赖的方法
    depend: function () {
        if (Dep.target) {
            Dep.target.addDep(this);
        }
    },
    // 收集器添加watcher的方法
    addWatcher: function (data) {
        this.subs.push(data);
    },
    // 订阅信息派发方法
    notify: function () {
        this.subs.forEach(item => {
            item.update();
        })
    }
}

// fn 为渲染函数
const Watcher = function (vm, fn) {
    const self = this;
    this.vm = vm;

    // 将Dep.target指向自己
    Dep.target = this;

    // 运行渲染函数，会触发相应的get函数
    this.value = fn();

    // 清空target，防止重复绑定
    Dep.target = null;
}

Watcher.prototype = {
    // 向相应的dep添加自己作为依赖
    addDep: function (dep) {
        console.log(dep)
        dep.addWatcher(this);
    },

    // 更新函数
    update: function () {
        console.log('in Update');
    }
}

const vue = new Vue({
    data() {
        return {
            text: 'hello world',
            laji: 'world'
        };
    }
})
vue.mount();
window.vue = vue;
  
// vue.mount(); // in get
    </script>
</body>
</html>
```

##### 监听器 Observer 的实现

```
/**
  * 循环遍历数据对象的每个属性
  */
function observable(obj) {
    if (!obj || typeof obj !== 'object') {
        return target;
    }
    let keys = Object.keys(obj);
    keys.forEach((key) => {
        defineReactive(obj, key, obj[key])
    })
    return obj;
}
/**
 * 将对象的属性用 Object.defineProperty() 进行设置
 */
function defineReactive(obj, key, val) {
    // 定义一个 Dep 对象
    const dep = new Dep();
    
    // 深度监听
    observable(val);
    
    Object.defineProperty(obj, key, {
        get() {
            console.log(`${key}属性被读取了...`);
            // 这里会进行依赖收集，关联到watcher的操作
            // dep.depend();
            return val;
        },
        set(newVal) {
            if (newVal !== val) {
                console.log(`${key}属性被修改了...`);
                
                // 设置新值的时候也需要深度监听，有可能新值也是一个对象类型的val
                observable(newVal);
                
            	val = newVal;
                // 触发更新视图，感知更新，通知 watcher 去调用更新函数
                updateView();
            }
        }
    })
}
```

- 深度监听，需要递归到底，一次性计算量大（data扁平化）
- 无法监听对象属性的新增和删除（Vue.set Vue.delete）

##### 订阅器 Dep 实现

订阅器采用 **发布 - 订阅** 的设计模式

**优点：**

- 发布-订阅模式广泛应用于**异步编程**中，这是一种**替代传递回调函数**的方案，比如，我们可以订阅 ajax 请求的 error 、succ 等事件。在异步编程中使用发布-订阅模式， 我们就无需过多关注对象在异步运行期间的内部状态，而只需要订阅感兴趣的事件发生点。

- 发布-订阅模式可以取代对象之间硬编码的通知机制，**一个对象不用再显式地调用另外一个对象的某个接口**。发布-订阅模式让两个对象松耦合地联系在一起，**虽然不太清楚彼此的细节，但这不影响它们之间相互通信**。当有新的订阅者出现时，发布者的代码不需要任何修改；同样发布者需要改变时，也不会影响到之前的订阅者。只要之前约定的事件名没有变化，就 可以自由地改变它们。

- 在 Dep 内部，会维护一个 watcher 队列。

  depend 方法在每次 getter 触发时都会把 watcher 实例和 dep 实例做一次关联。

  在 setter 触发时，dep 实例便会逐个通知每一个和自己有关联的 watcher：我对应的属性发生了更新！进而调度 watcher 实例的 update 方法，实现视图更新。

订阅器的实现详见手撕代码部分

```
function Dep () {
    this.subs = [];
}
Dep.prototype = {
    // 将 watcher 实例添加到 subs中（这个方法在 Watcher 类的实现中会用到）
    addSub: function(sub: Watcher) {
        this.subs.push(sub);
    }，
    
    // 通知 watcher 对象发生更新
    notify: function() {
        this.subs.forEach(function(sub) {
            sub.update();
        });
    }
};
Dep.target = null;
```

将 `defineReactive` 函数进行改造一下，向其植入订阅器：

```
defineReactive: function(data, key, val) {
	var dep = new Dep();
	Object.defineProperty(data, key, {
		enumerable: true,
		configurable: true,
		get: function getter () {
            // 第一次触发 get 会对事件进行订阅
			if (Dep.target) {
				dep.addSub(Dep.target);
			}
			return val;
		},
		set: function setter (newVal) {
			if (newVal === val) {
				return;
			}
			val = newVal;
			dep.notify();
		}
	});
}
```

`Dep` 类，该类里面定义了一些属性和方法，这里需要特别注意的是它有一个静态属性 `Dep.target`，这是一个全局唯一的`Watcher`，因为在同一时间只能有一个全局的 `Watcher` 被计算，另外它的自身属性 `subs` 也是 `Watcher` 的数组。

##### 订阅者 Watch 实现

订阅者 `Watcher` 在**初始化的时候需要将自己添加进订阅器 `Dep` 中**，那该如何添加呢？

- 在订阅者 `Watcher` 初始化的时候触发对应的 `get` 函数去执行添加订阅者操作（监听器`Observer` 是在 get 函数执行了添加订阅者 Wather 的操作的）
- 只有在订阅者 `Watcher` 初始化的时候才需要添加订阅者，所以需要做一个判断操作，因此可以在订阅器上做一下手脚：在 `Dep.target` 上缓存下订阅者，添加成功后再将其去掉就可以了。

订阅者 `Watcher` 的实现如下：

```
function Watcher(vm, exp, cb) {
    this.vm = vm;
    this.exp = exp;
    this.cb = cb;
    this.value = this.get();  // 将自己添加到订阅器的操作
}

Watcher.prototype = {
    update: function() {
        this.run();
    },
    run: function() {
        var value = this.vm.data[this.exp];
        var oldVal = this.value;
        if (value !== oldVal) {
            this.value = value;
            this.cb.call(this.vm, value, oldVal);
        }
    },
    get: function() {
        Dep.target = this; // 将自己赋值为全局的订阅者
        var value = this.vm.data[this.exp]  // 强制执行监听器里的get函数
        Dep.target = null; // 全局变量 订阅者 释放
        return value;
    }
};
```

订阅者 `Watcher` 是一个 类，在它的构造函数中，定义了一些属性：

- **vm：\**一个 Vue 的\**实例对象**；
- **exp：\**是 `node` 节点的 `v-model` 等指令的属性值 或者插值符号中的\**属性**。如 `v-model="name"`，`exp` 就是`name`;
- **cb：\**是 `Watcher` 绑定的\**更新函数**;

实例化 `Watcher` 的流程：

1. 进入 `watcher` 的构造函数逻辑，就会执行它的 `this.get()` 方法，进入 `get` 函数，将自己赋值为全局的订阅者 `Dep.target`
2. 访问当前 `vm` 绑定的 `exp` 数据，其实就是为了触发数据对象的 `getter`（每个对象值的 `getter` 都持有一个 `dep`，在触发 `getter` 的时候会调用 `dep.depend()` 方法，也就会执行`this.addSub(Dep.target)`，即把当前的 `watcher` 订阅到这个数据持有的 `dep` 的 `watchers` 中，这个目的是为后续数据变化时候能通知到哪些 `watchers` 做准备）
3. 把 `Dep.target` 恢复成上一个状态
4. 订阅器在数据发生改变的时候调用该`watcher`的`update`方法。(`update()` 函数是用来当数据发生变化时调用 `Watcher` 自身的更新函数进行更新的操作。先通过 `let value = this.vm.data[this.exp];` 获取到最新的数据,然后将其与之前 `get()` 获得的旧数据进行比较，如果不一样，则调用更新函数 `cb` 进行更新)

##### 解析器 compile 实现

解析器 `Compile` 实现步骤：

- 解析模板指令，并替换模板数据，初始化视图；
- 将模板指令对应的节点绑定对应的更新函数，初始化相应的订阅器；

下面是对 '{{变量}}' 这种形式的指令处理的关键代码进行分析，关键代码如下：

```
compileText: function(node, exp) {
	var self = this;
	var initText = this.vm[exp]; // 获取属性值
	this.updateText(node, initText); // dom 更新节点文本值
    // 将这个指令初始化为一个订阅者，后续 exp 改变时，就会触发这个更新回调，从而更新视图
	new Watcher(this.vm, exp, function (value) { 
		self.updateText(node, value);
	});
}
```

#### **Vue 框架怎么实现对数组的监听**

Object.defineProperty() 只能对属性进行数据劫持，不能对整个对象进行劫持，同理**无法对数组进行劫持**

监听数组需要重新定义数组的原型，注意不能直接更改所有数组的原型（防止污染全局的原型），需要复制一个原数组的原型，在复制的原型上的数组操作方法加上视图更新函数，然后将监听的数组的原型换成这个新的原型。

```
//重新定义数组原型
const oLdArrayProperty = Array.prototype
//创建新对象，原型指向0 ldArrayProperty ，再扩展新的方法不会影响原型
const arrProto = object.create(oldArrayProperty);
['push', 'pop', 'shift', 'unshift', 'splice'].forEach(methodName => {
	arrProto[methodName] = function () {
		updateView() // 触发规图更新
		oldArrayProperty[methodName].call(this, ...arguments)
	}
})
// 监听数据类型为数组则将其原型换成带更新函数的新数组原型
/**
  * 循环遍历数据对象的每个属性
  */
function observable(obj) {
    if (!obj || typeof obj !== 'object') {
        return target;
    }
    if (Array.isArray(target)) target.__proto__ = arrProto;
    let keys = Object.keys(obj);
    keys.forEach((key) => {
        defineReactive(obj, key, obj[key])
    })
    return obj;
}
```

#### nextTick 原理

当数据更新发生时，它不会立刻给你执行视图层的更新动作。而是先把这个更新给“存起来”，等到“时机成熟”再执行它；这个“存起来”的地方，叫做**异步更新队列**；**即便一个 watcher 被多次触发，它也只会被推进异步更新队列一次**。在同步逻辑执行完之后，watcher 对应的就是其依赖属性的最新的值。最后，Vue 会**把异步更新队列**的动作集体出队，**批量更新**。

这个实现异步任务派发的接口，就叫做 “nextTick”。

**Vue数据更新后的事件循环逻辑**

- 第一次tick：（数据更新循环）

  - 首先是Vue模板依赖的数据修改，这一步是同步任务。同一个事件循环内的同步代码在这里形成执行栈，这时还没有进行DOM的更新操作
  - Vue为依赖数据的修改开启一个更新的异步队列，缓存本次事件循环中的所有事件变化。本质上是将依赖数据的watcher更新函数推入到异步任务队列中去，注意同一个watcher被触发多次，只会被推进到队列中一次
  - 以上皆为同步任务（宏任务）中的范畴
  - 同步任务执行完毕，开始执行异步 watcher 队列的任务，更新 DOM 。Vue 在内部尝试对异步队列使用原生的 Promise.then 和 MessageChannel 方法，如果执行环境不支持，会采用 setTimeout(fn, 0) 代替。

- 第二次 tick：

  此时就是文档所说的

  > 下次 DOM 更新循环结束之后

  开始执行异步 watcher 队列的任务之后，此时通过 Vue.nextTick 将传递的异步回调函数统一推送到异步队列中，则异步回调函数在下一轮事件循环执行时可以拿到上一轮DOM渲染完成后的数据了

**nextTick源码**：

> 实际上，Vue 非常“懒”，它并没有自己去实现和维护一套异步队列逻辑，而是完全依赖于浏览器暴露的 api 接口来实现异步任务的派发。

```
import { noop } from 'shared/util'
import { handleError } from './error'
import { isIE, isIOS, isNative } from './env'

export let isUsingMicroTask = false

const callbacks = []
let pending = false

function flushCallbacks () {
  pending = false
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}

// 用来派发异步任务的函数
let timerFunc


// 下面这一段逻辑，是根据浏览器的不同，选择不同的 api 来派发异步任务
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
} else if (!isIE && typeof MutationObserver !== 'undefined' && (
  isNative(MutationObserver) ||
  MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else {
  // Fallback to setTimeout.
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}

// 暴露 nextTick 方法
export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  // 维护一个异步更新队列
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  // pending 是一个锁，确保任务执行的有序性
  if (!pending) {
    pending = true
    timerFunc()
  }
  // 兜底逻辑，处理入参不是回调的情况
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}
```

在上面这段代码里，有三个关键角色：

- nextTick
- timerFunc
- flushCallbacks

##### **逻辑统筹者——nextTick**:

关注三个变量：

- callbacks：异步更新队列
- pending：锁
- timerFunc：异步任务的派发函数

**nextTick 的入参是一个回调函数，这个回调函数就是一个“任务”。**每次 nextTick 接收一个任务，它不会立刻去执行它，而是把它 push 进 callbacks 这个异步更新队列里。接着，去检查 pending 的值。

pending 为 false，意味着“现在还没有一个异步更新队列被派发出去”，那么就调用 timerFunc，把当前维护的这个异步队列给派发出去；那如果 pending 为 true 呢？意味着现在异步更新队列（callbacks）已经被派发出去了，此时 callbacks 已经呆在浏览器的异步任务队列里、确保会被执行了，因此没有必要再执行一遍 timerFunc 去重复派发这个队列，只需要往里面添加任务就可以了。

##### 异步任务派发器——timerFunc

这一整坨的代码里，其实有不少是在**处理浏览器的兼容性**，细节上不必深究。这里我给大家把关键逻辑抽离为伪代码，其实事情很简单：

```
if(当前环境支持 Promise){
    Promise.then 派发 timerFunc
} else if (当前环境支持 MutationObserver) {
        MutationObserver 派发 timerFunc
} else if (当前环境支持 setImmediate) {
    setImmediate 派发 timerFunc
} else {
    setTimeout 派发 timer Func
}
```

我们看到，timerFunc 按照优先级分别可能通过：`Promise.then`、`MutationObserver`、`setImmediate`、`setTimeout` 四种途径派发。这个优先级比较有看头，大家会发现它是**优先派发 micro-task、次选 macro-task**。

##### 任务执行器——flushCallbacks

负责**把 callbacks 里的任务逐个取出，依次执行**。

注意，进入 flushCallbacks 后，做的第一件事情就是**把 pending 置为 false**。因为 flushCallbacks 执行完毕后，callbacks 将被清空、浏览器的异步任务队列中也就没有 Vue 的异步任务了。此时必须把 pending 置空，确保下一个 Vue 异步任务队列进来时，可以及时被派发。

#### **Proxy 与 Object.defineProperty 优劣对比**

[Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy])的优势如下:

- Proxy 可以**直接监听对象**而非属性；
- Proxy 可以**直接监听数组**的变化；
- Proxy 有多达 13 种拦截方法,不限于 apply、ownKeys、deleteProperty、has 等等是 Object.defineProperty 不具备的；
- Proxy **返回的是一个新对象**,我们可以只操作新的对象达到目的,而 Object.defineProperty 只能遍历对象属性直接修改；
- Proxy 作为新标准将受到浏览器厂商重点持续的性能优化，也就是传说中的新标准的性能红利；

Object.defineProperty 的优势如下:

- 兼容性好，支持 IE9，而 Proxy 的存在浏览器兼容性问题,而且无法用 polyfill 磨平，因此 Vue 的作者才声明需要等到下个大版本( 3.0 )才能用 Proxy 重写。

#### **Vue 怎么用 vm.$set() 解决对象新增属性不能响应的问题 ？**

受现代 JavaScript 的限制 ，Vue 无法检测到对象属性的添加或删除。由于 Vue 会在初始化实例时对属性执行 getter/setter 转化，所以属性必须在 data 对象上存在才能让 Vue 将它转换为响应式的。

```
export function set (target: Array<any> | Object, key: any, val: any): any {
  // target 为数组  
  if (Array.isArray(target) && isValidArrayIndex(key)) {
    // 修改数组的长度, 避免索引>数组长度导致splcie()执行有误
    target.length = Math.max(target.length, key)
    // 利用数组的splice变异方法触发响应式  
    target.splice(key, 1, val)
    return val
  }
  // key 已经存在，直接修改属性值  
  if (key in target && !(key in Object.prototype)) {
    target[key] = val
    return val
  }
  const ob = (target: any).__ob__
  // target 本身就不是响应式数据, 直接赋值
  if (!ob) {
    target[key] = val
    return val
  }
  // 对属性进行响应式处理
  defineReactive(ob.value, key, val)
  ob.dep.notify()
  return val
}
```

我们阅读以上源码可知，vm.$set 的实现原理是：

- 如果目标是**数组**，直接**使用数组的 splice 方法**触发相应式；
- 如果目标是**对象**，会**先判读属性是否存在、对象是否是响应式**，最终如果要对属性进行响应式处理，则是通过调用 defineReactive 方法进行响应式处理（ defineReactive 方法就是 Vue 在初始化对象时，给对象属性采用 Object.defineProperty 动态添加 getter 和 setter 的功能所调用的方法）

#### **Vue 中的 key 有什么作用？**

key 是为 Vue 中 **vnode 的唯一标记**，通过这个 key，我们的 **diff 操作可以更准确、更快速**。

Vue 的 diff 过程可以概括为：oldCh 和 newCh 各有两个头尾的变量 oldStartIndex、oldEndIndex 和 newStartIndex、newEndIndex，它们会新节点和旧节点会进行两两对比，即一共有4种比较方式：newStartIndex 和oldStartIndex 、newEndIndex 和 oldEndIndex 、newStartIndex 和 oldEndIndex 、newEndIndex 和 oldStartIndex，如果以上 4 种比较都没匹配，如果设置了key，就会用 key 再进行比较，在比较的过程中，遍历会往中间靠，一旦 StartIdx > EndIdx 表明 oldCh 和 newCh 至少有一个已经遍历完了，就会结束比较。具体有无 key 的 diff 过程，可以查看作者写的另一篇详解虚拟 DOM 的文章《深入剖析：Vue核心之虚拟DOM》

所以 Vue 中 key 的作用是：key 是为 Vue 中 vnode 的唯一标记，通过这个 key，我们的 diff 操作可以更准确、更快速

更准确：因为**带 key 就不是就地复用了**，在 sameNode 函数 a.key === b.key 对比中可以避免就地复用的情况。所以会更加准确。

更快速：**利用 key 的唯一性生成 map 对象来获取对应节点，比遍历方式更快**，源码如下：

```
function createKeyToOldIdx (children, beginIdx, endIdx) {
  let i, key
  const map = {}
  for (i = beginIdx; i <= endIdx; ++i) {
    key = children[i].key
    if (isDef(key)) map[key] = i
  }
  return map
}
```

#### vdom 和 diff 的相关原理

##### diff 算法是什么

从**必要性、执行方式、高效性**三方面进行阐述：（https://www.bilibili.com/video/BV1G7411N7Ef?from=search&seid=497696501350346913）

1. 必要性：组件中可能存在很多个data中的key使用

2. 执行方式：patchVnode是diff发生的地方，整体策略：**深度优先，同层比较**（在这之前会进行patch比较，patch算法的三种结果详细看下面解析）

3. 高效性：

   1. 深度优先、同层比较：虽说新旧vnode在比较时采用同层比较的方式，但在层级比较的选择上会采用深度优先的策略：在进行本层比较时，当前比较的新旧两个节点，若是同一个节点，则先不进行同层比较，进行深度的比较，比较这个节点的新旧孩子，比较完当前节点的孩子节点的差异后，再回到本层进行其他节点的比较

   2. 同一个节点的比较方式：(patchVnode逻辑)

      1. 都有孩子则比较孩子节点
      2. 一方有孩子，另一方无孩子的情况，进行相应的新增节点和删除节点的操作（孩子节点和文本节点）
      3. 都是文本节点，则更新节点

   3. 同层节点的比较方式：(updateChildren逻辑) 详细请看（

      https://www.bilibili.com/video/BV1b5411V7i3/?spm_id_from=333.788.recommend_more_video.1）

      1. 注意有key和无key的比较区别
      2. 先进行：头头，尾尾、头尾、尾头比较4种方式比较节点，来找出更新前后相同的节点相同，如果前4种查找方式有节点没有找到，则采用遍历的方式，在老节点数组中查找新节点，若还是没有则进行添加操作，找到则进行相应的位置更新操作
      3. 同层节点比较后，会有一些剩下的节点需要处理，可能是批量的新增或者删除

   4. patch -> patchVnode -> updateChildren -> patchVnode

![image-20210722105719577](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210722105719577.png)

一句话总结：用 JS 模拟 DOM 结构（vnode）；新旧 vnode 对比（diff），得到最小的更新范围，最后更新 DOM；数据驱动视图的模式下，有效控制 DOM 操作

> 注意：以下是一些细节部分的零碎拼凑，主要看上面部分的为主

##### 为什么需要 Virtual DOM

三方面的需求：

1. 前端性能优化：为了尽可能少地操作DOM，我们需要这样一层抽象，在patch过程中尽可能地一次性将差异更新到DOM中，以保证DOM操作不会出现性能很低的情况。
2. 现代主流框架的一个基本要求是无需手动操作DOM，一方面是手动操作DOM无法保证程序性能，另一方面是省略手动操作DOM可以大大提高开发效率
3. 虚拟DOM最初的目的是为了更好的一个跨平台的特性，比如Node.js就没有DOM,如果想实现SSR(服务端渲染)，那么一个方式就是借助Virtual DOM，因为Virtual DOM本身是JavaScript对象。

##### 虚拟dom的优缺点

- **保证性能下限**： 框架的虚拟 DOM 需要适配任何上层 API 可能产生的操作，它的一些 DOM 操作的实现必须是普适的，所以它的性能并不是最优的；但是**比起粗暴的 DOM 操作性能要好很多**，因此框架的虚拟 DOM 至少可以保证**在你不需要手动优化的情况下，依然可以提供还不错的性能**，即保证性能的下限；
- **无需手动操作 DOM**： 我们不再需要手动去操作 DOM，**只需要写好 View-Model 的代码逻辑**，框架会根据虚拟 DOM 和 数据双向绑定，帮我们以可预期的方式更新视图，极大**提高我们的开发效率**；
- **跨平台**： 虚拟 DOM **本质上是 JavaScript 对象**,而 DOM 与平台强相关，相比之下虚拟 DOM 可以进行**更方便地跨平台操作**，例如服务器渲染、weex 开发等等。

缺点

- **无法进行极致优化**： 虽然虚拟 DOM + 合理的优化，足以应对绝大部分应用的性能需求，但在一些性能要求极高的应用中虚拟 DOM 无法进行针对性的极致优化。

##### 虚拟dom的文章详见《[深入剖析：Vue核心之虚拟DOM](https://juejin.cn/post/6844903895467032589)》

##### 用 JS 模拟 DOM 结构

![image-20210514111211547](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210514111211547.png)

JS 模拟的 DOM 结构主要包含下列几部分

- tag：标签名
- props：节点属性
- children：节点的子节点信息
- text：节点的文本信息

##### 结合 snabbdom 库来理解 vdom 的使用，snabbdom 库的几个关键 API，流程

```
// 定义h
const h = snabbdom.h
const container = document.getELementById('container')

// 生成 vnode
const vnode = h('ul#list', {}, [
	h('li.item', {}, 'Item1'),
	h('li.item', {}, 'Item2')
)]
patch(container, vnode)

document.getELementById('btn-change').addEventListener('click', () => {
//生成newVnode
	const newVnode = h( 'ul#list', {}, [
        h('li.item', {}, 'Item 1'),
        h('li.item', {}, 'Item B'),
        h('li.item', {}, 'Item 3')
    ])
    patch(vnode, newVnode)
})
```

1. **h函数**，根据 JS 模拟DOM节点的节点信息生成 vnode

2. **vnode数据结构**（用 JS 模拟的节点）

3. patch函数

   （两个参数）

   - （挂载元素，vnode）：Patch into empty DOM element
   - （vnode，newVnode）：Patch invocation
   - （newVnode， null）：unmount from the DOM and clean up

**diff 算法的优化**

- 传统diff算法
  - ![image-20210722122952257](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMgAAADICAAAAACIM/FCAAACh0lEQVR4Ae3ch5W0OgyG4dt/mQJ2xgQPzJoM1m3AbALrxzrf28FzsoP0HykJEEAAAUQTBBBAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQAABBBBAAAEEkKK0789+GK/I2ezfQB522PnS1qc8pGgXvr4tE4aY0XOUWlGImThWgyCk6DleixzE7qwBkg/MGiDPlVVAyp1VQGrPKiACDhFI6VkF5LmzCki+sg7IwDoglnVAil0IMkeG9CyUiwsxLFUVFzJJOQaKCjFCDN9RXMjIX7W6ztZXZDKKCyn8sWJvH+nca7WHDN9lROlAliPH9iRKCPI4cswFJQWxB46toLQgQ9jhn5QYZA9DOkoMUoQde5YapAxDWkoNYsOQR3KQd9CxUnIQF4S49CB9ENKlBxmDEKsFUgMCCCCAAHIrSF61f6153Ajy8nyiPr8L5MXnmm4CyT2fzN4DUvHZ+ntA2tOQBRBAAAEEEEAAAQQQ7ZBaC6TwSiDUaYHQ2yuB0MN+ft+43whyrs4rgVCjBUKTFshLC6TUAjGA3AxSaYFYLZBOC2RUAsk8h5qTg9QcbEoOsoQhQ2qQhsO5xCD5dgB5JQaZ+KBKGtKecvR81Ic0ZDjByKdDx0rSEDZ/djQbH+bkIdvfJFm98BfV8hD2zprfVdlu9PxVeyYAkciREohRAplJCaRSAplJCcQogTjSAdlyHRBvSAekJR0QRzogA+mADJkOiCPSAPEtqYBshlRAXC43hxix2QiOuEZkVERykGyNo9idIZKE0HO7XrG6OiMShlDWjstVzdPgXtUH9v0CEidAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQP4HgjZxTpdEii0AAAAASUVORK5CYII=)
- 只比较同一层级，不跨级比较
- tag 不相同，则直接删掉重建，不再深度比较
- tag 和 key 都相同，则认为是相同节点，不再深度比较

**diff 算法的源码**

> h 函数

![image-20210516201224690](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210516201224690.png)

接收参数

- sel 元素的标签名
- data 元素的属性
- children 元素的子元素

![image-20210516201615709](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210516201615709.png)

返回结果

包含 sel，data，children，text，elm 和 key 信息的vnode

elm 为 vnode 更新对应的 dom 元素（绑定的dom元素），key 类似循环中使用的 key

> patch 函数

![image-20210516202024040](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210516202024040.png)

接收参数：

oldVnode：可以是 VNode，也可以是元素类型，元素类型则为挂载的真实dom元素

vnode：Vnode类型

可能的几种传参形式对应的处理方式

1. 第一个参数不是 vnode，是一个 dom 元素，则创建一个空的节点作为 oldVode，关联到这个 DOM 元素

![image-20210529170456251](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210529170456251.png)

1. 第一个参数 oldVnode 和第二个参数 node 是相同的 vnode，则执行 patchVnode 命令对比改 vnode 的变化

**相等的定义（key 和 sel 都相等）**

![image-20210529170733443](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210529170733443.png)

![image-20210529170840409](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210529170840409.png)

1. 第一个参数 oldVnode 和第二个参数 node 是不同的 vnode，直接删除重建

![image-20210529171204156](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210529171204156.png)

> patchNode 函数

1. 执行pre hook

2. 设置新vnode的elm

3. 暂存新旧vnode的children数据，方便调用

4. hook执行相关

5. 开始对比新旧vnode的差异

   1. 新 vnode 没有 text，一般就是指新 vnode 有 children

   ![image-20210725152816104](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725152816104.png)

   1. 新 vnode 的 text 有值，一般就是指新 vnode 没有 children，且新旧 vnode 的 text 还不相同

   ![image-20210725151723682](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725151723682.png)

   删除该元素的子节点和更新text节点

> updateChildren 函数

1. 先定义新旧children数组头和尾四个指针

![image-20210725153220052](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725153220052.png)

1. 进行：头头，尾尾、头尾、尾头比较4种方式比较节点，来找出更新前后相同的节点相同，如果找到相同的节点，则对相同的节点进行patchNode处理

![image-20210725154821481](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725154821481.png)

1. 如果前4种查找方式有节点没有找到，则采用遍历的方式，在老节点数组中查找新节点，若还是没有则进行添加操作，找到则进行相应的位置更新操作

![image-20210725154924799](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725154924799.png)

1. 同层节点比较后，会有一些剩下的节点需要处理，可能是批量的新增或者删除

![image-20210725155044091](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725155044091.png)

> 关于为什么 v-for 中需要指定 key 的问题

![image-20210725154711335](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725154711335.png)

## 框架特性

#### 说说你对 SPA 单页面的理解，它的优缺点分别是什么？

**定义：**SPA：（ single-page application ）

**特点：** **仅在 Web 页面初始化时**加载相应的 HTML、JavaScript 和 CSS。**一旦页面加载完成**，SPA 不会因为用户的操作而进行页面的重新加载或跳转；取而代之的是利用**路由机制**实现 HTML 内容的变换，UI 与用户的交互，**避免页面的重新加载**。

**优点：**

- **用户体验好、快**，内容的改变不需要重新加载整个页面，避免了不必要的跳转和重复渲染；
- 基于上面一点，SPA 相对**对服务器压力小**；
- **前后端职责分离，架构清晰**，前端进行交互逻辑，后端负责数据处理；

**缺点：**

- 初次加载耗时多：为实现单页 Web 应用功能及显示效果，需要在加载页面的时候将 JavaScript、CSS 统一加载，部分页面按需加载；**首屏加载问题**
- 前进后退路由管理：由于单页应用在一个页面中显示所有的内容，所以**不能使用浏览器的前进后退功能**，所有的**页面切换需要自己建立堆栈管理**；
- **SEO 难度较大**：由于所有的内容都在一个页面中动态替换显示，所以在 SEO 上其有着天然的弱势。

#### 使用过 Vue SSR 吗？说说 SSR？(服务端渲染)

> Vue.js 是构建**客户端应用程序**的框架。默认情况下，可以在浏览器中输出 Vue 组件，进行生成 DOM 和操作 DOM。然而，也可以将**同一个组件渲染为服务端的 HTML 字符串**，将它们直接发送到浏览器，最后将这些静态标记"激活"为客户端上完全可交互的应用程序。
>
> 即：**SSR**大致的意思就是vue在客户端将标签渲染成的整个 html 片段的工作在**服务端**完成，**服务端形成的html 片段直接返回给客户端**这个过程就叫做**服务端渲染**。

服务端渲染的优点：

- **更好的 SEO**：因为 **SPA 页面的内容是通过 Ajax 获取**，而搜索**引擎爬取工具并不会等待** Ajax 异步完成后再抓取页面内容，所以在 SPA 中是抓取不到页面通过 Ajax 获取到的内容；而 **SSR 是直接由服务端返回已经渲染好的页面（数据已经包含在页面中）**，所以搜索引擎爬取工具可以抓取渲染好的页面；
- 更快的内容到达时间（**首屏加载更快**）：SPA 会等待所有 Vue 编译后的 js 文件都下载完成后，才开始进行页面的渲染，文件下载等需要一定的时间等，所以首屏渲染需要一定的时间；SSR 直接由服务端渲染好页面直接返回显示，无需等待下载 js 文件及再去渲染等，所以 **SSR 有更快的内容到达时间**；

服务端渲染的缺点：

- 更多的**开发条件限制**：例如服务端渲染**只支持 beforCreate 和 created** 两个钩子函数，这会导致一些**外部扩展库需要特殊处理**，才能在服务端渲染应用程序中运行；并且与可以部署在任何静态文件服务器上的完全静态单页面应用程序 SPA 不同，服务端渲染应用程序，需要**处于 Node.js server 运行环境**；
- **更多的服务器负载**：在 Node.js 中渲染完整的应用程序，显然会比仅仅提供静态文件的 server 更加大量占用CPU 资源 (CPU-intensive - CPU 密集)，因此如果你预料在高流量环境 ( high traffic ) 下使用，请准备相应的服务器负载，并明智地采用缓存策略。

#### 什么是MVVM

**传统MVC结构模式存在的问题**：

前端逻辑冗余的问题，本质原因是Model -> View可以直接通信，导致前端承载了过多的Model加工处理逻辑。

**解释1**

> 概念

- Model–View–ViewModel （MVVM） 是一个**软件架构设计模式**，源自于经典的 Model–View–Controller（MVC）模式，MVVM 的出现促进了**前端开发与后端业务逻辑的分离**，极大地提高了前端开发效率。
- MVVM 的**核心是 ViewModel 层**，它就像是一个中转站（value converter），该层向上**与视图层进行双向数据绑定**，向下**与 Model 层**通过接口请求进行数据交互，**转换 Model** 中的数据对象来让数据变得**更容易管理和使用**，起呈上启下作用。如下图所示：

![image-20210319112809935](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210319112809935.png)

> View层

View 是**视图层**，也就是用户界面。前端主要由 **HTML 和 CSS** 来构建 。

> Model 层

Model 是指**数据模型**，泛指后端进行的各种业务逻辑处理和数据操控，对于前端来说就是**后端提供的 api 接口**。

> ViewModel层

ViewModel 是由前端开发人员组织生成和维护的**视图数据层**。在这一层，前端开发者对从后端获取的 Model 数据进行**转换处理**，做二次封装，以生成符合 View 层使用预期的视图数据模型。

> MVVM框架的优势

- 需要注意的是 ViewModel 所封装出来的数据模型**包括视图的状态和行为**两部分，而 Model 层的数据模型是只包含状态的，比如页面的这一块展示什么，而页面加载进来时发生什么，点击这一块发生什么，这一块滚动时发生什么这些都属于视图行为（交互），**视图状态和行为都封装在了 ViewModel 里**。这样的封装**使得 ViewModel 可以完整地去描述 View 层**。
- MVVM 框架实现了**双向绑定**，这样 ViewModel 的内容会实时展现在 View 层，前端开发者再也**不必低效又麻烦地通过操纵 DOM 去更新视图**，MVVM 框架已经把最脏最累的一块做好了，我们开发者**只需要处理和维护 ViewModel**，更新数据视图就会自动得到相应更新。
- 这样 View 层展现的不是 Model 层的数据，而是 ViewModel 的数据，由 ViewModel 负责与 Model 层交互，这就完全**解耦了 View 层和 Model 层**，这个解耦是至关重要的，它是前后端分离方案实施的重要一环。

**解释2**

![image-20210518100021344](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210518100021344.png)

- View 层：视图层，对应到 `<template>` 标签的内容。
- VM 层：View-Model，对应到 **Vue 实例**。这一层是 View 和 Model 间的媒介。当用户操作通过 View 修改 View-Model 层的数据后，View-Model 会去修改 Model，然后再反过来把修改后的数据映射到 View 层上去。
- Model 层： 模型层，其实就是数据层。它对应到 Vue 中的数据。这个数据并非一个固定的实体，它可以代指 data 属性，也可以代指 Vuex 提供的数据，总之，它是**页面所依赖的 JS 数据对象**。

MVVM模型的关键，在于View的变化会直接映射在 ViewModel 中（反之亦然）。这个映射的过程不需要你手动实现，而是 MVVM 框架来帮你做掉。

这样一来，开发者开发 View 中的显示逻辑和 View-Model 中调用model的**业务逻辑可以隔离的非常好**，不需要在View 中还去维护一块和 ViewModel 间的逻辑。

“隔离”意味着什么？没错，就是解耦合！不同的人写不同的代码，彼此井水不犯河水。进而达到的就是关注点分离——负责业务逻辑的开发人员不需要关心显示细节，负责显示逻辑的人不需要关心业务逻辑细节，项目复杂度由此大大降低。

#### jQuery和React/Vue们的历史意义

> HTML、CSS 和 JavaScript，三位剑客各司其职，使得浏览器世界得以正常运行：

![image-20210511192542418](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210511192542418.png)

从图来看，JS 才是真正的多面手，其通过 API 操作 HTML 和 CSS。

在web的远古时代，前端页面“展示”的属性远远强于其“交互”的属性。因此 JavaScript 在很长一段时间里都不是前端世界的主角，人们只用 JS 来做一些类似于拖拽、隐藏这样简单的动效。

这个时期里，前端工程师需要关心的 DOM 操作是有限的。这样看来，使用 JS、jQuery 来定点对 DOM 进行修改好像也不是什么特别让人头大的事情。

**原生 JS 开发的痛点**：

原生DOM API 的时候，这些 **API 的冗长和繁琐**无数次使我感到疲惫，而使我更加疲惫的是 **IE 的各种奇怪特性**... （**兼容性问题**）因这些痛点

> jQuery 顺势而生，大放异彩！它极大地提高了开发的便利性

**解决的问题**：

- **简化**了 原生API 那冗长的**命名**，通过**链式调用**既方便了代码的书写又提高了可读性。
- 解决了大量**兼容性**问题。
- 对 ajax 等一些**常用功能的封装**。

**开发的特点**：

数据驱动的先驱——“模板助攻“

模板会帮我们把数据源读进去，塞到上面这段 template 代码里，把它们融合在一起，吐出一段目标 HTML 给你。然后这段 HTML 代码就可以直接被拿去渲染到页面上，成为 DOM。

这个过程差不多是这样：

```
// 数据和模板融合出 HTML 代码
var targetDOM = template({data: students})
// 添加到页面中去
document.body.appendChild(targetDOM)
```

这样的操作，可以帮助我们程序员做到只关心数据及数据变化，而**不必操心具体的 DOM 细节**，大大解放了生产力。

**jQuery时代开发的痛点**：

- 前端请求到的数据要转变为 HTML 时要写大量操作 DOM 的代码，而这些代码的强壮性与可复用性都很差，即要**实现「数据与UI同步」很难**。
- JS 修改 HTML 往往意味着整棵 DOM树 的销毁与重建，这非常**耗费性能**（模板渲染方案并不能很好地解决更新的问题）。
- 变量的全局污染，使**得模块化或多人协作都非常困难**等等

> React/Vue 等 MVVM 架构的框架又顺势而生，大放异彩

- **开发的便利性**：基于 **MVVM** 模式的思想，将 数据(Model) 映射至 HTML(View) 并保持同步（数据与 UI 同步），使得开发人员只需关注和操作数据(Model) 就可操作 DOM树 了（**数据驱动视图**）。
- **性能的极大提升**：通过 **虚拟DOM + Diff算法**，使得此类框架在更改 DOM树 前先判断 DOM树 的哪部分发生了变动，进而只更改变动的那部分（JS模拟DOM结构，计算每次更新的最小变更）。
- **模块化**：经由 node_modules 到各大框架的尝试，使得模块化开发已在前端界彻底落地。

> 在“更快”和“更广”还是有巨大的提升空间

比如：

- 更快：WebAssembly、Web Worker、ArrayBuffer 等等
- 更广：React-Native、Weex、Electron、PWA、Deno、Aframe、Ruff、小程序等等

## Vue3相关

#### Vue3 比 Vue2 有什么优势

- 性能更好
- 体积更小（5G时代体积优化的敏感度相对性能提升更低）
- 更好的ts支持（Vue3是ts开发的）
- 更好的代码组织
- 更好的逻辑抽离
- 更多新功能

#### Vue3 生命周期相对 Vue2的有什么区别

**Options API生命周期**（主要是改名来更好的对应mounted生命周期）

- beforeDestory 改为 beforeUnmount
- destroyed 改为 unmouted
- 其他沿用Vue2的生命周期

**Composition API生命周期**

采用Composition的方式来进行组件开发就得使用Composition API的生命周期函数

- setup内的执行代码相当与beforeCreate 和 created的执行代码
- setup内通过onXXX来定义相应的生命周期函数，传入参数为函数

```
// 等于beforeCreate 和 created
setup () {
    console.log('setup');
    
    onBeforeMount(() => {
        console.log('onBeforeMount');
    })
    
    onMounted(() => {
        console.log('onMounted');
    })
    
    onBeforeUpdate(() => {
        console.log('onBeforeUndate');
    })
    
    onUpdated(() => {
        console.log('onUpdated');
    })
    
    onBeforeUnmount(() => {
        console.log('onBeforeUnmount');
    })
    
    onUnmounted(() => {
        console.log('onBeforeUnmounted');
    })
}
```

#### Composition API 对比 Options API

> Compostion API 的优势（针对大型项目多代码的情况）

- 更好的代码组织（代码的组织会更加清晰，容易维护，但不一定是通过逻辑复用来实现）
- 更好的逻辑复用（多个组件的公共逻辑的复用更容易简单）
- 更好的类型推导

更好的代码组织和逻辑复用实例对比：

![image-20210417003417986](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210417003417986.png)

更好的类型推导对比

> Options API中的属性和方法引用（通过this的方式直接引用data和methods中的属性和方法，不符合JavaScript中的变量引用规范）

```
{
data() {
    return {
        a: 10
    }
}
methods: {
    fn1() {
        const a = this.a
    }
},
mounted() {
    this.fn()
    }
}
```

> Composition API 中的变量引用遵循 JS 的变量引用规范

![image-20210417123502025](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210417123502025.png)

**Composition API 如何实现逻辑复用**

- 抽离逻辑代码到一个函数
- 函数命名约定为 useXxx 格式（React Hooks 也是）
- 在 setup 中引用 useXxx 函数

#### ref toRef 和 toRefs

> ref

- 生成值类型的响应式数据
- 可用于模板和 reactive
- 通过 .value 修改值

基本使用？

1. 获取响应式的值

![image-20210529172454715](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210529172454715.png)

1. 从 template 中获取相应的dom元素，和vue2的使用方式相同

为什么需要 ref ？

1. 返回值类型（如解构赋值的形式 return 响应式对象），会丢失响应式
2. 为什么返回值不能都通过 reactive 封装的响应式对象的形式替代呢，如在setup、computed、合成函数，都有可能返回值类型（是开发者很普遍的需求）

为何需要 `.value`？

1. ref 是一个对象（不丢失响应式），value 存储值
2. 通过 .value 属性的 **getter，setter** 实现响应式（或者利用对象的引用关系）
3. 用于模板、reactive 时，不需要 .value，其他时候都需要

> toRef

- 针对的是一个响应式对象，reactive 封装的 prop
- 创建一个 ref，具有响应式（toRef 如果用于非响应式对象，如普通对象，产出的结果不具备响应式）
- 两者保持引用关系
- 与 reactive 在使用场景上的区别：
  - reactive 用于普通对象实现响应式对象
  - 一个响应式对象中的某个属性实现响应式用 toRef

![image-20210604112033259](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210604112033259.png)

> toRefs

- 将响应式对象（reactive封装）转换为普通对象
- 对象的每个 prop 都是对应的 ref
- 两者保持引用关系
- 和 reactive 封装的响应式对象使用上的区别：
  - reactive 封装的响应式对象在 return 时，需要返回整个响应式对象，但模板引用的时候需要加上前缀
  - reactive 封装的响应式对象在 return 时若采用解构的形式，其 return 的属性会失去响应式，此时采用 toRefs 封装后再返回会更合适（引用的时候也不能解构去使用，否则也会失去响应式）
  - 在setup中，若需要返回一个 reactive 对象供视图使用，不能直接返回reactive对象，否则也是相当于解构了该 reactive 对象，应该以{ reactive }的方式返回

![image-20210604112436641](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210604112436641.png)

toRefs 的使用场景

![image-20210604113458724](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210604113458724.png)

为什么需要 toRef 和 toRefs ？

1. 初衷：在不丢失响应式的前提下，把对象数据 **扩散/分散**
2. 前提：针对的是响应式对象（reactive 封装的）非普通对象（对象用 reactive，值类型用 ref）
3. 注意：**不创造**响应式，而是**延续**响应式

> 三者的最佳使用方式

- 用reactive做对象的响应式,用ref做值类型响应式
- setup中返回`toRefs(state)` , 或者`toRef(state，'xxx' )`
- ref的变量命名都用`xxxRef`
- 合成函数返回响应式对象时,使用toRefs

#### Vue3 的升级功能

- createApp
- 多事件
- emits 属性
- Fragiment
- 生命周期
- 移除 `.sync`
- 异步组件的写法
- Suspense
- 移除filter
- Composition API
- Teleport

![image-20210611205322208](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210611205322208.png)

![image-20210611205620404](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210611205620404.png)

![image-20210611205706937](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210611205706937.png)

![image-20210611205743797](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210611205743797.png)

![image-20210611205833395](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210611205833395.png)

![image-20210611205918042](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210611205918042.png)

![image-20210611210035282](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210611210035282.png)

![image-20210611210108479](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210611210108479.png)

![image-20210611210226201](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210611210226201.png)

![image-20210611210319479](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210611210319479.png)

#### Proxy的基本使用

`defineProperty`监听对象的缺点:

- 对象的监听需要一次性监听到底
- 无法监听对象的新增，删除属性
- 无法监听数组元素的变化

Proxy监听对象的特点：

- Proxy可以通过配置handle实现对对象的监听，handle中的属性配置项和`defineProperty` 的属性配置项相同，同时带有`deleteProperty`等配置项
- 结合Reflect系统自带对象来实现对监听对象的属性操作，handle中的属性配置项名字和Reflect拥有的静态方法分别对应
- 可以监听数组的变化

```
const data = ['a', 'b', 'c']

// const data = {
//     a: '1',
//     b: 2
// }
const proxyData = new Proxy(data, {
    get(target, key, receiver) {
        // Reflect.get 获取的是get的默认值
        let result = Reflect.get(target, key, receiver);
        console.log('get:', key);
        return result;
    },
    set(target, key, value) {
        let result = Reflect.set(target, key, value);
        console.log('set:', key);
        console.log('result:', result);
        // set 方法中需要返回Reflect.set的设置状态
        return result;
    },
    deleteProperty(target, key) {
        let result = Reflect.deleteProperty(target, key);
        console.log('del:', key);
        console.log('result:', result);
        // delete 方法中需要返回Reflect.set的设置状态
        return result;
    }
});
```

应用：

- 在get中配置，只处理对象本身（非原型）的数据

```
get(target, key, receiver) {
    //只处理本身(非原型的)属性
    const ownKeys = Ref lect . ownKeys(tprget)
    if (ownKeys. includes (key)) {
    console.log('get', key) // 监听
    }
    const result = Reflect.get(target, key, receiver)
    return result //返回结果
},
```

- 在set中配置，重复的数值不处理

```
set(target, key, val, receiver) { 
    //重复的数据，不处理
    if (val === target[key]) {
    	return true;
    }
    const result = Reflect.set(target, key, val, receiver)
    console.log('set',key, val)
    return result //是否设置成功
},
```

> 总结 Reflect 的作用

- 和Proxy 能力一一对应
- 规范化、标准化、函数式
- 替代掉Object上的工具函数

#### Vue3 使用 Proxy 实现响应式

> Vue3 实现响应式的大概模板

![image-20210725162755965](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725162755965.png)

![image-20210725162817448](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725162817448.png)

然后 proxyConf 中复用前一部分的对象代理设置

```
get(target, key, receiver) {
        // Reflect.get 获取的是get的默认值
        let result = Reflect.get(target, key, receiver);
        console.log('get:', key);
        return result;
    },
        set(target, key, value) {
            let result = Reflect.set(target, key, value);
            console.log('set:', key);
            console.log('result:', result);
            // set 方法中需要返回Reflect.set的设置状态
            return result;
        },
            deleteProperty(target, key) {
                let result = Reflect.deleteProperty(target, key);
                console.log('del:', key);
                console.log('result:', result);
                // delete 方法中需要返回Reflect.set的设置状态
                return result;
            }
```

- 还缺少**深度监听** 和 **新增对象属性监听**的功能

![image-20210725163348291](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725163348291.png)

对于深一层的属性，进行操作时，只会触发proxy对象第一层级的监听函数，说明深层级的属性变化并没有被监听

![image-20210725163726381](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725163726381.png)

如上图，age是原有属性，age1是新增属性，proxy能监听新增属性，但没有体现出新增属性在事件处理上的区别

**深度监听的实现**

![image-20210725163931804](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725163931804.png)

在每一层的get方法中返回result之前，再使用proxy监听该result，这样相对 defineProperty 的好处是不需要一次深度监听所有层级的属性，只在访问到该属性的上一层级的数据时才会触发数据的响应式处理

注意：为啥 vue2 中的 defineProperty 不能在 get 中实现深度监听

老师这么解答，我个人其实没有非常理解：Proxy 就像一个工厂函数，它可以直接封装并返回一个带有监听特性的新对象。而 defineProperty 更像是一个装饰器，它需要主动去调用执行，才能给对象添加上监听的特性。所以，defineProperty 无论如何都要深度递归去绑定，没法像 Proxy 一样。

**新增对象属性监听的实现**

在 setter 中加上 includes 的判断即可

![image-20210725165350323](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210725165350323.png)

## Vue项目的优化

#### Vue项目优化详见《[Vue 项目性能优化 — 实践指南](https://juejin.cn/post/6844903913410314247)》

#### **你有对 Vue 项目进行哪些优化？**

> 代码层面的优化

- v-if 和 v-show 区分应用场景
- computed 和 watch 区分应用场景
- v-for遍历必须为每个item添加key，同时要避免和v-if同时使用
- 长列表性能优化

Vue 会通过 Object.defineProperty 对数据进行劫持，来实现视图响应数据的变化，然而有**些时候我们的组件就是纯粹的数据展示，不会有任何改变，我们就不需要 Vue 来劫持我们的数据**，在大量数据展示的情况下，这能够很明显的减少组件初始化的时间，那如何禁止 Vue 劫持我们的数据呢？可以通过 `Object.freeze` 方法来冻结一个对象，一旦被冻结的对象就再也不能被修改了。

- 事件的销毁
- 图片资源懒加载

对于图片过多的页面，为了加速页面加载速度，所以很多时候我们需要**将页面内未出现在可视区域内的图片先不做加载， 等到滚动到可视区域后再去加载**。这样对于页面加载性能上会有很大的提升，也提高了用户体验。我们在项目中使用 Vue 的 `vue-lazyload` 插件

- 路由懒加载

能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应的组件

- 第三方插件的按需引入

我们在项目中经常会需要引入第三方插件，如果我们直接引入整个插件，会导致项目的体积太大，我们可以借助 `babel-plugin-component` ，然后可以只引入需要的组件，以达到减小项目体积的目的。在项目中引入 element-ui 组件库为例使用过改第三方插件

- 优化无限列表的性能

如果你的应用存在非常长或者无限滚动的列表，那么需要采用 窗口化 的技术来优化性能，只需要渲染少部分区域的内容，减少重新渲染组件和创建 dom 节点的时间。 你可以参考以下开源项目 [vue-virtual-scroll-list](https://github.com/tangbc/vue-virtual-scroll-list) 和 [vue-virtual-scroller](https://github.com/Akryum/vue-virtual-scroller) 来优化这种无限列表的场景的。

- 服务端渲染SSR和**预渲染**

如果你的 Vue 项目只需改善少数营销页面（例如 `/， /about， /contac`t 等）的 SEO，那么你可能需要**预渲染**，在构建时 (build time) 简单地生成针对特定路由的静态 HTML 文件。优点是**设置预渲染更简单**，并可以将你的前端作为一个完全静态的站点，具体你可以使用 [prerender-spa-plugin](https://github.com/chrisvfritz/prerender-spa-plugin) 就可以轻松地添加预渲染 。

> Webpack层面的优化

- webpack对图片进行压缩

在 vue 项目中除了可以在 `webpack.base.conf.js` 中 url-loader 中设置 limit 大小来对图片处理，对**小于 limit 的图片转化为 base64 格式**，其余的不做操作。所以对有些**较大的图片资源**，在请求资源的时候，加载会很慢，我们可以用 `image-webpack-loader`来**压缩图片**。

- 减少ES6转为ES5的冗余代码

Babel 插件会在将 ES6 代码转换成 ES5 代码时会注入一些**辅助函数**。在默认情况下， Babel 会在**每个输出文件中**内嵌这些依赖的辅助函数代码，如果多个源代码文件都依赖这些辅助函数，那么这些辅助函数的代码将会出现很多次，造成代码冗余。为了不让这些辅助函数的代码重复出现，可以在依赖它们时通过 `require('babel-runtime/helpers/createClass')` 的方式导入，这样就能做到只让它们出现一次。`babel-plugin-transform-runtime` 插件就是用来实现这个作用的，**将相关辅助函数进行替换成导入语句**，从而减小 babel 编译出来的代码的文件大小。

- 提取公共代码

将多个页面的公共代码抽离成单独的文件，来优化**相同的资源被重复加载**的问题 。Webpack 内置了专门用于提取多个Chunk 中的公共部分的插件 `CommonsChunkPlugin`

- 模板预编译

预编译模板最简单的方式就是使用[单文件组件](https://cn.vuejs.org/v2/guide/single-file-components.html)——相关的构建设置会自动把预编译处理好，所以构建好的代码已经包含了编译出来的渲染函数而不是原始的模板字符串。

- 提取组件的CSS

当使用单文件组件时，组件内的 CSS 会以 style 标签的方式通过 JavaScript 动态注入。这有一些小小的运行时开销，如果你使用服务端渲染，这会导致一段 “无样式内容闪烁 (fouc) ” 。将所有组件的 CSS 提取到同一个文件可以避免这个问题，也会让 CSS 更好地进行压缩和缓存。

- 优化SourceMap

我们在项目进行打包后，会将开发中的多个文件代码打包到一个文件中，并且经过压缩、去掉多余的空格、babel编译化后，最终将编译得到的代码会用于线上环境，那么这样处理后的代码和源代码会有很大的差别，当有 bug的时候，我们只能定位到压缩处理后的代码位置，无法定位到开发环境中的代码，对于开发来说不好调式定位问题，因此 sourceMap 出现了，它就是为了解决不好调式代码问题的。

SourceMap 的可选值如下（+ 号越多，代表速度越快，- 号越多，代表速度越慢, o 代表中等速度 ）

![image-20210323145530446](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/Vue%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210323145530446.png)

**cheap**： 源代码中的列信息是没有任何作用，因此我们打包后的文件不希望包含列相关信息，只有行信息能建立打包前后的依赖关系。因此不管是开发环境或生产环境，我们都希望**添加 cheap 的基本类型来忽略打包前后的列信息**；

**module** ：不管是开发环境还是正式环境，我们都希望能**定位到bug的源代码具体的位置**，比如说某个 Vue 文件报错了，我们希望能**定位到具体的 Vue 文件**，因此我们也需要 module 配置；

**soure-map** ：source-map 会**为每一个打包后的模块生成独立的 soucemap 文件** ，因此我们需要增加source-map 属性；

**eval-source-map**：eval 打包代码的速度非常快，因为它**不生成 map 文件**，但是可以对 eval 组合使用 eval-source-map 使用会将 map 文件以 DataURL 的形式存在打包后的 js 文件中。在正式环境中不要使用 eval-source-map, 因为**它会增加文件的大小**，但是在开发环境中，可以试用下，因为他们打包的速度很快。

> 基础的 Web 技术的优化

- 开启 gzip 压缩
- 浏览器缓存
- CDN 的使用
- 使用 Chrome Performance 查找性能瓶颈

