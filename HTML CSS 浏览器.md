# HTML CSS 浏览器相关

## HTML

### HTML5 新特性

#### HTML5有哪些新特性？

**新增特性**：

- 拖放（Drag and drop）API
- 语义化更好的内容标签
- 音频、视频API
- 画布（Canvas）API
- 地理（Geolocation）API
- 本地离线存储（localStorage），长期数据存储，浏览器关闭后数据不会丢失
- 会话存储（sessionStorage），数据在浏览器关闭后自动删除
- 表单控件（calendar、date、time、email、url、search）
- 新的技术包括webwork、websocket、Geolocation

#### 什么是HTML5

HTML5是最新的**HTML标准**，它的主要目标是**提供所有内容，而不需要任何Flash、 SilverLight等的额外插件，这些内容来自动画、视频、富GUI等**

HTML5是万维网联盟（W3C）和网络超文本应用技术工作组（ WHATWG）合作输出的。

### 自带方法

#### HTML生命周期

> 生命周期的三个重要事件

- `DOMContentLoaded` —— 浏览器已经**完全加载了 HTML，DOM 树已经构建完毕**，但是像是 ![img]() 和样式表等**外部资源可能并没有下载完毕**。
- `load` —— 浏览器**已经加载了所有的资源**（图像，样式表等）。
- `beforeunload` —— 当用户**即将离开当前页面（刷新或关闭）时触发**。正要去服务器读取新的页面时调用，此时还没开始读取；
- `unload` —— 在用户**离开页面后触发**。从服务器上读到了需要加载的新的页面，在即将替换掉当前页面时调用。

> 各个重要事件的常见用途

- `DOMContentLoaded` —— DOM 加载完毕，所以 **JS 可以访问所有 DOM 节点**，**初始化界面**。
- `load` —— 附加资源已经加载完毕，可以在此事件触发时**获得图像的大小**（如果没有被在 HTML/CSS 中指定）
- `beforeunload` —— 该事件可**用于弹出对话框**，提示用户是继续浏览页面还是离开当前页面。
- `unload` —— **删除本地数据**localstorage等

> DOMContentLoaded

- DOMContentLoaded 由 **document 对象**触发。使用 addEventListener 来监听它：

```
document.addEventListener("DOMContentLoaded", () => {});
```

**注意点**:

- **DOMContentLoaded 和脚本**

当浏览器在解析 HTML 页面时遇到了 `<script>...</script>` 标签，将无法继续构建DOM树（**UI 渲染线程与 JS 引擎是互斥的**，当 JS 引擎执行时 UI 线程会被挂起），必须立即执行脚本。**所以 `DOMContentLoaded` 有可能在所有脚本执行完毕后触发。**

外部脚本（带 `src` 的）的加载和解析也会**暂停DOM树构建**，**所以 `DOMContentLoaded` 也会等待外部脚本**。带 `async` 的外部脚本，可能会在DOMContentLoaded事件之前或之后执行。带 `defer` 的脚本肯定会在`DOMContentLoaded`事件之前执行。

- **DOMContentLoaded 与样式表**

外部样式表并**不会阻塞 DOM 的解析**，所以 `DOMContentLoaded` 并不会被它们影响。

> load

`window` 对象上的 `load` 事件在所有文件**包括样式表，图片和其他资源下载完毕**后触发。

```
window.addEventListener('load', function(e) {...});
window.onload = function(e) { ... };
```

> beforeunload

当窗口即将被卸载（关闭）时, 会触发该事件。此时**页面文档依然可见**, 且该**事件的默认动作可以被取消**。beforeunload在unload之前执行，它还可以阻止unload的执行。

```
// 推荐使用
window.addEventListener('beforeunload', (event) => {
  // Cancel the event as stated by the standard.
  event.preventDefault();
  // Chrome requires returnValue to be set.
  event.returnValue = '关闭提示';
});
```

> unload

用户离开页面的时候，`window` 对象上的 `unload` 事件会被触发，无法阻止用户转移到另一个页面上。

> 页面加载状态

`document.readyState` 表示页面的加载状态，有三个值：

- `loading` 加载 —— **document仍在加载**。
- `interactive` 互动 —— **文档已经完成加载**，文档已被解析，但是诸如图像，样式表和框架之类的**子资源仍在加载**。
- `complete` —— **文档和所有子资源已完成加载**。 `load` 事件即将被触发。

可以在 `readystatechange` 中追踪页面的变化状态：

```
document.addEventListener('readystatechange', () => {  console.log(document.readyState);});
```

#### 本地存储相关

##### webstorage

webstorage是本地存储，存储在客户端，包括localStorage和sessionStorage。

##### localStorage

> localStorage**生命周期是永久**，这意味着除非用户显示在浏览器提供的UI上清除localStorage信息，否则这些信息将永远存在。存放数据大小为一般为**5MB**,而且它仅在客户端（即浏览器）中保存，**不参与和服务器的通信**。

##### sessionStorage

> sessionStorage仅在**当前会话下有效**，关闭页面或浏览器后被清除。存放数据大小为一般为**5MB**,而且它仅在客户端（即浏览器）中保存，**不参与和服务器的通信**。源生接口可以接受，亦可再次封装来对Object和Array有更好的支持。

localStorage和sessionStorage使用时**使用相同的API**：

```
localStorage.setItem("key","value");//以“key”为名称存储一个值“value”

localStorage.getItem("key");//获取名称为“key”的值

localStorage.removeItem("key");//删除名称为“key”的信息。

localStorage.clear();//清空localStorage中所有信息
```

