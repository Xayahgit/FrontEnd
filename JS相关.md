# JS相关面试题

## 系统方法

### 系统方法

- `isNaN`方法：该方法用于检测参数是否为数值型，如果是，返回true，否则，返回false。（False）

 `isNaN()` **函数用于检查其参数是否是非数字值。**

如果判断可以转化为数字  说明他是一个数字 （转化前） 那么返回false、如果是字符串或者NaN本身  不能转化为数字的返回true



- `eval`方法：该方法将**某个参数字符串作为一个JavaScript执行。**(True)

考察另一角度：可以在写的代码中用程序生成代码并运行，就好像代码写在那个位置，起到修改词法作用域的效果。（见JS书）

- `escape`方法： 该方法返回对一个字符串编码后的结果字符串。(True)
- 与**浏览列表**有关的对象：history screen location Navigator

### DOM操作

#### DOM属性和方法

- 页面有一个按钮`<button>按钮，通过原生的js 设置背景色为红色？</button>`

```
document.getElementById('button1').style.backgroundcolor="red";
```

注意点：`getElementById`方法名，`backgroundcolor`属性名

- 下段代码的分析

```
<html>
<body>
 <div id="ele" class="div">
 <span id="s1" class="sp" lang="zh-cn">
 </span>
 </div>
</body>
<script type="text/javascript">  
 function exct() {
    var oEle = document.getElementById("ele");
    var child = oEle.children;
    console.log("ele.children的执行结果是:");
    for(i = 0; i < child.length; i++){
       console.log(child[i].tagName);
    }  
    child = oEle.childNodes;
    console.log("ele.childNodes的执行结果是:");
    for(i = 0; i < child.length; i++){
       console.log(child[i].tagName);
    }
 }  
 exct (); 
</script>
</html>
其运行结果是：
div1.children的执行结果是:
SPAN
div1.childNodes的执行结果是:
undefined
SPAN
undefined
```

对于DOM元素，children是指DOM Object类型的子对象，不包括tag之间隐形存在的TextNode，而childNodes包括tag之间隐形存在的TextNode对象

**childNodes获取子节点没有类型限制，span是一个元素节点，**还有两个文本节点：空格和换行都被看做一个文本节点。所以，3个子节点 = 两个文本节点 + 一个元素节点。tagName用来获取元素的标签名，文本节点没有标签名，所以也就为undefined了

- 查找元素的方法

getElementById

getElementsByTagName

getElementsByClassName 注意带s

getElementsByName 注意有这个方法，根据name属性查询

#### DOM事件

1. w3c 制定的 javascript 标准事件模型，以下正确的顺序以及描述是

事件捕获>事件冒泡 事件捕获->事件处理->事件冒泡 事件冒泡->事件处理 事件冒泡->事件处理->事件捕获 事件处理->事件捕获->事件冒泡

> 解答

B

