## ES6细节

**ECMAScript 和 JavaScript 的关系是，前者是后者的规格，后者是前者的一种实现**

### proxy

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

```js
var proxy = new Proxy(target, handler);
//handler参数是一个对象  用来定制拦截行为
```

一般是叫他拦截器，可以拦截对象上的一个操作。用法如下，通过 new 的方式创建对象，第一个参数是被拦截的对象，第二个参数是对象操作的描述。实例化后返回一个新的对象，当我们对这个新的对象进行操作时就会调用我们描述中对应的方法。

```js
var proxy = new Proxy(target,{
    get(tar,property) {
        
    },
    set(tar,property) {
        
    },
    deleteProperty(tar,property) {
        
    }
})

```

### Proxy区别于Object.defineProperty

Object.defineProperty 只能监听到属性的读写，而 Proxy 除读写外**还可以监听属性的删除，方法的调用等。**

```js
const list = [1,2,3,1,1]
const listProxy = new Proxy(list,{
    set(tar,property,val) {
        tar[property] = val
        return true
    }
})
```

使用Object.defineProperty

```js
 Object.defineProperty(obj, key, {
    get() {
      console.log('获取：' + key)
      return value
    },
    set(val) {
      observer(val)
      console.log(key + "-数据改变了")
      value = val
    }
```

* 问题1：删除或者增加的属性无法监听
* 问题2：无法监听数组的变化
* 问题3：如果层级较深，花的时间可能会比较多



**proxy**

```js
  const handler = {
        get(target, key, receiver) {
          console.log('获取：' + key) // 如果是对象，就递归添加 proxy 拦截
          if (typeof target[key] === 'object' && target[key] !== null) {
            return new Proxy(target[key], handler)
          }
          return Reflect.get(target, key, receiver)
        },
        set(target, key, value, receiver) {
          console.log('设置：' + key) // 如果是对象，就递归添加 proxy 拦截
          return Reflect.set(target, key, value, receiver)
        }
      }
      return new Proxy(obj, handler)
    }
```

+ **Object.defineProperty 拦截的是对象的属性，会改变原对象。proxy 是拦截整个对象，通过 new 生成一个新对象，不会改变原对象**

+ **proxy 的拦截方式，除了上面的 get 和 set ，还有 11 种。选择的方式很多 Proxy，也可以监听一些 Object.defineProperty 监听不到的操作，比如监听数组，监听对象属性的新增，删除等**

### Symbol

```js
let s = Symbol();

typeof s
```

Symbol值不能与其他类型的值做运算  会报错

但是Symbol可以转化为字符串

```js
let sym = Symbol('my symbol')
String(sym) = 'Symbol(my symbol)''
sym.toString()  =>  'Symbol(my symbol)'
```

注意：创建symbol的时候可以添加一个描述

```js
const sym = Symbol('this is a description')
```

上面的代码中  sym的描述字符串就是 'this is a description'

读取这个描述需要显示转化为字符串

```js
String(sym)
```

后面给出了

```js
sym.description   //'this is a description'
```

由于每一个 Symbol 值都是不相等的，这意味着 Symbol 值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性。这对于一个对象由多个模块构成的情况非常有用，能防止某一个键被不小心改写或覆盖。

#### 作为属性名的Symbol

```js
let a = {
    [mySymbol]  = 'hello'
}
```

在对象中 使用symbol值定义属性时 放在方括号中

```js
let s = Symbol()

let obj = {
    [s]:function(arg) {...}
    [s](arg) {...}  //增强写法
}
```

Symbol 作为属性名，遍历对象的时候，该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回。

**有一个`Object.getOwnPropertySymbols()`方法，**可以获取指定对象的所有 Symbol 属性名。该方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。

#### Symbol.for() 、Symbol.keyFor()

可以通过Symbol.for()方法生成同一个symbol值

```js
let s1 = Symbol.for('foo')
let s2 = Symbol.for('foo')

s1 === s2   //true
```

#### 模块的 Singleton 模式

Singleton 模式指的是调用一个类，任何时候返回的都是同一个实例。