如果你想用[JSON](https://link.zhihu.com/?target=http%3A//caibaojian.com/t/json)格式存储，那么可以使用“JSON.stringify”函数，如下面所示的代码。

```
localStorage.setItem(key,JSON.stringify(country));
```

##### 两者作用域不同

不同浏览器无法共享localStorage或sessionStorage中的信息。**相同浏览器的不同页面间可以共享相同的 localStorage（页面属于相同域名和端口）**，但是不同页面或标签页间无法共享sessionStorage的信息。这里需要注意的是，页面及标签页仅指顶级窗口，**如果一个标签页包含多个iframe标签且他们属于同源页面，那么他们之间是可以共享sessionStorage的**。

##### Cookie

> 生命期为只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。 存放数据大小为**4K左右** 。有个数限制（各浏览器不同），**一般不能超过20个**。与服务器端通信：每次都会携带在HTTP头中，如果使用cookie保存过多数据会带来性能问题。但Cookie需要程序员自己封装，**源生的Cookie接口不友好**([http://www.jb51.net/article/6...](http://www.jb51.net/article/64330.htm))

##### 本地存储和cookies之间的区别是什么？

- cookies存储的数据，客户端/服务器端既**可以从客户端也可以从服务器端访问**。每个请求都会发送cookie数据到服务器。
- 本地存储只能在本地浏览器端访问数据。**服务器无法访问本地存储**，除非特意通过POST或GET发送到服务器。大小每个Cookie 4095个字节。每个域5 MB。
- **有效期**：cookie有附加的有效期。所以有效期后的cookie和cookie数据会被删除。本地存储数据没有有效期限。要么最终用户从浏览器删除它，要么使用JavaScript编程删除。
- cookie需要指定作用域，**不可以跨域调用**。
- cookie需要前端开发者自己封装 setCookie和 getCookie。但 cookie也是不可或缺的，因为 cookie的作用是与服务器进行交互，并且还是HTTP规范的一部分，而 localStorage 仅因为是为了在本地“存储”数据而已，无法跨浏览器使用。

##### cookie 和 session 的区别是什么

区别如下：

- cookie数据存放在客户的浏览器上， session数据存放在**服务器**上。
- cookie不是很安全，别人可以分析存放在本地的 cookie并进行 **cookie欺骗**。考虑到安全问题应当使用 session。
- session会**在一定时间内保存在服务器上**。**当访问增多时，会占用较多服务器的资源**。为了减轻服务器的负担，应当使用 cookie。
- 单个 cookie保存的数据不能超过4KB，很多浏览器都限制一个站点最多保存20个 cookie。

**建议可以将登录信息等重要信息存放在 session中，其他信息（如果需要保留）可以存放在 cookie中。**

#### Web Worker相关

##### web workers是什么，为什么我们需要web workers？

Web Worker 的作用，就是为 JavaScript **创造多线程环境**，**允许主线程创建 Worker 线程，将一些任务分配给后者运行**。在主线程运行的同时，Worker 线程在后台运行，**两者互不干扰**。**等到 Worker 线程完成计算任务，再把结果返回给主线程**。这样的好处是，一些计算密集型或高延迟的任务，被 Worker 线程负担了，主线程（通常负责 UI 交互）就会很流畅，不会被阻塞或拖慢。

Worker 线程**一旦新建成功，就会始终运行**，不会被主线程上的活动（比如用户点击按钮、提交表单）打断。这样有利于随时响应主线程的通信。但是，这也造成了 Worker **比较耗费资源**，不应该过度使用，而且一旦使用完毕，就应该关闭。

**使用方式**

（1）通过 worker= new Worker（url）加载一个 JavaScript文件，创建一个 Worker，同时返回一个 Worker实例。由于 Worker 不能读取本地文件，所以这个脚本必须来自网络。

（2）然后，主线程调用`worker.postMessage()`方法，向 Worker 发消息。

（3）`worker.postMessage()`方法的参数，就是主线程传给 Worker 的数据。它可以是各种数据类型，包括二进制数据。

（4）主线程通过`worker.onmessage`指定监听函数，接收子线程发回来的消息。

（5）主线程使用 worker.terminate()终止一个 Worker的执行。

**例子**

请看下面的for循环代码，它将运行超过百万次。

```
function  SomeHeavyFunction()
{
    for (i = 0; i < 10000000000000; i++)
    {
    x = i + x;
    }
}
```

假设上述for循环代码在一个HTML按钮点击上执行。现在，这种方法执行是同步的。换句话说，完整的浏览器会一直等，直到循环完成。

```
<input type="button" onclick="SomeHeavyFunction();" />
```

这会进一步导致浏览器冻结，并出现如下图所示的错误信息而无法响应。

![img](https://pic1.zhimg.com/80/v2-c66cbbb486ca4299d80b19357458797c_1440w.jpg)

因此，如果我们可以将这个烦琐的for循环到一个[JavaScript](https://link.zhihu.com/?target=http%3A//caibaojian.com/t/javascript)文件中，并异步运行，那么就意味着浏览器不必等待循环，这样我们就能拥有一个更敏感的浏览器。这就是web worker的目的。

web worker有助于**异步执行JavaScript文件**。

#### WebSocket相关

##### 相关概念

它是Web应用程序的传输协议，提供了双向的、按序到达的数据流。它是HTML5新増的协议， WebSocket**的连接是持久的**，它在客户端和服务器之间**保持双工连接**，**服务器的更新可以及时推送到客户端**，而不需要客户端以一定的时间间隔去轮询。

其他特点包括：

（1）建立在 TCP 协议之上，服务器端的实现比较容易。

（2）与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

（3）数据格式比较轻量，性能开销小，通信高效。

（4）可以发送文本，也可以发送二进制数据。

（5）没有同源限制，客户端可以与任意服务器通信。

（6）协议标识符是`ws`（如果加密，则为`wss`），服务器网址就是 URL。

##### 简单示例

WebSocket 的用法相当简单。

> ```
> var ws = new WebSocket("wss://echo.websocket.org");
> 
> ws.onopen = function(evt) { 
>   console.log("Connection open ..."); 
>   ws.send("Hello WebSockets!");
> };
> 
> ws.onmessage = function(evt) {
>   console.log( "Received Message: " + evt.data);
>   ws.close();
> };
> 
> ws.onclose = function(evt) {
>   console.log("Connection closed.");
> };     
> ```

### 标签

#### HTML5标签相关

##### HTML 5的页面结构和HTML 4或早先的HTML有什么不同？

一个典型的Web页面有页眉（header），页脚（footer），导航（navigation），正文（central area）和侧栏（side bar）。现在如果是在HTML 4中，HTML部分中的上述这些专用名词**需要使用DIV标签来描述**。

但是，如果是在HTML 5，可以专门为这些区域创建特定的元素名，让HTML更具可读性。

- `<header>`：表现HTML的标题数据。
- `<footer>`：页面的页脚部分。
- `<nav>`：页面中的导航元素。
- `<article>`：正文内容。
- `<section>`：用在正文中定义section或区段内容。
- `<aside>`：表现页面侧边栏内容。

##### HTML 5中的DataList是什么？

HTML 5中的DataList控件元素有助于提供自动完成功能的文本框，如下图所示。

![img](https://pic3.zhimg.com/80/v2-eb9973a63926ebc707fed492ace1e136_1440w.png)

下面是DataList控件功能的HTML代码：

```
<input list="Country">
<datalist id="Country">
<option value="India">
<option value="Italy">
<option value="Iran">
<option value="Israel">
<option value="Indonesia">
</datalist>
```

##### HTML 5中不同的新表单元素类型是什么？

HTML 5推出了10个重要的新的表单元素：

1. Color（拾色器对话框）
2. Date（日历对话框）
3. Datetime-local（本地时间显示日历）
4. Email
5. Time
6. Url
7. Range（显示范围调整控件）
8. Telephone
9. Number
10. Search（搜索引擎框）

##### HTML 5中的输出元素是什么？

当你需要计算两个输入的结果并将结果放到一个标签里的时候，就需要输出元素了。比如你有两个文本框（参见下图），你想要让这些文本框数字相加，然后输出给标签。

![img](https://pic1.zhimg.com/80/v2-6b16326d7340ef4c480c78af3141da98_1440w.png)

下面就是如何使用HTML 5代码输出元素。

```
<form onsubmit="return false"  &ouml;ninput="o.value = parseInt(a.value) + parseInt(b.value)">
<input name="a" type="number"> +
<input name="b" type="number"> =
<output name="o" />
</form>
```

为了简单起见，你也可以用“valueAsNumber”替换“parseInt”。为了更具可读性，你也可以在输出元素中使用“for”。

```
<output name="o" for="a b"></output>
```

##### **HTML5 Canvas元素有什么作用？**

Canvas元素用于在网页上绘制图形，该元素标签的强大之处在于可以直接在HTML上进行图形操作。

##### **如何在HTML5页面中嵌入音频和视频？**

HTML5包含了嵌入音频文件的标准方式，支持的格式包括MP3、Wav和Ogg等，嵌入方式如下。

```
<audio controls>
	<source src="icketang.mp3" type="audio/mpeg">
	Your browser does'nt support audio embedding feature.
</audio>
```

和嵌入音频文件一样，HTML5定义了嵌入视频的标准方式，支持的格式包括MP4、WebM和Ogg等，嵌入方式如下。

```
<video width=”450” height=”340” contro1s>
	<source src="icketang.mp4"  type="video/mp4">
	Your browser does'nt support video embedding feature.
</video>
```

##### **HTML5应用缓存？**

首先搞清楚HTML5中的应用缓存是什么？

HTML5应用缓存的**最终目的是帮助用户离线浏览页面**。换句话说，如果网络连接不可用，打开的页面就来自浏览器缓存，离线应用缓存可以帮助用户达到这个目的。

应用缓存**可以帮助用户指定哪些文件需要缓存**，哪些不需要

HTML5应用缓存最关键的就是支持离线应用，可获取少数或者全部网站内容，包括HTML、CSS、图像和 JavaScript脚本并存在本地。该特性提升了网站的性能，可通过如下方式实现。

```
<！doctype html>
<html manifest="example.appcache">
    ......
</html>
```

每个指定了 manifest 的页面在用户对其访问时都会被缓存。如果未指定 manifest 属性，则页面不会被缓存（除非在 manifest 文件中直接指定了该页面）。

应用程序缓存为应用带来**3个优势**。

（1）**离线浏览**，让用户可在应用离线时（网络不可用时）使用它们。

（2）**速度**，让已缓存资源加载得更快。

（3）**减少服务器负载**，让浏览器将只下载服务器更新过的资源。

**与传统的浏览器缓存相比**

与传统的浏览器缓存比较，该特性并不强制要求用户访问网站。

#### script标签相关

> script标签的属性

| 属性      | 值               | 描述                                                         |
| --------- | ---------------- | ------------------------------------------------------------ |
| async     | async            | 立即下载脚本（仅适用于外部脚本）。                           |
| charset   | *charset*        | 表示通过src属性指定的代码的字符集                            |
| defer     | defer            | 表示脚本可以延迟到文档完全被解析和显示之后再执行（仅适用于外部脚本）。 |
| language  | *script*(已废弃) | 表示编写代码使用的脚本语言。用 type 属性代替它。             |
| src       | *URL*            | 规定外部脚本文件的 URL。                                     |
| xml:space | preserve         | 规定是否保留代码中的空白。                                   |
| type      | text/xxx         | language的替换属性，表示编写代码使用的脚本语言的内容类型,也称为MIME属性。 |

没有 `defer` 或 `async`，所有<script>元素会按照在页面**出现的先后顺序**依次被解析，浏览器会立即加载并执行指定的脚本, 只有解析完前面的script元素的内容后，才会解析后面的代码。 **`async` 和 `defer` 属性仅仅对外部脚本起作用，在 `src` 不存在时会被自动忽略。**

**关于`defer`属性**:

有了defer属性，加载后续文档的过程和js脚本的加载(此时仅加载不执行)是**并行进行**的(异步)，js脚本的执行需要等到**文档所有元素解析完成之后**，**DOMContentLoaded事件触发执行之前**。

**关于`async`属性**:

有了async属性，表示后续文档的加载和渲染与js脚本的**加载和执行**是**并行进行**的，即异步执行；

**两者的异同**：

1. defer和async在网络加载过程是一致的，**都是异步执行的**；
2. 两者的**区别在于脚本加载完成之后何时执行**，可以看出**defer更符合大多数场景对应用脚本加载和执行的要求**；
3. 如果存在多个有defer属性的脚本，那么它们是按照加载顺序执行脚本的；而对于async，它的加载和执行是紧紧挨着的，无论声明顺序如何，只要加载完成就立刻执行，它对于应用脚本用处不大，因为它完全不考虑依赖。

![image-20210317154521975](HTML CSS相关.assets/image-20210317154521975.png)

> script标签的使用方式

**1.页面中嵌入script代码， 只需指定type属性**

```
<script type="text/javascript">
  function sayHi() {
    console.log('hihihi');
    // 内部不能出现'</script>'字符串，如果必须出现，必须使用转义标签‘\’
    alert('<\/script>');
  }
</script>
```

包含在<script>元素内的代码会从上而下依次解释，在解释器对<script>元素内的所有代码求值完毕之前，**页面中的其余内容都不会被浏览器加载或显示**

**2.包含外部js文件, src属性是必须的。**

```
<script src="example.js"></script>
// 带有src属性的元素不应该在标签之间包含额外的js代码，即使包含，只会下载并执行外部文件，内部代码也会被忽略。
```

与嵌入式js代码一样, 在解析外部js文件时，**页面的处理会暂时停止**。

3.浏览器对于带有`type="module"`的<script>，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，**等同于打开了<script>标签的defer属性**。

#### meta标签相关

> meta标签的功能

- META标签是HTML标记HEAD区的一个关键标签，它提供的信息虽然用户不可见，但却是文档的**最基本的元信息**。
- `<meta>`除了提供**文档字符集、使用语言、作者**等网页相关信息外，还可以**设置信息给搜索引擎**，目的是为了SEO（搜索引擎优化）。
- HTML 元素表示那些**不能**由其它 HTML 元相关（meta-related）元素（(、, <script>、<style> 或 <title>）之一表示的任何元数据信息。

> 属性

- **name** 设置元数据的名称。`name` 和 `content` 属性可以一起使用，以**名-值对**的方式给文档提供元数据，content 作为元数据的值。

- **content** 设置与 http-equiv 或 name 属性**相关的元信息**。

- **charset** 声明了**文档的字符编码**。如果使用了这个属性，其值**必须**是与ASCII大小写无关（ASCII case-insensitive）的"`utf-8`"。

- http-equiv

   

  定义了一个

  编译指示指令

  ，其作用

  类似于http协议

  , 告诉浏览器一些关于字符设定,页面刷新,cookie,缓存等等相关信息。属性名叫做 http-equiv 是因为

  所有允许的值都是HTTP头部的名称

  。可设置的值有：

  - content-security-policy：它允许页面作者定义当前页的内容策略。内容策略主要指定允许的服务器源和脚本端点，这有助于防止跨站点脚本攻击。
  - Expires：可以用于设定网页的到期时间，一旦过期则必须到服务器上重新调用。content必须使用GMT时间格式；
  - content-type：如果使用这个属性，其值必须是"`text/html; charset=utf-8`"。注意：该属性只能用于 MIME type为 `text/html` 的文档，不能用于MIME类型为XML的文档。
  - default-style：设置默认CSS 样式表组的名称。
  - refresh：定时让网页在指定的时间n内，刷新或跳转； 如果 `content` 只包含一个正整数，则是n秒后, 页面刷新。 如果 `content` 包含一个正整数，并且后面跟着字符串 '`;url=`' 和一个合法的 URL，则是重定向到指定链接的时间间隔(秒)。

> `meta` 元素定义的元数据的类型

- 如果设置了 `name` 属性，`meta` 元素提供的是**文档级别**（*document-level*）的元数据，应用于整个页面。
- 如果设置了 `http-equiv` 属性，`meta` 元素则是**编译指令**，提供的信息与类似命名的HTTP头部相同。
- 如果设置了 `charset` 属性，`meta` 元素是一个**字符集声明**，告诉文档使用哪种字符编码。
- 如果设置了 `itemprop` 属性，`meta` 元素**提供用户定义的元数据**。

> `meta`元素的使用方式

content值里有**多个属性通过`,`隔开**，同时设置多个属性。

```
/* name */
// 适配移动设备
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no" />
// 检测html格式：禁止把数字转化为拨号链接
<meta name="format-detection" content="telephone=no" /> 

/* charset */
<meta charset="utf-8">

/* http-equiv */
<meta http-equiv="refresh" content="3;url=https://www.mozilla.org">
<meta http-equiv="Expires" content="Mon,12 May 2001 00:20:00 GMT">
```

##### viewport 元信息

> viewport的定义

viewport 是**浏览器的可视区域**，可视区域的大小是浏览器自己设置的。它可能大于移动设备可视区域，也可能小于移动设备可视区域。一般来讲，移动设备上的viewport都是大于移动设备可视区域。**在控制台输出`window.innerWidth`查看Viewport大小**。

> 相关概念

**设备像素：\**设备\**屏幕**分辨率。iphone6p 的分辨率是 1334 * 750； **设备独立像素：\**设备上\**程序**用来描绘数据的一个个的“点”, 在控制台用 `screen.width/height`查看。iphone6p 的设备独立像素是375 * 667； **设备像素比（DPR）：**设备像素(宽)/设备独立像素(宽)，**DPR越高渲染越精致**。在控制台输出`window.devicePixelRatio`查看设备像素比。iphone6s 的设备像素比就是 750 / 375 = 2； **CSS像素：**浏览器使用的单位，用来精确度量网页上的内容。在一般情况下(页面缩放比为 1)，1 个 CSS 像素等于 1 个设备独立像素。 **屏幕尺寸：\**屏幕\**对角线**的长度，以英尺为单位。 **像素密度（PPI）**：每英寸屏幕拥有的像素数。

> 为什么要使用meta viewport？

通常情况下，移动设备上的浏览器都会把viewport设为980px或1024px（默认），此时页面**会出现横向滚动条**，因为移动设备可视区域宽度是比这个默认的viewport的宽度要小。所以出现了meta 标签设置viewport 元信息**进行移动端网页优化**。

> viewport的属性

- **width**：控制 viewport 的大小，可以给它指定一个值(正整数)，或者是一个特殊的值(如：device-width 设备独立像素宽度，单位缩放为 1 时)；
- **initial-scale**：初始缩放比例，即当页面第一次加载时的缩放比例，为一个数字(可以带小数)；
- **maximum-scale**：允许用户缩放到的最大比例，为一个数字(可以带小数)；
- **minimum-scale**：允许用户缩放到的最小比例，为一个数字(可以带小数)；
- **user-scalable**：是否允许用户手动缩放，值为 "no"(不允许) 或 "yes"(允许)；
- **height**：与 width 相对应(很少使用)。

## CSS

### 概念

#### CSS3选择器

```
·id选择器（#myId）
·类选择器（.myClassName）
·标签选择器（div, h1, p）
·后代选择器（h1 p）
·相邻后代选择器（子）选择器（ul > li）
·兄弟选择器（li~a）
·相邻兄弟选择器（li+a）
·属性选择器（a[rel="external"]）
·伪类选择器（a:hover, li:nth-child）
·伪元素选择器（::before, ::after）
·通配符选择器（*）
```

**属性选择器**

在HTML中，通过各种各样的属性可以给元素增加很多附加的信息。例如，通过id属性可以将不同div元素进行区分。

```
input[type="text"]
{
  width:150px;
  display:block;
  margin-bottom:10px;
  background-color:yellow;
  font-family: Verdana, Arial;
}
```

在CSS2中引入了一些属性选择器，而CSS3在CSS2的基础上对属性选择器进行了扩展，新增了3个属性选择器，使得属性选择器有了**通配符**的概念，这三个属性选择器与CSS2的属性选择器共同构成了CSS功能强大的属性选择器。如下表所示：

![这里写图片描述](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMgAAADICAAAAACIM/FCAAACh0lEQVR4Ae3ch5W0OgyG4dt/mQJ2xgQPzJoM1m3AbALrxzrf28FzsoP0HykJEEAAAUQTBBBAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQAABBBBAAAEEkKK0789+GK/I2ezfQB522PnS1qc8pGgXvr4tE4aY0XOUWlGImThWgyCk6DleixzE7qwBkg/MGiDPlVVAyp1VQGrPKiACDhFI6VkF5LmzCki+sg7IwDoglnVAil0IMkeG9CyUiwsxLFUVFzJJOQaKCjFCDN9RXMjIX7W6ztZXZDKKCyn8sWJvH+nca7WHDN9lROlAliPH9iRKCPI4cswFJQWxB46toLQgQ9jhn5QYZA9DOkoMUoQde5YapAxDWkoNYsOQR3KQd9CxUnIQF4S49CB9ENKlBxmDEKsFUgMCCCCAAHIrSF61f6153Ajy8nyiPr8L5MXnmm4CyT2fzN4DUvHZ+ntA2tOQBRBAAAEEEEAAAQQQ7ZBaC6TwSiDUaYHQ2yuB0MN+ft+43whyrs4rgVCjBUKTFshLC6TUAjGA3AxSaYFYLZBOC2RUAsk8h5qTg9QcbEoOsoQhQ2qQhsO5xCD5dgB5JQaZ+KBKGtKecvR81Ic0ZDjByKdDx0rSEDZ/djQbH+bkIdvfJFm98BfV8hD2zprfVdlu9PxVeyYAkciREohRAplJCaRSAplJCcQogTjSAdlyHRBvSAekJR0QRzogA+mADJkOiCPSAPEtqYBshlRAXC43hxix2QiOuEZkVERykGyNo9idIZKE0HO7XrG6OiMShlDWjstVzdPgXtUH9v0CEidAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQP4HgjZxTpdEii0AAAAASUVORK5CYII=)

**CSS3 结构性伪类选择器—nth-child(n)**

`:nth-child(n)`选择器用来定位某个父元素的一个或多个特定的子元素。其中“n”是其参数，而且可以是整数值(1,2,3,4)，也可以是表达式(2n+1、-n+5)和关键词(odd、even)，但参数n的起始值始终是1，而不是0。也就是说，参数n的值为0时，选择器将选择不到任何匹配的元素。

经验与技巧:当“:nth-child(n)”选择器中的n为一个表达式时，其中n是从0开始计算，当表达式的值为0或小于0的时候，不选择任何匹配的元素。如下表所示： ![这里写图片描述](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMgAAADICAAAAACIM/FCAAACh0lEQVR4Ae3ch5W0OgyG4dt/mQJ2xgQPzJoM1m3AbALrxzrf28FzsoP0HykJEEAAAUQTBBBAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQAABBBBAAAEEkKK0789+GK/I2ezfQB522PnS1qc8pGgXvr4tE4aY0XOUWlGImThWgyCk6DleixzE7qwBkg/MGiDPlVVAyp1VQGrPKiACDhFI6VkF5LmzCki+sg7IwDoglnVAil0IMkeG9CyUiwsxLFUVFzJJOQaKCjFCDN9RXMjIX7W6ztZXZDKKCyn8sWJvH+nca7WHDN9lROlAliPH9iRKCPI4cswFJQWxB46toLQgQ9jhn5QYZA9DOkoMUoQde5YapAxDWkoNYsOQR3KQd9CxUnIQF4S49CB9ENKlBxmDEKsFUgMCCCCAAHIrSF61f6153Ajy8nyiPr8L5MXnmm4CyT2fzN4DUvHZ+ntA2tOQBRBAAAEEEEAAAQQQ7ZBaC6TwSiDUaYHQ2yuB0MN+ft+43whyrs4rgVCjBUKTFshLC6TUAjGA3AxSaYFYLZBOC2RUAsk8h5qTg9QcbEoOsoQhQ2qQhsO5xCD5dgB5JQaZ+KBKGtKecvR81Ic0ZDjByKdDx0rSEDZ/djQbH+bkIdvfJFm98BfV8hD2zprfVdlu9PxVeyYAkciREohRAplJCaRSAplJCcQogTjSAdlyHRBvSAekJR0QRzogA+mADJkOiCPSAPEtqYBshlRAXC43hxix2QiOuEZkVERykGyNo9idIZKE0HO7XrG6OiMShlDWjstVzdPgXtUH9v0CEidAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQP4HgjZxTpdEii0AAAAASUVORK5CYII=)

**CSS3选择器 :enabled和:disabled选择器**

在Web的表单中，有些表单元素有可用（“:enabled”）和不可用（“:disabled”）状态，比如输入框，密码框，复选框等。在默认情况之下，这些表单元素都处在可用状态。那么我们可以通过伪选择器`:enabled`对这些表单元素设置样式。

`:disabled`选择器刚好与`:enabled`选择器相反，用来选择不可用表单元素。要正常使用`:disabled`选择器，需要在表单元素的HTML中设置“disabled”属性。

**CSS3选择器 :checked选择器**

在表单元素中，单选按钮和复选按钮都具有选中和未选中状态。（大家都知道，要覆写这两个按钮默认样式比较困难）。在CSS3中，我们可以通过状态选择器`:checked`配合其他标签实现自定义样式。而`:checked`表示的是选中状态。

**CSS3选择器 ::selection选择器**

`::selection`伪元素是用来匹配突出显示的文本(用鼠标选择文本时的文本)。浏览器默认情况下，用鼠标选择网页文本是以“深蓝的背景，白色的字体”显示的。

**CSS3选择器 ::before和::after**

`::before`和`::after`这两个主要用来给元素的前面或后面插入内容，这两个常和”content”配合使用，使用的场景最多的就是清除浮动。

##### **::before 和 :after 中双冒号和单冒号的区别？这2个伪元素的作用？**

```
·在 CSS3 中 : 表示伪类， :: 表示伪元素
·想让插入的内容出现在其他内容前，使用::befroe。否则，使用::after
```

#### CSS属性

##### 常见的公共样式配置

```
html {
  font-size: 62.5%;
}
body {
  margin: 0;
}
* {
    box-sizing: border-box;
}
h1,
h2,
h3,
h4,
h5,
h6,
p {
    font-weight: normal;
    margin: 0;
}

a {
    text-decoration: none;
    color: #000;
}

ul {
    padding: 0;
    margin: 0;
    list-style: none;
}
    
img {
    width: 100%;
}

.clearfix::after {
    content: '',
    display: block;
    clear: both;
}
```

##### width:auto和width:100%的区别

- width、height使用固定值是一定会显示的，但是除非是小型项目或是特殊情况，最好不要使用固定值。不利于响应式开发，当从移动端看页面就会非常不美观。
- 不设置width、height时默认为auto，当position不同时显示效果不同，浮动可能会导致其不显示，需要清除浮动。
  - width:auto表示宽度是可变动的，这个div的所有部分（content+margin+padding+border）相加为父元素的width大小，。
  - height:auto表示高度可变动的，如果div设置了auto但是却没有显示有三种可能：
    - cotent里没有能将其height支撑的子元素
    - 由于定位和浮动导致其不显示，清除浮动或修改定位
- width、height强制将子元素充满父元素的content。
  - width:100%子元素的width值为父元素的width值，加margin时不改变子元素width值大小，而是溢出父元素。
  - height:100%不显示的原因可能为没有设置父元素的height，可以通过将父元素的height设为固定值或是将父元素及父元素的父元素设置height:100%显示。
  - auto与**子元素**有关，100%与**父元素**有关

##### 关于 margin: auto

auto 在任何情况下，只会取下面两种值中的一个：

1. 父元素剩余空间的宽度
2. 0

> 当元素的布局方式为 **static/relative** 且**宽高已知**时，auto 取1中的值；当元素的布局方式为 **position/absolute/fixed** 或者 **float/inline** 或者**宽高未知**时，auto 就取 2中的值。

**注意:**

以上 auto 的取值均指**水平方向**，垂直方向上，auto **不会自动填充**。

##### 流体特性和 margin: auto

当一个**绝对定位元素**，**其对立定位方向属性同时有具体定位数值**的时候，流体特性就发生了。

下述代码是同时实现水平和垂直居中的例子

```
#center {
  background-color: red;
  width: 200px;
  height: 200px;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  margin: auto;
}
```

注意，这里的 left、right、top、bottom 的值其实没有实际的大小描述意义。我们只要确认其存在性就行，就算不是 0，也可以激发其流体特性。

**注意：**如果没有给元素指定宽高，元素的宽高会根据实际的定位值进行拉伸填充

##### overflow

- css属性overflow属性定义溢出元素内容区的内容会如何处理。如果值为 scroll，不论是否需要，用户代理都会提供一种滚动机制。（True）

##### Box-sizing

- content-box：这是默认情况，整个元素的高度和宽度就是元素内容
- border-box：这种情况下，你设置的width和height属性值就是针对整个元素，包括了border，padding，和元素内容。

##### 多媒体查询语法

多媒体查询由多种媒体组成，可以包含一个或多个表达式，表达式根据条件是否成立返回 true 或 false。

```
@media not|only mediatype and (expressions) { CSS 代码...; }
```

如果指定的多媒体类型匹配设备类型则查询结果返回 true，文档会在匹配的设备上显示指定样式效果。

除非你使用了 not 或 only 操作符，否则所有的样式会适应在所有设备上显示效果。

- **not:** not是用来排除掉某些特定的设备的，比如 @media not print（非打印设备）。
- **only:** 用来定某种特别的媒体类型。对于支持Media Queries的移动设备来说，如果存在only关键字，移动设备的Web浏览器会忽略only关键字并直接根据后面的表达式应用样式文件。对于不支持Media Queries的设备但能够读取Media Type类型的Web浏览器，遇到only关键字时会忽略这个样式文件。
- **all:** 所有设备，这个应该经常看到。

你也可以在不同的媒体上使用不同的样式文件：

```
<link rel="stylesheet" media="mediatype and|not|only (expressions)" href="print.css">
```

##### **CSS中哪些属性可以继承？**

- 每一个属性在定义中都给出了这个属性是否具有继承性，一个**具有继承性的属性会在没有指定值的时候，会使用父元素的同属性的值来作为自己的值**。
- 一般具有继承性的属性有，**字体相关的属性**，font-size和font-weight等。
- **文本相关的属性**，color和text-align等。
- **表格的一些布局属性、列表属性**如list-style等。
- 还有**光标属性cursor**、**元素可见性**visibility。
- 当一个属性不是继承属性的时候，我们也可以通过将它的值设置为**inherit**来使它从父元素那获取同名的属性值来继承

##### calc 计算的使用

CSS3 的 `calc()` 函数允许我们**在属性值中执行数学计算操作**。例如，我们可以使用 `calc()` 指定一个元素宽的固定像素值为多个数值的和。

```
.foo {
  width: calc(100px + 50px);
}
```

首先，我们能够组合不同的单元。特别是，我们可以**混合计算绝对单元（比如百分比与视口单元）与相对单元（比如像素）**。例如，我们可以创造一个表达式，用一个百分比减掉一个像素值。

```
.foo {
    width: calc(100% - 50px);
}
```

> 什么场景可以使用 `calc()`

**Example 1 - 居中元素**

使用 `calc()` 给我们提供另一个垂直居中元素的解决方案。如果我们知道元素的尺寸，一个典型的解决方案是使用负外边距移动自身距离高与宽的一半，如下所示：

```
// Assuming .foo is 300px height and 300px width
.foo {
    position: absolute
    top: 50%;
    left: 50%;
    marging-top: -150px;
    margin-left: -150px;
}
```

使用 `calc()` 函数，我们仅仅通过 top 与 left 属性便能实现相同的效果：

```
.foo {
    position: absolute
    top: calc(50% - 150px);
    left: calc(50% - 150px);
}
```

**Example 2 - 创建根栅格尺寸**

使用 `rem`，`calc()` 函数能够用来创建一个基于视口的栅格。我们可以设置根元素的字体大小为视口宽度的一部分。

```
html {  
    font-size: calc(100vw / 30);
}
```

现在，`1rem` 为视口宽度的 1/30。在页面上的任何文本，将会根据你的视口自动缩放。更进一步，相同比例的视口总会显示相同的文本数量，不管视口的真实尺寸是多少。

**Example 3 - 清晰**

最后，`calc()`使计算更加清晰。如果你使一组项目为它们父元素容器宽度的 1/6，你可能值么写：

```
.foo {
    width: 16.666666667%;
}
```

然而，它能够更加清晰并具有可读性：

```
.foo {
    width: calc(100% / 6);
}
```

##### 伪类选择器和伪元素选择器的区别

**伪类**：

获取**不存在于DOM树中的信息**。比如标签的:link、visited等，这些信息不存在与DOM树结构中，只能通过CSS选择器来获取；

获取**不能被常规CSS选择器获取的信息**。比如伪类:target，它的作用是匹配文档(页面)的URI中某个标志符的目标元素，例如我们可以通过如下代码来实现页面内的区域跳转：

伪元素：

伪元素**创建了一个虚拟容器，这个容器不包含任何DOM元素，但是可以包含内容**。另外，开发者还**可以为伪元素定制样式**。

#### flex布局

- flex

任何一个容器都可以指定为 Flex 布局。

> ```
> .box{
>   display: flex;
> }
> ```

行内元素也可以使用 Flex 布局。

> ```
> .box{
>   display: inline-flex;
> }
> ```

注意，设为 Flex 布局以后，子元素的`float`、`clear`和`vertical-align`属性将失效。

- 基本的概念

**采用 Flex 布局的元素，称为 Flex 容器**（flex container），简称"容器"。它的**所有子元素自动成为容器成员，称为 Flex 项目**（flex item），简称"项目"。

![image-20210406110852397](HTML CSS相关.assets/image-20210406110852397.png)

##### 容器上使用的属性

- `flex-direction`：属性决定主轴的方向（即项目的排列方向）。

```
.box {
  flex-direction: row | row-reverse | column | column-reverse;
}
```

- `flex-wrap`：如果一条轴线排不下，如何换行

```
.box{
  flex-wrap: nowrap | wrap | wrap-reverse;
  /* wrap-reverse：换行，第一行在下方。 */
}
```

- `flex-flow`：`flex-direction`属性和`flex-wrap`属性的简写形式，，默认值为`row nowrap`

```
.box {
  flex-flow: <flex-direction> || <flex-wrap>;
}
```

- `justify-content`：定义了项目在主轴上的对齐方式

```
.box {
  justify-content: flex-start | flex-end | center | space-between | space-around;
}
```

下面假设主轴为从左到右：

- `flex-start`（默认值）：左对齐
- `flex-end`：右对齐
- `center`： 居中
- `space-between`：两端对齐，项目之间的间隔都相等。
- `space-around`：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

![image-20210406111455906](HTML CSS相关.assets/image-20210406111455906.png)

- `align-items`：定义项目在交叉轴上如何对齐

```
.box {
  align-items: flex-start | flex-end | center | baseline | stretch;
}
```

假设交叉轴从上到下：

> - `flex-start`：交叉轴的起点对齐。
> - `flex-end`：交叉轴的终点对齐。
> - `center`：交叉轴的中点对齐。
> - `baseline`: 项目的第一行文字的基线对齐。
> - `stretch`（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

![image-20210406111838718](HTML CSS相关.assets/image-20210406111838718.png)

- `align-content`：定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

```
.box {
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

该属性可能取6个值。

> - `flex-start`：与交叉轴的起点对齐。
> - `flex-end`：与交叉轴的终点对齐。
> - `center`：与交叉轴的中点对齐。
> - `space-between`：与交叉轴两端对齐，轴线之间的间隔平均分布。
> - `space-around`：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
> - `stretch`（默认值）：轴线占满整个交叉轴。

##### 项目上使用的属性

- `order`：定义项目的排列顺序。数值越小，排列越靠前，默认为0。
- `flex-grow`：定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大。

如果所有项目的`flex-grow`属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

- `flex-shrink`：定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

如果所有项目的`flex-shrink`属性都为1，当空间不足时，都将等比例缩小。如果一个项目的`flex-shrink`属性为0，其他项目都为1，则空间不足时，前者不缩小。

- `flex-basis`：定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。

```
.item {
  flex-basis: <length> | auto; /* default auto */
}
```

它可以设为跟`width`或`height`属性一样的值（比如350px），则项目将占据固定空间。

- `flex`：`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。

```
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

该属性有两个快捷值：`auto` (`1 1 auto`) 和 none (`0 0 auto`)。

建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。

- `align-self`：允许单个项目有**与其他项目不一样的对齐方式**，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。

```
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

### 特性

#### BFC

##### 常见的定位方案

- 普通流 (normal flow)

> 在普通流中，元素**按照其在 HTML 中的先后位置至上而下布局**，在这个过程中，**行内元素水平排列，直到当行被占满然后换行**，**块级元素则会被渲染为完整的一个新行**，除非另外指定，否则所有元素默认都是普通流定位，也可以说，普通流中元素的位置由该元素在 HTML 文档中的位置决定。

- 浮动 (float)

> 在浮动布局中，元素**首先按照普通流的位置出现**，然后**根据浮动的方向尽可能的向左边或右边偏移**（脱离文档流），其效果与印刷排版中的文本环绕相似。

- 绝对定位 (absolute positioning)

> 在绝对定位布局中，元素会**整体脱离普通流**，因此绝对定位元素**不会对其兄弟元素造成影响**，而元素具体的位置由绝对定位的坐标决定。

##### BFC概念

BFC 即 **Block Formatting Contexts** (块级格式化上下文)，它属于上述定位方案的**普通流**。

**具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。**

通俗一点来讲，可以把 BFC 理解为一个封闭的大箱子，箱子内部的元素无论如何翻江倒海，都不会影响到外部。

##### 触发BFC的条件

只要元素满足下面任一条件即可触发 BFC 特性：

- body **根元素**
- **浮动元素**：float 除 none 以外的值
- **绝对定位元素**：position (absolute、fixed)
- **display 为 inline-block、table-cells、flex**
- **overflow 除了 visible 以外的值** (hidden、auto、scroll)

##### BFC特性及应用

**1.同一个BFC下的元素外边距会发生折叠**

```
<head>
    <style>
    	div{
        width: 100px;
        height: 100px;
        background: lightblue;
        margin: 100px;
    }
    </style>
</head>
<body>
    <div></div>
    <div></div>
</body>
```

从效果上看，因为**两个 div 元素都处于同一个 BFC 容器下** (这里指 body 元素) 所以第一个 div 的下边距和第二个 div 的上边距发生了重叠，所以两个盒子之间距离只有 100px，而不是 200px。

首先这不是 CSS 的 bug，我们可以理解为一种规范，**如果想要避免外边距的重叠，可以将其放在不同的 BFC 容器中。**

```
<head>
    <style>
    	.container {
        	overflow: hidden;
        }
        p {
            width: 100px;
            height: 100px;
            background: lightblue;
            margin: 100px;
        }
    </style>
</head>
<body>
    <div class="container">
        <p></p>
    </div>
    <div class="container">
        <p></p>
    </div>
</body>
```

**2.BFC可以包含浮动的元素（清除浮动）**

我们都知道，浮动的元素会脱离普通文档流，来看下面一个例子

```
<div style="border: 1px solid #000;">
    <div style="width: 100px;height: 100px;background: #eee;float: left;"></div>
</div>
```

![img](https://pic4.zhimg.com/80/v2-371eb702274af831df909b2c55d6a14b_1440w.png)

由于容器内元素浮动，脱离了文档流，所以容器只剩下 2px 的边距高度。如果使触发容器的 BFC，那么容器将会包裹着浮动元素。

```
<div style="border: 1px solid #000;overflow: hidden">
    <div style="width: 100px;height: 100px;background: #eee;float: left;"></div>
</div>
```

效果如图：

![img](https://pic4.zhimg.com/80/v2-cc8365db5c9cc5ca003ce9afe88592e7_1440w.png)

**3.BFC可以阻止元素被浮动元素覆盖**

先来看一个文字环绕效果：

```
<div style="height: 100px;width: 100px;float: left;background: lightblue">我是一个左浮动的元素</div>
<div style="width: 200px; height: 200px;background: #eee">我是一个没有设置浮动, 
也没有触发 BFC 元素, width: 200px; height:200px; background: #eee;</div>
```

![img](https://pic4.zhimg.com/80/v2-dd3e636d73682140bf4a781bcd6f576b_1440w.png)

这时候其实第二个元素有部分被浮动元素所覆盖，(但是文本信息不会被浮动元素所覆盖) 如果想避免元素被覆盖，可触第二个元素的 BFC 特性，在第二个元素中加入 **overflow: hidden**，就会变成：

![img](https://pic3.zhimg.com/80/v2-5ebd48f09fac875f0bd25823c76ba7fa_1440w.png)

这个方法可以用来实现两列自适应布局，效果不错，这时候左边的宽度固定，右边的内容自适应宽度(去掉上面右边内容的宽度)。

### 大小单位区别

**px**：像素。 **em**：**参考物是父元素的font-size**，具有继承的特点。**如果自身定义了font-size按自身来计算**，整个页面内1em不是一个固定的值。 **rem**：**相对于根元素html的font-size计算**，不会像em那样，依赖于父元素的字体大小，而造成混乱。 **vw**：视窗宽度，1vw等于视窗宽度的1%。 **vh**：视窗高度，1vh等于视窗高度的1%。 **vm**：min(vw, vh)。 **%**：是**相对于父元素的大小设定的比率**，position:absolute;的元素是**相对于已经定位的父元素**，position：fixed；的元素是**相对可视窗口**。

> **浏览器默认字体是16px**, body设置font-size：62.5%, 那么1rem =62.5% * 16=10px 。

> **谷歌浏览器强制最小字体为12号**，即使设置成 10px 最终都会显示成 12px，当把html的font-size设置成10px,子节点rem的计算还是以12px为基准。

### 响应式布局和自适应布局

响应式布局等于**流动网格布局**，而自适应布局等于**使用固定分割点来进行布局**。 **自适应布局给了你更多设计的空间，因为你只用考虑几种不同的状态**。而在**响应式布局中你却得考虑上百种不同的状态**。虽然绝大部分状态差异较小，但仍然也算做差异。它使得把握设计最终效果变得更难，同样让响应式布局更加的难以测试和预测。但同时说难，这也算是响应式布局美的所在。在考虑到表层级别不确定因素的过程中，你也会因此更好的掌握一些基础知识。当然，要做到精确到像素级别的去预测设943*684像素视区里的样子是很难的，但是你至少可以很轻松的确定它是能够正常工作的，因为页面的基本特性和布局结构都是根据语义结构来部署的。

#### 响应式布局

**概念**

Responsive design，意在**实现不同屏幕分辨率的终端上浏览网页的不同展示方式**。通过响应式设计能使网站在手机和平板电脑上有更好的浏览阅读体验。

**设计步骤**

1. 设置Meta标签

**大多数移动浏览器将HTML页面放大为宽的视图（viewport）以符合屏幕分辨率**。你可以使用视图的meta标签来进行重置。下面的视图标签告诉浏览器，**使用设备的宽度作为视图宽度并禁止初始的缩放**。在`<head>`标签里加入这个meta标签。

```
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
```

[1]（user-scalable = no 属性能够解决 iPad 切换横屏之后触摸才能回到具体尺寸的问题。 ）

1. 通过媒介查询来设置样式Media Queries

Media Queries 是响应式设计的核心。 它**根据条件告诉浏览器如何为指定视图宽度渲染页面**。假如一个终端的分辨率小于 980px，那么可以这样写：

```
@media screen and (max-width: 980px) {
  #head { … }
  #content { … }
  #footer { … }
}
```

这里的样式就会覆盖上面已经定义好的样式。

1. 设置多种视图宽度

假如我们要设定兼容 iPad 和 iPhone 的视图，那么可以这样设置：

```
/** iPad **/
@media only screen and (min-width: 768px) and (max-width: 1024px) {}
/** iPhone **/
@media only screen and (min-width: 320px) and (max-width: 767px) {}
```

恩，差不多就这样的一个原理。

```
/* Smartphones (portrait and landscape) ----------- */
@media only screen
and (min-device-width : 320px)
and (max-device-width : 480px) {
/* Styles */
}
/* Smartphones (landscape) ----------- */
@media only screen
and (min-width : 321px) {
/* Styles */
}
/* Smartphones (portrait) ----------- */
@media only screen
and (max-width : 320px) {
/* Styles */
}
```

1. 注意点

**宽度需要使用百分比**

例如这样：

```
#head { width: 100% }
#content { width: 50%; }
```

**处理图片缩放的方法**

- 简单的解决方法可以使用百分比，但这样不友好，会放大或者缩小图片。那么可以尝试给图片指定的最大宽度为百分比。假如图片超过了，就缩小。假如图片小了，就原尺寸输出。

```
img { width: auto; max-width: 100%; }
```

- 用`::before`和`::after`伪元素 +content 属性来动态显示一些内容或者做其它很酷的事情，在 CSS3 中，任何元素都可以使用 content 属性了，这个方法就是结合 css3 的 attr 属性和 HTML 自定义属性的功能： HTML 结构：

```
<img src="image.jpg"
     data-src-600px="image-600px.jpg"
     data-src-800px="image-800px.jpg"
     alt="">
```

CSS 控制：

```
@media (min-device-width:600px) {
    img[data-src-600px] {
        content: attr(data-src-600px, url);
    }
}

@media (min-device-width:800px) {
    img[data-src-800px] {
        content: attr(data-src-800px, url);
    }
}
```

**其他属性**

例如 `pre` ，`iframe`，`video` 等，都需要和`img`一样控制好宽度。对于`table`，建议不要增加 padding 属性，低分辨率下使用内容居中：

```
table th, table td { padding: 0 0; text-align: center; }
```

### 应用

#### 水平与垂直居中实现方式

flex, transform, 绝对定位, 这几种方法**同时适用于水平居中和垂直居中**

**水平居中**：

- `text-align: center;`适用于行内元素
- `margin: 0 auto;`适用于块级元素

**原理**：

> 为什么设置 auto 可以让元素相对于父元素水平居中？这点很多候选人都答不上来。其实，这和 auto 这个属性的取值有关。auto 在任何情况下，只会取下面两种值中的一个：
>
> 1. 父元素剩余空间的宽度
> 2. 0
>
> 何时取1，何时取2？
>
> 这是由元素的布局方式决定的，当元素的布局方式为 static/relative 且宽高已知时，auto 取1中的值；当元素的布局方式为 postion/absolute/fixed 或者 float/inline 或者宽高未知时，auto 就取 2中的值。
>
> 注意，以上 auto 的取值均指水平方向，垂直方向上，auto 不会自动填充。

- `width: fit-content;` 若子元素包含 `float:left` 属性, 为了让子元素水平居中, 则可让父元素宽度设置为fit-content, 并且配合margin。

```
.parent {
 width:fit-content;
 margin:0 auto;
}
```

- flex

```
.parent {
    display: flex;
    justify-content: center;
}
```

- transform

```
.son {
    position: absolute;
    left: 50%;
    transform: translate(-50%, 0);
}
```

- 绝对定位方法（2种）

```
.son {
    position: absolute;
    left: 50%;
    width: 固定;
    margin-left: -0.5 * 宽度;
}

.son {
    position: absolute;
    width: 固定;
    /* 注意： margin auto的居中方式必须设置left和right都为0 */
    left: 0;
    right: 0;
    margin: 0 auto
}
```

**流体特性可实现水平和垂直居中**：

> 当一个绝对定位元素，其对立定位方向属性同时有具体定位数值的时候，流体特性就发生了。
>
> 流体特性的妙处，在于**元素可自动填充父级元素的可用尺寸**。
>
> 当流体特性发生时，我们可以给水平/垂直方向的对立定位（也就是 left、right、top、bottom）各设定一个值，然后将水平/垂直方向的 margin 均设为 auto，这样一来，auto 就会自动平分父元素的剩余空间了。

**垂直居中**：

- 单行文本：通过设置和父节点高度相同的line-height

- 行内块级元素：使用`display: inline-block;`，`vertical-align: middle;`加上为元素辅助实现

  ps:z只有在表格中可以垂直居中  不然就设置display: table;

```css
.parent::after, .son {
		//display: table; 在非表格时需要设置
    display:inline-block;
    vertical-align:middle;
}
.parent::after {
    content:'';
    height:100%;
}
```

- `vertical-align`。`vertical-align`只有**在父层为 td 或者 th 时, 才会生效**, **对于其他块级元素, 例如 div、p 等, 默认情况是不支持**的. 为了使用`vertical-align`, 我们需要设置父元素display:table, 子元素 display:table-cell;vertical-align:middle;
- flex

```
.parent {
    display: flex;
    align-items: center;
}
```

- transform

```
.son {
  position: absolute;
  top: 50%;
  transform: translate(0, -50%);
}
```

- 绝对定位方法（2种）

```
.son {
  position: absolute;
  height: 固定;
  top: 50%;
  margin-top: -0.5 * height;
}

.son {
  position: absolute;
  height: 固定;
  top: 0;
  bottom: 0;
  margin: auto 0;
}
```

**table 布局实现居中：**

3个步骤的操作：

1. 将容器元素的布局方式设置为 table-cell
2. 指定容器元素内部子元素的布局方式，vertical-align 指定垂直居中，text-align 指定水平居中
3. 将子元素的布局设为 inline-block，因为 table-cell 理论上只能处理具备行内特性的元素布局

```
#center {
    background-color: red;
    display: inline-block;
}

#container {
    width: 400px;
    height: 400px;
    background-color: black;
    display: table-cell;
    vertical-align: middle;
    text-align: center;
}
```

#### 纯CSS实现三角形

```
/* 
  采用的是相邻边框链接处的均分原理
  将元素的宽高设为0，只设置 border ,
  将任意三条边隐藏掉（颜色设为 transparent ）,剩下的就是一个三角形
 */
#demo{
  width: 0;
  height: 0;
  border-width: 20px;
  border-style: solid;
  border-color: transparent transparent red transparent;
}
```

#### 精灵图

- 利用CSS的“background-image”，“background-repeat”，“background-position”的组合进行背景定位。（True）
- CSS Sprites虽然增加了总的图片的字节，但是很好地减少网页的http请求，从而大大的提高页面的性能。（False）

**CSS Sprites能减少图片的字节，曾经比较过多次3张图片合并成1张图片的字节总是小于这3张图片的字节总和。**

#### **使用 base64 编码的优缺点**

base64编码是一种**图片处理格式**，通过**特定的算法将图片编码成一长串字符串**，在页面上显示时可**用该字符串来代替图片的url属性** **使用base64的优点：**

- 减少一个图片的 HTTP 请求

**使用base64的缺点：**

- 根据base64的编码原理，编码后的大小会**比源文件大**小大1/3，如果把大图片编码到html/css中，不仅会造成文件体积增加，**影响文件的加载速度**，还会增加浏览器对html或css文件解析渲染的时间。
- 使用base64**无法直接缓存**，要缓存只能缓存包含base64的文件，比如HTML 或CSS，这相比于直接缓存图片的效果要差很多。
- ie8以前的浏览器不支持

一般一些网站的小图标可以使用base64图片引入

#### 前端实现动画的方式

1. **javascript 直接实现动画**

> 其主要思想是通过setInterval或setTimeout方法的回调函数来持续调用改变某个元素的CSS样式以达到元素样式变化的效果。

Jquery的`animate()`方法就是这种方式实现的。

语法：(*selector*).animate(*{styles},speed,easing,callback*)

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <style type="text/css">
        #rect {
            width: 200px;
            height: 200px;
            background: #ccc;
        }
    </style>
</head>
<body>
    <div id="rect"></div>
    <script>
        let elem = document.getElementById('rect');
        let left = 0;
        let timer = setInterval(function(){
            if(left<window.innerWidth-200){
                elem.style.marginLeft = left+'px';
                left ++;
            }else {
                clearInterval(timer);
            }
        },16);
    </script>
</body>
</html>
```

**Tip：为什么是16ms**

上面例子中，我们设置的setInterval时间间隔是16ms。**一般认为人眼能辨识的流畅动画为每秒60帧**，这里16ms比(1000ms/60)帧略小一些，但是一般可仍为该动画是流畅的。 在很多移动端动画性能优化时，一般**使用16ms来进行节流处理连续触发的浏览器事件**。例如对touchmove、scroll事件进行节流等。通过这种方式减少持续事件的触发频率，可以大大提升动画的流畅性。

1. **CSS3 transition**

transition是**过渡动画**。但是transition并**不能实现独立的动画**，只能在某个标签元素样式或状态改变时进行平滑的动画效果过渡，而不是马上改变。

```
transition: property duration timing-function delay;
```

**注意**

在移动端开发中，**直接使用transition动画会让页面变慢甚至卡顿**。所以我们通常添加`transform:translate3D(0,0,0)`或`transform:translateZ(0)`来**开启移动端动画的GPU加速**，让动画过程更加流畅。

1. **CSS3 animation**

animation 算是真正意义上的CSS3动画。通过对**关键帧**和**循环次数**的控制，页面标签元素**会根据设定好的样式改变进行平滑过渡**。而且**关键帧状态的控制是通过百分比来控制的**。

animation 属性用来指定一组或多组动画，每组之间用逗号相隔。

```
animation: name duration timing-function delay iteration-count direction;
/* @keyframes duration | timing-function | delay | name */
animation: 3s linear 1s slidein;
 
 
@keyframes slidein {
  from { transform: scaleX(0); }
  to   { transform: scaleX(1); }
}
```

**比较**

CSS3最大的优势是摆脱了js的控制，并且**能利用硬件加速以及实现复杂动画效果**。

1. **requestAnimationFrame**

requestAnimationFrame是另一种Web API，原理与setTimeout和setInterval类似，都是**通过javascript持续循环的方法调用来触发动画动作**。但是requestAnimationFrame是浏览器**针对动画专门优化形成的APi**，在性能上比另两者要好。

```
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        * {
            margin:0;
            padding:0;
        }
        div {
            width: 200px;
            height: 200px;
            background-color: #ccc;
        }
    </style>
</head>
<body>
    <div id="rect"></div>
    <script type="text/javascript">
    window.requestAnimationFrame = window.requestAnimationFrame || window.mozRequestAnimationFrame || window.webkitRequestAnimationFrame ||
    window.msRequestAnimationFrame;

    let elem = document.getElementById("rect");
    let left = 0;
    //自动执行持续性回调
    requestAnimationFrame(step);
    //持续该改变元素位置
    function step() {
        if(left<window.innerWidth-200){
            left+=1;
            elem.style.marginLeft = left+"px";
            requestAnimationFrame(step);
        }
    }
    </script>
</body>
</html>
```

我们注意到，requestAnimationFrame只是**将回调的方法传入到自身的参数中执行**，而不是通过setInterval调用

**为什么`setTimeout`和`setInterval`不好？**

由于js是单线程执行，所以为了防止某个任务执行时间过长而导致进程阻塞，js中存在异步队列的概念，对于如`setTimeout`和`ajax`请求都是把进程放到了异步队列中，当主进程为空时才执行异步队列中的任务。所以 `setTimeout`和`setInterval`**无法保证回调函数的执行时机**，可能会在一帧之内执行多次导致多次页面渲染，浪费CPU资源甚至产生卡顿，或者是在一帧即将结束时执行导致重新渲染，出现掉帧的情况。

**`requestAnimationFrame`是怎么优化的？**

- CPU节能，当页面被隐藏或最小化时，暂停渲染。
- 函数节流，其**循环间隔是由屏幕刷新频率决定的**，**保证回调函数在屏幕的每一次刷新间隔中只执行一次**。

1. SVG
2. Canvas

**总结**：

复杂的动画是通过一个个简单的动画组合实现的。基于兼容性问题，通常在项目中，一般在

- 桌面端浏览器推荐使用javascript直接实现动画或SVG方式；
- 移动端可以考虑使用CSS3 transition、CSS3 animation、Canvas或requestAnimationFrame方式。

**存在的问题:**

javascript 实现动画通常会导致页面频繁性重排重绘，消耗性能，一般应该在桌面端浏览器。在移动端上使用会有明显的卡顿。

#### 1px 问题的解决方案

下面是实际业务中操作性比较强的三种思路：

**思路一：直接写 0.5px**

我没开玩笑，比如说你之前 1px 的样式这样写：

```
border:1px solid #333
```

你可以先在 JS 中拿到 window.devicePixelRatio 的值，然后**把这个值通过 JSX 或者模板语法给到 CSS 的 data 里**，达到这样的效果（这里我用 JSX 语法做个示范）：

```
  <div id="container" data-device={{window.devicePixelRatio}}></div>
```

然后你就可以在 CSS 中用属性选择器来命中 devicePixelRatio 为某一值的情况，比如说这里我尝试命中 devicePixelRatio 为2的情况：

```
#container[data-device="2"] {
  border:0.5px solid #333
}
```

直接把 1px 改成 1/devicePixelRatio 后的值，这是目前为止最简单的一种方法。这种方法的缺陷在于**兼容性不行**，IOS 系统需要8及以上的版本，安卓系统则直接不兼容。

**思路二：伪元素先放大后缩小**

这个方法的可行性会更高，兼容性也更好。唯一的缺点是代码会变多。

我们的思路是**先放大、后缩小：在目标元素的后面追加一个 ::after 伪元素，让这个元素布局为 absolute 之后、整个伸展开铺在目标元素上，然后把它的宽和高都设置为目标元素的两倍，border值设为 1px。接着借助 CSS 动画特效中的放缩能力，把整个伪元素缩小为原来的 50%。此时，伪元素的宽高刚好可以和原有的目标元素对齐，而 border 也缩小为了 1px 的二分之一，间接地实现了 0.5px 的效果。**

代码演示如下：

```
#container[data-device="2"] {
    position: relative;
}

#container[data-device="2"]::after{
      position:absolute;
      top: 0;
      left: 0;
      width: 200%;
      height: 200%;
      content:"";
      transform: scale(0.5);
      transform-origin: left top;
      box-sizing: border-box;
      border: 1px solid #333;
    }
}
```

**思路三：viewport 缩放来解决**

对 meta 标签里几个关键属性下手：

```
<meta name="viewport" content="initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no">
```

这里针对像素比为2的页面，把整个页面缩放为了原来的1/2大小。如此一来，本来占用2个物理像素的 1px 样式，现在占用的就是标准的一个物理像素。根据像素比的不同，这个缩放比例可以被计算为不同的值，我们用 js 代码实现如下：

```
const scale = 1 / window.devicePixelRatio;

// 这里 metaEl 指的是 meta 标签对应的 Dom
metaEl.setAttribute('content', `width=device-width,user-scalable=no,initial-scale=${scale},maximum-scale=${scale},minimum-scale=${scale}`);
```

1px 的 bug 就这样轻松搞定了，但这样做的副作用也很大，整个页面被缩放了。这时候我们的 1px 已经被处理成物理像素大小了，这样的大小在手机上显示边框很合适。但仔细想想，一些原本不需要被缩小的内容，比如文字、图片等，也被无差别缩小掉了。

### css工程化

#### css工程化解决的问题

1. 宏观设计：CSS 代码**如何组织**、**如何拆分**、**模块结构**怎样设计？
2. 编码优化：怎样**写出更好的 CSS**？
3. 构建：如何处理我的 CSS，才能让它的**打包结果最优**？
4. 可维护性：代码写完了，如何**最小化它后续的变更成本**？如何确保任何一个同事都能轻松接手？

#### css的常见工程化方式

- 预处理器：Less、 Sass 等；它们普遍会具备这样的特性：

  - **嵌套代码**的能力，通过嵌套来反映不同 css 属性之间的层级关系 ；
  - 支持**定义 css 变量**；
  - 提供**计算函数**；
  - 允许对代码片段**进行 extend 和 mixin**；
  - 支持**循环语句**的使用；
  - 支持将 CSS 文件**模块化**，实现复用。

- 重要的工程化插件： PostCss；

  - 它和预处理器的不同就在于，预处理器处理的是 类CSS，而 PostCss **处理的就是 CSS 本身**。

    理解 PostCss，我们可以**类比 JS 世界里的 Babel**。大家知道，Babel 可以将高版本的 JS 代码转换为低版本的 JS 代码。PostCss 做的是类似的事情：它可以**编译尚未被浏览器广泛支持的先进的 CSS 语法，还可以自动为一些需要额外兼容的语法增加前缀**。更强的是，由于 PostCss 有着强大的插件机制，支持各种各样的扩展，极大地强化了 CSS 的能力。

    PostCss 在业务中的使用场景非常多，这里我列举几个最高频的：

    - **提高 CSS 代码的可读性**：PostCss 其实可以做类似预处理器能做的工作；
    - 当我们的 CSS 代码需要**适配低版本浏览器**时，PostCss 的 [Autoprefixer](https://github.com/postcss/autoprefixer) 插件可以帮助我们自动增加浏览器前缀；
    - 允许我们**编写面向未来的 CSS**：PostCss 能够帮助我们编译 CSS next 代码；

- Webpack loader 等 。

  - Webpack 中操作 CSS 需要使用的两个关键的 loader：css-loader 和 style-loader
  - - css-loader：导入 CSS 模块，对 CSS 代码进行编译处理；
    - style-loader：创建style标签，把 CSS 内容写入标签。

## 浏览器相关原理

### 浏览器渲染原理

#### 浏览器的组成

![image-20210507093314883](HTML CSS相关.assets/image-20210507093314883.png)

- 用户界面只是一层皮，它没有独自处理命令的能力，但它可以把命令转交给“幕后”的浏览器引擎。
- 浏览器引擎虽然也无法独自完成这些命令，但能“读懂”你的命令，进而把这个命令用渲染引擎能理解的方式传递给它
- 渲染引擎不仅自己有一套任务逻辑，它还能够调度一系列执行层面的工作模块。网络资源，经过了渲染引擎渲染成可交互的页面呈现在你面前了。

#### 渲染引擎工作流程

![image-20210507094015958](HTML CSS相关.assets/image-20210507094015958.png)

![image-20210719155656589](HTML CSS相关.assets/image-20210719155656589.png)

**渲染引擎处理**可以分为以上五个具体流程：

**1. HTML解析**

在这一步浏览器**对HTML文档进行解析**，并在解析 HTML 的过程中**发出了页面渲染所需的各种外部资源请求**。(浏览器需要加载解析的不仅仅是**HTML**，还包括**CSS**、**JS**，以及还要加载图片、视频等其他**媒体资源**。)

**2. CSS解析** 浏览器将**识别并加载所有的 CSS 样式信息**。

**3. 样式与结构合并** 将样式信息和文档结构合并，最终**生成页面 render 树**（:after :before 这样的伪元素会在这个环节被构建到 DOM 树中）。

**4. 布局阶段** 页面中所有元素的**相对位置信息，大小等信息**均在这一步得到计算。

**5. 页面绘制** 在这一步中浏览器会根据我们前面处理出来的结果，**把每一个页面图层转换为像素，并对所有的媒体文件进行解码**。

上面五个**具体流程对应的产物**：

- DOM树

![image-20210507094532400](HTML CSS相关.assets/image-20210507094532400.png)

- CSSOM树

虽然我们编写 CSS 代码时不像 HTML 代码一样表现出明显的嵌套关系，**但是** **CSSOM 也是具有树结构的**。这是因为在样式计算的过程中，浏览器**总是从适用于该节点的最通用规则开始**（例如 div 节点是 body 元素的子项，则应用所有 body 样式），**一层一层递归细化出具体的样式**。这个由通用到具体的细化关系，我们也可以用树结构来描述：

![image-20210507094652217](HTML CSS相关.assets/image-20210507094652217.png)

- 渲染树（DOM树与CSSOM树“合体”）

值得注意的是，渲染树的特点是它**只包含渲染网页所需的节点。**所以在构建渲染树的过程中，除了“结合”之外，浏览器还做了一些关键的小动作，这些小动作可能作为考点出现：

step1： 从 DOM 树的根节点开始遍历，筛选出所有**可见**的节点（不包括head，meta，display=none等不可见的节点）；

step2：仅针对可见节点，为其**匹配 CSSOM 中的 CSS 规则**；

step3：发射可见节点（连同其内容和计算的样式）。

![image-20210507095037437](HTML CSS相关.assets/image-20210507095037437.png)

- 盒模型（布局渲染树）

浏览器对渲染树进行遍历，将元素间**嵌套关系**以**盒子模型的形式**写入文档流：

![image-20210507095210448](HTML CSS相关.assets/image-20210507095210448.png)

盒模型在布局过程中会**计算出元素确切的大小和定位**。计算完毕后，**相应的信息被写回渲染树上**，就形成了“布局渲染树”。同时，每一个元素盒子也都携带着自身的样式信息，作为后续绘制的依据。

- 目标界面

浏览器会将各层的信息发送给GPU，GPU会将各层合成（composite），实现了将渲染树上的每一个节点转换成我们肉眼可见的像素，将页面呈现在我们面前，这个过程就是“绘制”。

> 浏览器的解析过程并非是串连进行的，比如在**解析CSS的同时，可以继续加载解析HTML**，但在**解析执行JS脚本时，会停止解析后续HTML**，会出现阻塞问题。

#### DOM文档的加载步骤

- 解析HTML结构
- 加载外部脚本和样式表文件
- 解析并执行脚本
- 构建HTML DOM 模型 // DOMContentLoaded
- 加载外部资源文件（image等）
- 页面渲染完成

#### 回流（reflow）和重绘（repaint）

##### 什么是回流和重绘

**回流**：元素的（几何尺寸）**大小**或者**位置**发生了变化，触发了**重新布局**（其他元素的几何属性和位置也会因此受到影响），导致**渲染树重新计算布局和渲染**。页面第一次加载的时候，至少发生一次回流。

**回流的触发情况**：

1. **添加或删除**可见的DOM元素（改变DOM树的结构）；
2. 元素的**位置**、**尺寸**发生变化（改变元素的几何尺寸）；
3. **内容**发生变化（比如文本变化或图片被另一个不同尺寸的图片所替代）；
4. 获取一些**特定属性的值**（offsetTop、offsetLeft、 offsetWidth、offsetHeight、scrollTop、scrollLeft、scrollWidth、scrollHeight、clientTop、clientLeft、clientWidth、clientHeight 等属性）这些属性有一个共性，就是需要通过**即时计算**得到。因此浏览器为了获取这些值，也会进行回流。
5. 页面**一开始渲染**的时候（这个无法避免）；
6. **浏览器的窗口尺寸**变化， 因为回流是根据视口的大小来计算元素的位置和大小的；

**回流的流程如下图所示**：

![image-20210507100031498](HTML CSS相关.assets/image-20210507100031498.png)

**重绘**：元素的**外观，风格**改变，而不会影响布局（样式变化而并未影响其几何属性）。 如：outline, visibility, color, background-color......等

**重绘的流程如下图所示**：

![image-20210507101019079](HTML CSS相关.assets/image-20210507101019079.png)

**两者区别**：Reflow 的成本比 Repaint 高得多的多。DOM Tree 里的每个结点都会有 reflow 方法，一个结点的 reflow 很有**可能导致子结点，甚至父点以及同级结点的 reflow**。**回流一定会触发重绘，而重绘不一定会回流**。

##### 减少重绘和回流的方式

1. **CSS方法**
   - 用 `visibility` 替换 `display: none` ，因为前者只会引起重绘，后者会引发回流
   - **避免使用table布局**，可能很小的一个小改动会造成整个 table 的重新布局。
   - **避免设置多层内联样式**，CSS 选择符从右往左匹配查找，避免节点层级过多。
   - 将**动画效果应用到position属性为absolute或fixed的元素**上，避免影响其他元素的布局，这样只是一个重绘，而不是回流，同时，控制动画速度可以选择 `requestAnimationFrame`
   - **避免使用CSS表达式**，可能会引发回流。
   - 将**频繁重绘或者回流的节点设置为图层**，图层能够阻止该节点的渲染行为影响别的节点，例如`will-change`、`video`、`iframe`等标签，浏览器会自动将该节点变为图层。
   - **CSS3 硬件加速**（GPU加速），使用css3硬件加速，可以让transform、opacity、filters这些动画不会引起回流重绘 。但是对于动画的其它属性，比如background-color这些，还是会引起回流重绘的，不过它还是可以提升这些动画的性能。
2. **JavaScript方法**
   - **避免频繁操作样式**，最好一次性重写style属性，或者将样式列表定义为class并一次性更改class属性。
   - **避免频繁操作DOM**，创建一个`documentFragment`，在它上面应用所有DOM操作，最后再把它添加到文档中。
   - **避免频繁读取会引发回流/重绘的属性**，如果确实需要多次使用，就用一个变量缓存起来。

### 浏览器的多线程机制

#### 浏览器包含的线程

1. **GUI线程**

- **功能**：负责**渲染浏览器界面，解析HTML、CSS，构建DOM树、CSSOM树和RenderObject树，进行布局和绘制**等
- **执行时机**：当界面需要重绘（Repaint）或由于某种操作引发回流(reflow)时，该线程就会执行
- **注意**：**GUI渲染线程与JS引擎线程是互斥的**，当JS引擎执行时GUI线程会被挂起（相当于被冻结了），GUI更新会被保存在一个**队列**中等到JS引擎空闲时立即被执行。

1. **JS引擎线程**

- 称为**JS内核，功能**：负责负责解析Javascript脚本，运行代码。（例如V8引擎）
- JS引擎一直**等待着任务队列中任务的到来**，然后加以处理，一个Tab页（renderer进程）中无论什么时候都只有一个JS线程在运行JS程序
- **注意**：**GUI渲染线程与JS引擎线程是互斥的**，所以如果JS执行的时间过长，这样就会造成页面的渲染不连贯，导致页面渲染加载阻塞。

1. **事件触发线程**

- **功能**：用来**控制事件循环**，归属于浏览器而不是JS引擎
- 当JS引擎执行**异步代码块**如setTimeOut时（也可来自浏览器内核的其他线程,如鼠标点击、AJAX异步请求等），会**将对应任务添加到事件线程中**
- 当对应的事件**符合触发条件被触发时**，该线程会**把事件添加到待处理队列的队尾，等待JS引擎的处理**
- 注意，由于JS的单线程关系，所以这些待处理队列中的事件都得排队等待JS引擎处理（当JS引擎空闲时才会去执行）

1. **定时触发器线程**

- 浏览器定时计数器并不是由JavaScript引擎计数的,（因为JavaScript引擎是单线程的, 如果处于阻塞线程状态就会影响记计时的准确）
- 通过单独线程来计时并触发定时（计时完毕后，添加到事件队列中，等待JS引擎空闲后执行）

1. **异步http请求线程**

- 在XMLHttpRequest在连接后是通过浏览器新开一个线程请求
- 将检测到**状态变更时**，**如果设置有回调函数，异步线程就产生状态变更事件，将这个回调再放入事件队列中**。再由JavaScript引擎执行。（有限状态机？）

**为啥GUI渲染线程与JS引擎线程互斥**

- JS引擎可以修改DOM树，那么如果JS引擎在执行修改了DOM结构的同时，GUI线程也在渲染页面，那么这样就会导致渲染线程获取的DOM的元素信息可能与JS引擎操作DOM后的结果不一致。
- 为了防止这种现象，GUI线程与JS线程需要设计为互斥关系，**当JS引擎执行的时候，GUI线程需要被冻结，但是GUI的渲染会被保存在一个队列当中，等待JS引擎空闲的时候执行渲染**。

**JS引擎线程与事件触发线程、定时触发器线程、异步HTTP请求线程**

- 上述四者均使用回调函数的形式，**当满足了特定的条件，这些回调函数会被执行**。这些回调函数被浏览器内核理解成事件，在浏览器内核中拥有一个事件队列，这三个线程当满足了内部特定的条件，**会将这些回调函数添加到事件队列中，等待JS引擎空闲执行**。例如异步HTTP请求线程，线程如果检测到请求的状态变更，如果设置有回调函数，回调函数会被添加事件队列中，等待JS引擎空闲了执行。
- 但是，JS引擎对事件队列（宏任务）与JS引擎内的任务（微任务）执行存在着先后循序，当每执行完一个事件队列的时间，JS引擎会检测内部是否有未执行的任务，如果有，将会优先执行（微任务）。

#### 浏览器多进程与JS线程

**进程和线程**

- 一个进程有一个或多个线程，线程之间共同完成进程分配下来的任务
- 进程是**cpu资源分配的最小单位**（是能拥有资源和独立运行的最小单位），线程是**cpu调度的最小单位**（线程是建立在进程的基础上的一次程序运行单位）

**浏览器内的进程**

- 浏览器是**多进程**的，之所以浏览器能够运行，是因为系统给浏览器分配了资源，如cpu、内存，简单的说就是，浏览器每打开一个标签页，就相当于创建了一个独立的浏览器进程。

- 浏览器的标签页进程之外，浏览器还有一些其他进程来辅助支撑标签页的进程，如下： ① **Browser进程**：浏览器的**主进程**（负责协调、主控），只有一个。作用有

  - 负责**浏览器界面显示**，与用户交互。如前进，后退等
  - 负责**各个页面的管理**，创建和销毁其他进程
  - **网络资源的管理，下载**等

  ② **第三方插件进程**：每种类型的插件对应一个进程，仅当使用该插件时才创建 ③ **GPU进程**：最多一个，用于3D绘制等 ④ **浏览器渲染进程（浏览器内核）**，Renderer进程，内部是多线程的，也就是我们每个标签页所拥有的进程，互不影响，负责页面渲染，脚本执行，事件处理等

#### 浏览器为什么是单线程的

- JavaScript作为一门客户端的脚本语言，**主要的任务是处理用户的交互**，而用户的交互无非就是响应DOM的增删改，**使用事件队列的形式，一次事件循环只处理一个事件响应，使得脚本执行相对连续**。
- 如果JS引擎被设计为多线程的，那么**DOM之间必然会存在资源竞争**，那么语言的实现会变得非常臃肿，在客户端跑起来，资源的消耗和性能将会是不太乐观的，故设计为单线程的形式，**并附加一些其他的线程来实现异步的形式**，这样运行成本相对于使用JS多线程来说降低了很多。

#### WebWorker

> Web Workers 使得一个Web应用程序可以在与**主执行线程分离的后台线程中运行一个脚本操作**。这样做的好处是可以在一个单独的线程中执行费时的处理任务，从而允许主（通常是UI）线程运行而不被阻塞/放慢。

- WebWorker可以向浏览器申请一个子线程，该子线程服务于主线程，完全受主线程控制。
- JS引擎线程与worker线程间通过特定的方式通信（postMessage API，需要通过序列化对象来与线程交互特定的数据）

所以，如果需要进行一些高耗时的计算时，可以单独开启一个WebWorker线程，这样不管这个WebWorker子线程怎么密集计算、怎么阻塞，都不会影响JS引擎主线程，只需要等计算结束，将结果通过postMessage传输给主线程就可以了。

### 浏览器兼容性问题

#### **浏览器兼容性有哪些**

- ① 浏览器默认的 margin 和 padding 不同 解决：加一个全局 *{ margin: 0; padding: 0; }来统一
- ② 谷歌中文界面下默认会将小于12px 的文本强制按照12px显示 解决：使用-webkit-transform:scale(.75);收缩的是整个span盒子大小，这时候，必须将span准换成块元素
- ③ 超链接访问过后hover样式就不会出现了，被点击访问过的超链接样式不再具有hover 和active 了 解决：改变css 属性的排列顺序L-V-H-A