![image-20210311204716023](https://gitee.com/lu_jianping/the-front-end-knowledge/raw/master/JS%E7%9B%B8%E5%85%B3%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20210311204716023.png)

1. 触摸事件包括以下哪几种？（）

touchesStart touchesMove touchesCancel touchesEnd

> 解答

ABCD

**以下是四种touch事件**

- touchstart: //手指放到屏幕上时触发

  touchmove: //手指在屏幕上滑动式触发

  touchend: //手指离开屏幕时触发

  touchcancel: //系统取消touch事件的时候触发，这个好像比较少用

- 每个触摸事件被触发后，会生成一个event对象，event对象里额外包括以下三个触摸列表

  touches: //当前屏幕上所有手指的列表

  targetTouches: //当前dom元素上手指的列表，尽量使用这个代替touches

  changedTouches: //涉及当前事件的手指的列表，尽量使用这个代替touches

### windows 和 document相关

1. 

```js
<html>
     <head>
         <script>
         function myFunc() {
             document.write(a);
             document.write(func());
             var a = 1;
             function func() {
                return 2;
             }
         }
         </script>
     </head>
     <body>
         <p>1</p>
         <button onclick = "myFunc()">点击</button>
     </body>
 </html>
```

点击按钮后页面上的最终输出结果为：undefined2

> 解析

题目问的是点击按钮后页面上的最终输出结果，所以原来页面上的p标签的1，会被document.write函数输出的内容覆盖。所以没有1。

打印a是undefined是因为var a的时候进行了变量提升，但是要执行到那一步才会真的赋值。

> 注意

总的来说就是 在页面渲染结束后（即文档加载后）使用document.write。会覆盖整个文档。

说明： window.onload必须等到页面内包括图片的所有元素加载完毕后才能执行。 点击事件也是页面渲染完毕后，鼠标点击才能执行。

w3school的官方说明：您只能在 HTML 输出中使用 document.write。如果您在文档加载后使用该方法，会覆盖整个文档。

1. 下列不属于document对象方法的是？（）

- onload
- querySelectorAll
- children
- ajax

> 解析

A.onload 为 document 对象的属性，而不是方法：

![img](https://qqadapt.qpic.cn/txdocpic/0/8f02a1e74ab6ae5d1e6c5323ae7dd31e/0)

C. ParentNode.children 是一个只读属性，返回一个节点的子元素，是一个动态更新的 HTMLCollection 类型。不是 document 对象的方法。

D. AJAX不是JavaScript的规范，它只是一个缩写：Asynchronous JavaScript and XML，意思就是用JavaScript执行异步网络请求。在现代浏览器中主要依靠 XmlHttpRequest 对象。

1. 以下哪些事件会在页面加载完成（onload）之前触发？

readystatechange pageshow beforeunload DOMContentLoaded

> 解答

AD

A. readystatechange

document有readyState属性来描述document的loading状态，readyState的改变会触发readystatechange事件.

- loading 文档仍然在加载
- interactive 文档结束加载并且被解析，但是像图片，样式，frame之类的子资源仍在加载
- complete 文档和子资源已经结束加载，该状态表明将要触发load事件。

因此readystatechange在onload之前触发。

B.onpageshow onpageshow 事件在用户浏览网页时触发。 onpageshow 事件类似于 onload 事件，onload 事件在页面第一次加载时触发， onpageshow 事件在每次加载页面时触发，即 onload 事件在页面从浏览器缓存中读取时不触发。

C. beforeunload 当浏览器窗口，文档或其资源将要卸载时，会触发beforeunload事件。这个文档是依然可见的，并且这个事件在这一刻是可以取消的. 如果处理函数为Event对象的returnValue属性赋值非空字符串，浏览器会弹出一个对话框，来询问用户是否确定要离开当前页面（如下示例）。有些浏览器会将返回的字符串展示在弹框里，但有些其他浏览器只展示它们自定义的信息。没有赋值时，该事件不做任何响应。

D.DOMContentLoaded 当初始的 HTML 文档被完全加载和解析完成之后，DOMContentLoaded 事件被触发，而无需等待样式表、图像和子框架的完成加载。 另一个不同的事件 load 应该仅用于**检测一个完全加载的页面**。因此DOMContentLoaded是HTML完全加载和解析完成之后发生的，发生时间点要早于load，选D。 在使用 DOMContentLoaded 更加合适的情况下使用 load 是一个令人难以置信的流行的错误，所以要谨慎。 注意：DOMContentLoaded 事件必须等待其所属script之前的样式表加载解析完成才会触发。

### 自带类

- ```
  var x = new Boolean(false);
  if (x) {
    alert('hi');
  }
  var y = Boolean(0);
  if (y) {
    alert('hello'); 
  }
  ```

  显示结果为 `hi`

  此题考查的是 JS 的类型转换：(注意new和没有new的区别)

  - if(x) 这里期望 x 是一个布尔类型的原始值，而 x 是一个对象，**任何对象转为布尔值，都为得到 true**（**切记！在 JS 中，只有 0，-0，NaN，""，null，undefined 这六个值转布尔值时，结果为 false**）。
  - 题目的第二部分，一定要注意 **y = Boolean(0)**，**而不是 y = new Boolean(0)。**这两个有很大区别，**用 new 调用构造函数会新建一个布尔对象，此处没有加 new，进行的是显示类型转换**，正如上述第一条所说，0 转换布尔，结果为 false，所以此时 y 的值就是 false。如果加了 new，那么 y 就是一个 Boolean 类型的对象，执行 if(y) 时，对象转布尔，始终是 true，所以结果会与不加 new 的时候相反。

- 以下结果里，返回true的是？

  - !![]
  - 1 === '1'
  - null === undefined
  - !!''

> 解析

空对象转换为布尔类型为true。

| 数据类型  | 转换为true的值            | 转换为false的值 |
| --------- | ------------------------- | --------------- |
| Boolean   | true                      | false           |
| String    | 任何非空字符串            | ""空字符串      |
| Number    | 任何非0数字值(包括无穷大) | 0和NaN          |
| Object    | 任何对象                  | null            |
| Undefined | n/a                       | undefined       |

> JavaScript中比较特殊的类型转换

```
!!String("")   // 返回true
!!Boolean("")  // 返回false
!!Number("")   // 返回true
 
!!Boolean([])   // 返回true
!!String([])    // 返回false
!!Number([])    //false
 
!!String(0)   // 返回true  #被转为字符串"0"
!!Number(0)      // 返回false
!!Boolean(0)   // 返回false
 
[] 转为字符串是 ""       // String([]) 返回""
[] 转为数字是 0            // Number([]) 返回0
[] 转为布尔值是 true        // Boolean([]) 返回true
true 转为数字是 1       // Number(true) 返回1
false 转为数字是 0      // Number(false) 返回0
```

- 下面哪些执行结果为true()

'foo' == new function(){ return String('foo'); }; 'foo' == new function(){ return new String('foo'); }; [] == 0 ![] !0

> 解答

BCE

如果构造函数没有return语句时，则默认返回原型为Human.prototype的、设置了属性name的对象。然而就像Class的constructor一样，return语句返回一个**对象**时，该对象将被作为new操作符的结果返回。

A：String()作为普通函数使用时，将值转为字符串，**不是对象**，默认返回是一个空对象，原型为匿名函数的prototype。

```
String(``new` `function``(){ ``return` `String(``'foo'``); })``"[object Object]"
```

B：String()作为构造函数来用时，返回了一个字符串包装**对象**。

```
String(``new` `function``(){ ``return` `new` `String(``'foo'``); })``"foo"
```

### Ajax相关问题

- 在准备XMLHttpRequest对象时，在send()前需要调用哪个方法？

Ajax技术核心就是XMLHttpRequest对象。

Ajax技术的工作原理：可以分成3步

1.创建Ajax对象：var xhr = new XMLHttpRequest();

2.xhr 发送请求：xhr.open('get','test.html','true');

 xhr.send();

3.xhr获取响应：

 xhr.onreadystatechange = function(){

 if(xhr.readystate == 4){//请求的状态码

 /*

 0:请求还没有建立（open执行前）

 1：请求建立了还没发送（执行了open）

 2：请求正式发送（执行了send）

 3：请求已受理，有部分数据可以用，但还没有处理完成

 4：请求完全处理完成

 */

 alert(xhr.responseText);//返回的数据

 }

 }

可以看到，send()前是open()

open()首先确定的是  方法get || post   然后是url和boolean



### `iframe`的相关问题

- `iframe`的创建比一般的DOM元素慢了1-2个数量级（True）
- `iframe`本质是动态语言的Include机制和利用ajax动态填充内容（False）

解析：

**1、创建比一般的 DOM 元素慢了 1-2 个数量级**

iframe 的创建比其它包括 scripts 和 css 的 DOM 元素的创建慢了 1-2 个数量级，使用 iframe 的页面一般不会包含太多 iframe，所以创建 DOM 节点所花费的时间不会占很大的比重。但带来一些其它的问题：onload 事件以及连接池（connection pool）

2、阻塞页面加载

及时触发 window 的 onload 事件是非常重要的。onload 事件触发使浏览器的 “忙” 指示器停止，告诉用户当前网页已经加载完毕。当 onload 事件加载延迟后，它给用户的感觉就是这个网页非常慢。

**window 的 onload 事件需要在所有 iframe 加载完毕后（包含里面的元素）才会触发。**（所以他会很大程度上阻塞onload事件）在 Safari 和 Chrome 里，通过 JavaScript 动态设置 iframe 的 SRC 可以避免这种阻塞情况

3、唯一的连接池

浏览器只能开少量的连接到 web 服务器。比较老的浏览器，包含 Internet Explorer 6 & 7 和 Firefox 2，只能对一个域名（hostname）同时打开两个连接。这个数量的限制在新版本的浏览器中有所提高。Safari 3+ 和 Opera 9+ 可同时对一个域名打开 4 个连接，Chrome 1+, IE 8 以及 Firefox 3 可以同时打开 6 个

绝大部分浏览器，主页面和其中的 iframe 是共享这些连接的。这意味着 iframe 在加载资源时可能用光了所有的可用连接，从而阻塞了主页面资源的加载。如果 iframe 中的内容比主页面的内容更重要，这当然是很好的。但通常情况下，iframe 里的内容是没有主页面的内容重要的。这时 iframe 中用光了可用的连接就是不值得的了。一种解决办法是，在主页面上重要的元素加载完毕后，再动态设置 iframe 的 SRC。

4、不利于 SEO

**搜索引擎**的检索程序**无法解读** iframe。另外，iframe 本身不是动态语言，样式和脚本都需要额外导入。综上，iframe 应谨慎使用。

## 语言特性

### 基本类型

基本类型：undefined、null、string、number、boolean、symbol、bigInt()

*`BigInt`** 是一种内置对象，它提供了一种方法来表示大于 `2^53 - 1` 的整数。这原本是 Javascript中可以用 [`Number`]表示的最大数字。*`BigInt`** 可以表示任意大的整数

它在某些方面类似于 [`Number`] ，但是也有几个关键的不同点：不能用于 [`Math`] 对象中的方法；不能和任何 [`Number`]实例混合运算，两者必须转换成同一种类型。在两种类型来回转换时要小心，因为 `BigInt` 变量在转换成 [`Number`]变量时可能会丢失精度。

**特点** 1.基本类型的值是不可变得

```
// 任何方法都无法改变一个基本类型的值
let name = 'jay';
name.toUpperCase(); // 输出 'JAY'
console.log(name); // 输出  'jay'
```

2.基本类型的比较是值的比较

```
// 只有在它们的值相等的时候它们才相等
let a = 1;
let b = true;
console.log(a == b); //true
// 用==比较两个不同类型的变量时会进行一些类型转换。
// 先会把true转换为数字1再和数字1进行比较，结果就是true了
```

3.基本类型的变量是存放在栈区的（栈区指内存里的栈内存）

### 引用类型

引用类型的值是**同时保存在栈内存和堆内存中的对象** javascript和其他语言不同，其不允许直接访问内存中的位置，也就是说不能直接操作对象的内存空间。

实际上，是操作对象的引用，所以引用类型的值是按引用访问的。准确地说，引用类型的存储需要内存的栈区和堆区（堆区是指内存里的堆内存）共同完成，**栈区内存保存变量标识符和指向堆内存中该对象的指针**，也可以说是该**对象在堆内存的地址**。

### 数据类型的判断

### **js判断数据类型**

**1. typeof 操作符**

- 对于基本类型，除 null 以外，均可以返回正确的结果。
- 对于引用类型，除 function 以外，一律返回 object 类型。
- 对于 null ，返回 object 类型。
- 对于 function 返回 function 类型。

**2. instanceof** 用来判断 A 是否为 B 的实例，检测的是原型。instanceof 只能用来判断两个对象是否属于实例关系， 而不能判断一个对象实例具体属于哪种类型。 instanceof 主要的实现原理就是只要右边变量的 prototype 在左边变量的原型链上即可。

**3. constructor**

- null 和 undefined 是无效的对象，不会有 constructor 存在的
- 函数的 constructor 是**不稳定的**，这个主要体现在自定义对象上，当开发者重写 prototype 后，原有的 constructor 引用会丢失，constructor 会默认为 Object。为了规范开发，在重写对象原型时一般都需要重新给 constructor 赋值。

**4. toString**

toString() 是 Object 的原型方法，调用该方法，**默认返回当前对象的 [[Class]]** 。这是一个内部属性，其格式为 [object Xxx] ，其中 Xxx 就是对象的类型。f

### 优先级

- 假设val已经声明,可定义为任何值。则下面js代码有可能输出的结果为:

```
console.log('Value is ' + (val != '0') ? 'define' : 'undefine');
```

> 解析

define

加号优先级高于 三目运算。低于括号。 所以括号中无论真假 加上前边的字符串都为 TRUE 三目运算为TRUE是 输出 define

### 变量提升

```
(function() {
      var a = b = 5;
  })();   
console.log(b);
console.log(a);
```

上面的输出结果：Uncaught ReferenceError: a is not defined

> 解析

- var a=b=5 相当于var a=b;b=5;
- a声明的是函数的局部变量

### 函数重载

- 

```
<script> 
    var m= 1, j = k = 0; 
    function add(n) { 
        return n = n+1; 
　 } 
    y = add(m); 
    function add(n) { 
        return n = n + 3; 
    } 
z = add(m); 
</script> 
```

y和z的最终结果为：4,4

> 解析

同名函数声明，后面声明的会覆盖前者

### ==和===的区别

- 

```
var one;
var two=null;
console.log(one==two,one===two);
```

上面代码的输出结果是：

> 解答

true false

- 在JavaScript中下面的值被当作假 false. undefined. null. 空字符串 数字0 数字NAN
- == 和 ===的区别是，==会将两边的值进行隐式类型转换，而 ===也不会。
- 题目中，var one; 只定义未赋值，故one是的值为undefined, undefined和null转换为Boolean类型后都为false，===不会进行转换， ，那么undefined和null当然不相等啦，所以是true！
- 下面结果为真的表达式是：（）

null instanceof Object null === undefined null == undefined NaN == NaN

> 解答

C

- typeof null === 'object' //true

  null instanceof Object //false

- NaN表示非数字值，特殊之处：它和任何值都不相等，包括自身。判断NaN的方法：x!=x返回true

## ES5 ES6相关

### 数组相关方法

- 以下代码执行后，array 的结果是？ let array = [,1,,2,,3]; array = array.map((i) => ++i)

> 解析

[,2,,3,,4]

ES5中orEach(), filter(), reduce(), every() 和some()都会**跳过空位**。

map()会跳过空位，但会保留这个值

join()和toString()会将空位视为undefined，而undefined和null会被处理成空字符串。

ES6都会**将空位当做undefined**

## Node.js相关

1. 按照CommonJS规范，在任何模块代码的作用域下内置了以下哪些变量？

module context require exports

> 解答

ACD

- 浏览器不兼容CommonJS的根本原因，在于缺少四个Node.js环境的变量。 module exports require global 来自阮一峰http://www.ruanyifeng.com/blog/2015/05/commonjs-in-browser.html

## 跨域问题的解决

因为**浏览器**的同源策略导致了跨域。**同源策略**是一个重要的**安全**策略，它用于限制一个`origin`的文档或者它加载的脚本**如何能与另一个源的资源进行交互**。它能帮助阻隔恶意文档，减少可能被攻击的媒介。

所谓同源是指"**协议+域名+端口**"三者相同。不同协议，不同域名，不同端口都会构成跨域。

### 跨域方式总结

- 第一种方式：`jsonp`请求；`jsonp`的原理是利用<script>标签的跨域特性，可以不受限制地从其他域中加载资源，类似的标签还有![img]().

前端代码：

```
<script>
    var script = document.createElement('script');
    script.type = 'text/javascript';

    // 传参一个回调函数名给后端，方便后端返回时执行这个在前端定义的回调函数
    script.src = 'http://xxxxxxx:8080/login?callback=handleCallback';
    document.head.appendChild(script);

    function handleCallback(res) {
        alert(JSON.stringify(res));
    }
</script>
```

后台代码：

```
<?php
  $callback = $_GET['callback'];//得到回调函数名
  $data = array('a','b','c');//要返回的数据
  echo $callback.'('.json_encode($data).')';//输出
?>
```

- 第二种方式：`document.domain`；这种方式用在主域名相同子域名不同的跨域访问中（有公共的上级域名）[解析](https://blog.csdn.net/nlznlz/article/details/79506655)（**仅限主域相同，子域不同的跨域应用场景。**）

栗子：

在父页面 http://xxx.com/a.html 中设置document.domain

```
<iframe id = "iframe" src="http://xxx.com/b.html" onload = "test()"></iframe>
<script type="text/javascript">
    document.domain = 'xxx.com';//设置成主域
    function test(){
       alert(document.getElementById('￼iframe').contentWindow);
       //contentWindow 可取得子窗口的 window 对象
    }
</script>
```

在子页面http://xxx.com/b.html 中设置document.domain

```
<script type="text/javascript">    
    document.domain = 'xxx.com';    //在iframe载入这个页面也设置document.domain，使之与主页面的document.domain相同
</script>
```

- 第三种方式：`window.name`；window的name属性有个特征：在一个窗口(window)的生命周期内,窗口载入的所有的页面都是共享一个`window.name`的，每个页面对`window.name`都有读写的权限，`window.name`是持久存在一个窗口载入过的所有页面中的，并不会因新页面的载入而进行重置。（**需要目标服务器响应window.name**）

栗子： 在子页面(b.com/data.html) 设置window.name：

```
/* b.com/data.html */
<script type="text/javascript">
   window.name = 'I was there!';    
   // 这里是要传输的数据，大小一般为2M，IE和firefox下可以大至32M左右
   // 数据格式可以自定义，如json、字符串
</script>
```

在父页面（a.com/app.html）中创建一个iframe，把其src指向子页面。在父页面监听iframe的onload事件，获取子页面数据：

```
/* a.com/app.html */
<script type="text/javascript">
    var iframe = document.createElement('iframe');
    iframe.src = 'http://b.com/data.html';
    function iframelLoadFn() {
      var data = iframe.contentWindow.name; 
      console.log(data);
      // 获取数据以后销毁iframe，释放内存；这也保证了安全（不被其他域frame js访问）。
      iframeDestoryFn();
    }

    function iframeDestoryFn() {
      iframe.contentWindow.document.write('');
      iframe.contentWindow.close();
      document.body.removeChild(iframe);
    }

    if (iframe.attachEvent) {
        iframe.attachEvent('onload', iframelLoadFn);
    } else {
        iframe.onload = iframelLoadFn;
    }
    document.body.appendChild(iframe);
</script>
```

- 第四种方式：`window.postMessage`；`window.postMessages`是html5中实现跨域访问的一种新方式，可以使用它来向其它的window对象发送消息，无论这个window对象是属于同源或不同源。（**需要服务器或者目标页面写一个postMessage，主要侧重于前端通讯。**）

**栗子：** 假如有一个页面，页面中拿到部分用户信息，点击进入另外一个页面，另外的页面默认是取不到用户信息的，你可以通过window.postMessage把部分用户信息传到这个页面中。（需要考虑安全性等方面。）

发送消息：

```
// 弹出一个新窗口
var domain = 'http://haorooms.com';
var myPopup = window.open(`${domain}/windowPostMessageListener.html`,'myWindow');

// 发送消息
setTimeout(function(){
  var message = {name:"站点",sex:"男"};
  console.log('传递的数据是  ' + message);
  myPopup.postMessage(message, domain);
}, 1000);
```

接收消息：

```
// 监听消息反馈
window.addEventListener('message', function(event) {  
    // 判断域名是否正确  
    if (event.origin !== 'http://haorooms.com') return;  
    console.log('received response: ', event.data);
}, false);
```

- 第五种方式：**CORS**；CORS背后的基本思想，就是使用自定义的HTTP头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功还是应该失败。

- 第六种方式：**Web Sockets**；web sockets原理：在JS创建了web socket之后，会有一个HTTP请求发送到浏览器以发起连接。取得服务器响应后，建立的连接会使用HTTP升级从HTTP协议交换为web socket协议。

- 第七种方式：**nginx反向代理**

  反向代理（Reverse Proxy）方式是指以代理服务器来接受客户端的连接请求，然后将请求转发给内部网络上的服务器；并将从服务器上得到的结果返回给客户端，此时**代理服务器对外就表现为一个服务器**。

  反向代理服务器对于客户端而言它就像是原始服务器，并且**客户端不需要进行任何特别的设置**。客户端向反向代理 的命名空间(name-space)中的内容发送普通请求，接着反向代理将判断向何处(原始服务器)转交请求，并将获得的内容返回给客户端，就像这些内容 原本就是它自己的一样。

### JSONP的优缺点

- JSONP的优点是：它不像`XMLHttpRequest`对象实现的Ajax请求那样受到同源策略的限制；它的兼容性更好，在更加古老的浏览器中都可以运行，不需要`XMLHttpRequest`或`ActiveX`的支持；并且在请求完毕后可以通过调用callback的方式回传结果。
- JSONP的缺点则是：它只支持`GET`请求而不支持`POST`等其它类型的HTTP请求；它**只支持跨域HTTP请求这种情况，不能解决不同域的两个页面之间如何进行JavaScript调用的问题**。

## 其他

### 其他

- flash和Js通过什么类进行交互

Flash提供了`ExternalInterface`接口与JavaScript通信，`ExternalInterface`有两个方法，`call`和`addCallback`，`call`的作用是让Flash调用Js里的方法，`addCallback`是用来注册flash函数让Js调用。

### 模块化

AMD/CMD/CommonJs都是JS模块化开发的标准，目前对应的实现是**RequireJS，SeaJs, nodeJs**;

#### **CommonJS：服务端js**

CommonJS 是以在浏览器环境之外构建 javaScript 生态系统为目标而产生的写一套规范，主要是为了**解决 javaScript** **的作用域问题而定义的模块形式**，可以使每个模块它自身的**命名空间**中执行。

**实现方法：**模块必须通过 **module.exports** 导出对外的变量或者接口，通过 **require()** 来导入其他模块的输出到当前模块的作用域中；

主要针对**服务端（同步加载文件）\**和桌面环境中，node.js 遵循的是 CommonJS 的规范；CommonJS 加载模块是\**同步的**，所以**只有加载完成才能执行后面的操作**。

- require()用来引入外部模块；
- exports对象用于导出当前模块的方法或变量，唯一的导出口；
- module对象就代表**模块本身**。

#### **AMD：异步模块定义【浏览器端js】**

采用的是**异步**的方式进行模块的加载，**在加载模块的时候不影响后边语句的运行**。主要是为**前端 js** 的表现指定的一套规范。

**实现方法：\**通过\**define方法去定义模块**，通过require方法去加载模块。 `define(id?,dependencies?,factory)`: 它要在声明模块的时候制定所有的依赖(dep)，并且还要当做形参传到factory中。*没什么依赖，就定义简单的模块（或者叫独立的模块）* `require([modules], callback)`: 第一个参数[modules]，是需加载的模块名数组；第二个参数callback，是模块**加载成功之后的回调函数**

```
// module1.js文件, 定义独立的模块
define({
    methodA: () => console.log('我是module1的methodA');
    methodB: () => console.log('我是module1的methodB');
});

// module2.js文件, 另一种定义独立模块的方式
define(() => {
    return {
        methodA: () => console.log('我是module2的methodA');
        methodB: () => console.log('我是module2的methodB');
    };
});

// module3.js文件, 定义非独立的模块（这个模块依赖其他模块）
define(['module1', 'module2'], (m1, m2) => {
    return {
        methodC: () => {
            m1.methodA();
            m2.methodB();
        }
    };
});


//定义一个main.js，去加载这些个模块
require(['module3'], (m3) => {
    m3.methodC();
});


// 为避免造成网页失去响应，解决办法有两个，一个是把它放在网页底部加载，另一个是写成下面这样：
<script src="js/require.js" defer async="true" ></script>
// async属性表明这个文件需要异步加载，避免网页失去响应。
// IE不支持这个属性，只支持defer，所以把defer也写上。

// data-main属性: 指定网页程序的主模块
<script data-main="main" src="js/require.js"></script>

// 控制台输出结果
我是module1的methodA
我是module2的methodB
```

#### **CMD：通用模块定义【浏览器端js】**

CMD 是 Common Module Definition 的缩写，通过**异步**的方式进行模块的加载的，在加载的时候会**把模块变为字符串解析一遍才知道依赖了哪个模块**；

主要针对浏览器端（异步加载文件），**按需加载文件**。对应的实现是seajs

#### **AMD和CMD的区别**

1. 对于依赖的模块，**AMD 是提前执行**，**CMD 是延迟执行**。不过 RequireJS 从 2.0 开始，也改成可以延迟执行（根据写法不同，处理方式不同）。CMD 推崇 as lazy as possible（尽可能的懒加载，也称为延迟加载，即在需要的时候才加载）。
2. **CMD 推崇依赖就近**，**AMD 推崇依赖前置**。

```
// CMD
define(function(require, exports, module) {
    var a = require('./a');
    a.doSomething();
    // ...
    var b = require('./b');   // 依赖可以就近书写
    b.doSomething();
    // ... 
})

// AMD
define(['./a', './b'], function(a, b) { // 依赖必须一开始就写好
    a.doSomething();
    // ...
    b.doSomething();
    //...
})
```

#### **import和require区别**

import和require都是被**模块化使用**。

- `require`是CommonJs的语法（AMD规范引入方式），**CommonJs的模块是对象**。`import`是**es6的一个语法标准**（浏览器不支持，**本质是使用node中的babel将es6转码为es5再执行，import会被转码为require**），**es6模块不是对象**。（语法规范上的不用，是否是对象）
- `require`是**运行时加载**整个模块（即模块中所有方法），生成一个对象，再从对象上读取它的方法（只有运行时才能得到这个对象，**不能在编译时做到静态化**），理论上可以用在代码的任何地方。`import`是**编译时调用，确定模块的依赖关系**，输入变量（es6模块不是对象，而是通过export命令指定输出代码，再通过import输入，只加载import中导的方法，其他方法不加载），**import具有提升效果**，会提升到模块的头部（编译时执行）（加载特性上的区别，运行时加载or编译时确定）

**export和import可以位于模块中的任何位置，但是必须是在模块顶层**，如果在其他作用域内，会报错(es6这样的设计可以提高编译器效率，但没法实现运行时加载)。

- `require`是**赋值过程**，把require的结果（对象，数字，函数等），默认是export的一个对象，赋给某个变量（复制或**浅拷贝**）。`import`是**解构过程**（需要谁，加载谁）。（本质的区别）

```
// require: 真正被require出来的是来自module.exports指向的内存块内容
const a = require('a') //

// exports: 只是 module.exports的引用，辅助module.exports操作内存中的数据
exports.a = a 
module.exports = a
// import
import a from 'a';
import { default as a  } from 'a';
import  *  as a  from 'a';
import { fun1,fun2 } from 'a';
// export
export default a;
export const a = 1;
export functon a { ... };
export { fun1, fun2 };
```

#### **ES6 模块与 CommonJS 模块的差异**

- CommonJS 模块输出的是一个**值的拷贝**，ES6 模块输出的是**值的引用**。

- CommonJS 模块是**运行时加载**，ES6 模块是**编译时输出接口**。

  因为 CommonJS **加载的是一个对象**（即module.exports属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的**对外接口只是一种静态定义，在代码静态解析阶段就会生成**。

- CommonJS 模块的require()是**同步加载模块**，ES6 模块的import命令是**异步加载**，有一个独立的模块依赖的解析阶段。

#### 相关题目

题目1. 关于 javascript 模块化

模块化有利于管理模块间的依赖，更依赖模块的维护

主流的模块化包括CommonJS,AMD,CMD等

AMD推崇**依赖前置**，CMD推崇**依赖就近**

Sea.js遵循AMD规范，RequireJS遵循CMD规范（False）

> 解析

AMD 是 RequireJS 在推广过程中对模块定义的规范化产出。 CMD 是 SeaJS 在推广过程中对模块定义的规范化产出。

[AMD](https://github.com/amdjs/amdjs-api/wiki/AMD)是"Asynchronous Module Definition"的缩写，意思就是"异步模块定义"。它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。 AMD也采用require()语句加载模块，但是不同于CommonJS。 主要有两个Javascript库实现了AMD规范：[require.js](http://requirejs.org/)和[curl.js](https://github.com/cujojs/curl)。 参考链接：http://www.ruanyifeng.com/blog/2012/10/asynchronous_module_definition.html

```
// CMD
define(function(require, exports, module) {
var a = require('./a')
a.doSomething()
// 此处略去 100 行
var b = require('./b') // 依赖可以就近书写
b.doSomething()
// ...
})

// AMD 默认推荐的是
define(['./a', './b'], function(a, b) { // 依赖必须一开始就写好
a.doSomething()
// 此处略去 100 行
b.doSomething()
...
})
```

