[TOC]

### 1. DOCTYPE的作用

1. DOCTYPE 位于文档的第一行，用来告诉浏览器用何种文档模式渲染文档。在缺失的情况下，浏览器会使用“兼容模式（怪异模式）”；`<!DOCTYPE html>`声明表明该页面遵守了HTML5规范，会选择“标准模式”；此外还有介于二者之间的“几乎标准模式”。
2. 标准模式以浏览器支持的最高标准进行渲染；怪异模式则是向后兼容，模拟旧版浏览器的行为以防止站点无法工作。
3. `DTD(Document Type Definition, 文档类型定义)`，一组机器可读规则，定义特定版本的XML或HTML允许的元素、属性和层级关系。HTML5之后不需要指明`DTD`，因为HTML5不再基于SGML。

### 2. SGML、HTML、XML和XHTML的区别

* SGML，标准通用标记语言，定义电子文档结构和内容的国际标准语言，是所有电子标记文档的起源。
* HTML，超文本标记语言，主要是规定怎么显示网页。
* XML，可拓展标记语言，和HTML的区别在于，它的元素可自创，且无数量限制。
* XHTML，和HTML相比要更为严格，比如标签小写，标签闭合。

### 3.行内元素

* 一个行内元素只占用它对应标签边框所含的空间。
* a、b、span、label、input、button、img（同时还是置换元素，可设置margin、padding）等。

### 4.块级元素

* 占满父级容器的整个宽度。
* div、p、ul、ol、li、dl、dt、dd、h1~h6、table、form等

### 5.行内、块级元素的区别

1. 格式：默认情况下，行内元素不换行，后者会；
2. 内容：默认情况下，行内元素只能包含文字和其他行内元素；块级元素没限制。
3. 属性：主要是盒模型属性，行内设置width、height、padding和margin无效。

### 6.常见的浏览器内核

1. Trident内核：IE内核

2. Chrome内核：统称为Chrome内核或Chromium内核，以前是Webkit，现在是Blink内核

3. Firefox内核：Gecko内核

>  国内浏览器一般是IE + Webkit双内核。

### 7.浏览器的渲染原理

1. 大体流程

    1. 浏览器解析三个东西：
        	* HTML/SVG/XHTML，webkit会有三个c++的类对应处理，解析它们产生DOM Tree。
         * CSS：解析产生CSS Rule Tree。
         * JavaScript：主要是通过 DOM API 和 CSSOM API 来操作DOM Tree 和 CSS Rule Tree。
    2. 解析后，浏览器引擎会通过 DOM Tree 和 CSS Rule Tree 构建 Render Tree。
        * Render Tree 的节点称为 渲染对象，因不可见的DOM元素不添加到渲染树种，渲染树不与DOM Tree 一一对应。
        * 浏览器构建完 Render Tree 之后进入布局阶段，计算每一个节点在页面上的位置和大小，也可称之为回流reflow。
    3. 布局结算后进入绘制阶段，调用渲染对象的 `paint` 方法（底层是Native GUI的API）将其绘制显示。

2. 渲染过程遇到JS文件？

    > ​    js的加载、解析和执行会阻塞文档的解析，也就是说如果HTML解析器在解析html文档时遇到js代码，它会暂停解析工作，将控制权交给js引擎。等js引擎运行完毕，浏览器再从中断的地方恢复html解析器使之继续工作。
    >
    > ​	这也是为什么在想要较快渲染首屏时，会建议将 script 标签放置在body底部的原因；此外还可以给 script标签添加上 `defer`或`async`属性。

    

### 8.defer 和 async 的作用，以及区别

1. 脚本没有`defer` 、`async` 时，浏览器会立即加载并执行。加载和解析过程都会使得html解析会被阻塞。

2. defer 表示延迟执行引入的js脚本：

    * 加载时不会阻塞html解析过程，是并行的；
    * 执行时机在 html解析完，DOMContentLoaded 事件触发之前；
    * 多个脚本按顺序执行。

3. async 表示异步执行引入的js脚本：

    * 加载时不会阻塞html解析过程，是并行的；
    * 执行时机在 加载完脚本文件 之后，会阻塞html解析；
    * 由于加载完就执行，不同大小的文件加载时间不同，因而执行顺序无法保证。（适合无依赖脚本）

    ![示意图](https://segmentfault.com/img/bVWhRl?w=801&h=814)

### 9.文档预解析

* 当执行js脚本时，另一线程解析剩下的文档，并加载需要通过网络加载的外部资源，比如外部脚本、样式表、图片等。
* 预解析不影响DOM树，这个工作是由主解析线程完成的。
* webkit 和 firefox 做了这个优化。

### 10. CSS如何阻塞文档解析

* 理论上，既然样式表不影响DOM的构建，也就没有必要停下解析过程等待它们。但存在的问题是，如果js脚本在文档的解析过程中请求样式信息，那么脚本将出现错误。
* 因此，如果浏览器尚未完成CSSOM的构建，而此时又需要执行js脚本，那么浏览器将会阻塞文档的解析和js的执行，直到CSSOM的构建完成。

### 11.渲染页面常见不良现象

1. FOUC（Flash of  unstyled Content）：样式闪烁，跟浏览器的渲染机制相关。它是指CSS加载之前，html先呈现，展示出无样式的内容，然后样式突然呈现的情况。出现的原因有：
    	* css加载时间过长
     * css放在了文档底部
2. 白屏：出现的原因：
    * css放在了html尾部，浏览器迟迟未渲染
    * js放在了文档头部，脚本的执行阻塞了文档解析。

### 12. 如何优化关键渲染路径？

​	为了尽早完成首次渲染，需要最大限度减小一下三种可变因素：

    1. 关键资源的数量
    2. 关键路径的长度
    3. 关键字节的大小

​	

### 13.什么是重绘和回流？

	* 重绘：渲染树中的元素需要更新属性，而这些属性只影响自身的外观，不影响到整体的布局，比如`background-color`。这种操作称为重绘。
	* 回流：渲染树中的元素因尺寸、布局和隐藏等改变而需要重新页面构建和布局的操作，称为回流。回流一定过会引起重绘。

### 14.如何减少回流？

> 可以提高性能。

* 使用 `transform`代替`top`；
* 不要把节点的属性值放在循环中充当变量（可能会多次读写）
* 不要使用table布局，一个小改动可能会引起table的重新布局；
* 把DOM离线后修改，比如：documentFragment
* 不要一条一条地修改DOM的样式，比如可以使用className。

### 15.为何操作DOM慢？

> 一些dom的操作可能会引起重绘或者回流，比较消耗性能。

### 16.DOMContentLoaded 事件 和 Load 事件的区别？

* 初始的html文档加载、解析完构建好DOM Tree之后，DOMContentLoaded 被触发，无需等待css、img等的加载。
* 当所有资源加载完成后，Load事件触发。

### 17.HTML5的新特性，移除了哪些元素？

> HTML5 不是SGML的子集；主要增加图像、位置、存储和多任务等特性。

​	新增的有：

 * 绘画：canvas

 * 媒体：audio 和 video

 * 本地存储：localStorage长期存储，关闭浏览器后不会丢失；sessionStorage的数据在关闭相应浏览器对应tab后自动删除。

 * 语义化：header、footer、section、article、nav等。

 * 表单控件：主要是input的type属性，表单属性autocomplete等。

 * 新技术：websocket、webwork

 * 新的文档属性：document.visibilityState（visible、hidden、prerender和unload）；典型用法是防止页面在渲染是加载资源，或页面在背景中或者窗口最小化时禁止某些活动。

    

    移除的有：

* 纯表现的元素：big、center等

* 对可用性产生负面影响的：frame等

### 18.如何处理HTML5标签的兼容性问题：

	* `document.createElement`
	* 使用成熟的框架，比如`html5shim`。

### 19. HTML5语义化

​	知识点：

		* 正确地使用标签
		* 结构清晰，易于解析（搜索引擎）
		* 便于源码阅读

### 20.b和strong 的区别、i和em的区别？

> 语义。

### 21. 前端需要注意哪些SEO？

* 合理的title、description和keywords；
* 语义化的html，便于搜索引擎理解网页；
* 非装饰性图片加上alt
* 提高网页速度（搜索引擎排序的重要指标）

### 22.html5的离线存储怎么用？工作原理？

原理：它是基于一个新建的 `.appcache`文件的缓存机制，将文件上的缓存清单像cookie一样存储下来。在网络离线时，用于进行页面展示。

如何使用：

1. 在html页面头部加入属性`manifest="cache.manifest"`

2. cache.manifest中的属性：

    * CACHE：要缓存的文件（优先级高于NETWORK；）

    * NETWORK：只能通过网络下载

    * FALLBACK：资源替换列表。如下：当访问根目录下的任一资源失败时，访问`/offline.html`。

        ``` manifest
        ...
        FALLBACK:
        / /offline.html
        ```

3. 离线状态下，通过`window.applicationCahce`进行离线缓存的操作。

### 23.浏览器如何管理离线存储的资源？

浏览器的管理：区分在线和离线两个状态。

 	1. 在线状态下：
     * 浏览器发现html头部有manifest文件，请求它；
     * 如果是第一次访问app，下载并存储manifest清单中的文件；如果已有过访问并且有离线存储，使用缓存的文件用于展示页面，然后对比新旧manifest文件，如果文件没有发生改变，无操作；如果文件发生改变了，重现下载并离线存储。
	2. 离线状态下：直接使用离线存储的文件。

### 24.cookie、localStorage和sessionStorage的区别?

	* 大小：4k、5M
 * 过期时间：
    * localStorage：存储持久数据，浏览器关闭后数据不丢失除非主动删除数据。
    * sessionStorage：  数据在页面会话结束时会被清除。页面会话在浏览器打开期间一直保持，并且重新加载或恢复页面仍会保持原来的页面会话。在新标签或窗口打开一个页面时会在顶级浏览上下文中初始化一个新的会话。
    * cookie：设置的 cookie 过期时间之前一直有效，即使窗口或浏览器关闭。
 * 作用域：
    * sessionStorage  只在同源的同窗口（或标签页）中共享数据，也就是只在当前会话中共享。
    * localStorage    在所有同源窗口中都是共享的。
    * cookie          在所有同源窗口中都是共享的。

Ps：

	1. 每次发起同源请求时，cookie会被发送给服务器端
 	2. 以上三种都是少量数据时的存储方式；当需要存储大量数据时，可以使用 indexDB 这种本地的接近NoSql的数据库存储机制。

### 25.frame的缺陷

iframe 元素会创建包含另外一个文档的内联框架（即行内框架）。

主要缺点有：

1. iframe 会阻塞主页面的 onload 事件。window 的 onload 事件需要在所有 iframe 加载完毕后（包含里面的元素）才
        会触发。在 Safari 和 Chrome 里，通过 JavaScript 动态设置 iframe 的 src 可以避免这种阻塞情况。
2. 搜索引擎的检索程序无法解读这种页面，不利于网页的 SEO 。
3.  iframe 和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载。
4. 浏览器的后退按钮失效。
5. 小型的移动设备无法完全显示框架。

### 26.label的作用

为表单组件定义标记。

### 27. HTML5的form自动完成功能

`autocomplete` 属性规定输入字段是否应该启用自动完成功能。默认为启用，设置为 autocomplete=off 可以关闭该功能。

自动完成允许浏览器预测对字段的输入。当用户在字段开始键入时，浏览器基于之前键入过的值，显示出在字段中填写的选项。

autocomplete 属性适用于` <form>`，以及下面的 `<input>` 类型：text, search, url, telephone, email, password, 
datepickers, range 以及 color。

### 28. 实现浏览器内多个标签页的通信：

> 本质上是通过 **中介模式** 来实现的。

	* websocket
	* localStorage的storageEvent（同一域下的其他页面触发）
	* postMessage

### 29. 页面可见性的用途

比如，通过Page Visibility API可以判断页面是否可见，在用户不看页面时，可以用来节省资源、减缓电量消耗等。比如：停止对服务器的轮训；暂停音视频播放等。

### 30.实现一个圆形可点击区域

* 纯html：map、area
* 纯css：border-radius
* 纯js：判断坐标。

### 31. title 和 h1

​	title 属性没有明确意义只表示是个标题，h1 则表示层次明确的标题，对页面信息的抓取也有很大的影响。

### 32.img的title和alt

* title 通常当鼠标滑动到元素上的时候显示

* alt 是 <img> 的特有属性，是图片内容的等价描述，用于图片无法加载时显示、读屏器阅读图片。可提图片高可访问性，除了纯装
    饰图片外都必须设置有意义的值，搜索引擎会重点分析。

### 33. canvas 和 svg

* Canvas 是一种通过 JavaScript 来绘制 2D 图形的方法。Canvas 是 **逐像素** 来进行渲染的，因此当我们对 Canvas 进行缩放时，
    会出现锯齿或者失真的情况。

* SVG 是一种使用 XML 描述 2D 图形的语言。
    * SVG 基于 XML，这意味着 SVG DOM 中的每个元素都是可用的。我们可以为某个元素
        附加 JavaScript 事件监听函数。
    * 并且 SVG 保存的是图形的绘制方法，因此当 SVG 图形缩放时并不会失真。

### 34.网页验证码的作用

* 区分是人还是计算机。可以防止恶意破解密码、刷票、论坛灌水。
* 有效防止暴力破解某个特定用户的密码。

### 35.渐进增强和优雅降级

* 渐进增强：针对低版本浏览器进行页面构建，然后再逐步针对高级浏览器进行交互、效果的改进以提供更好的用户体验。
* 优雅降级：一开始就根据高级浏览器构建页面，然后对低版本浏览器进行兼容。

### 36.attribute 和 property

* attribute 是 dom元素作为html标签时的属性
* property 是dom元素作为js对象时的属性
* 如果是html的标准标签，二者是同步的，自动更新的。（对于自定义的则不是）。

### 37. 可用性、可访问性和可维护性

* 可用性（Usability）：产品是否容易上手，用户是否能完成任务，是从用户角度来看产品的质量。（可用性好意味着产品质量高，是企业的核心竞争力）
* 可访问性（Accessibility）：web内容对于残障用户的可阅读和可理解。
* 可维护性（Maintainability）：两个层面：系统出问题可快速定位并解决；代码容易理解，容易修改和增加功能。

### 38. css reset 和 normalize.css 的区别



### 39.disabled 和 readonly 的区别

* disabled 禁用input元素，其内容不会随着表单提交
* readonly 规定输入字段只读，会随着表单提交

> 但设置它们时，js都可以修改input的value。

### 40.主流浏览器的私有属性前缀

* mozilla 内核 （firefox,flock 等）    -moz
* webkit  内核 （safari,chrome 等）   -webkit
* opera   内核 （opera 浏览器）        -o
* trident 内核 （ie 浏览器）           -ms

### 41. 前端性能优化

* 页面的内容：
    * 文件合并、雪碧图、base64等减少http请求数，避免请求过多造成等待。
    * 设置缓存，将不经常变动的资源缓存。
    * 使用延迟加载，减少首屏加载时需要请求的资源数量。
* css和js
    * 文件压缩
    * css 避免使用@import
    * 样式表放在head，减少页面首次渲染时间
    * js 脚本尽量放在页面底部或者使用 `defer`| `async`属性，避免脚本加载阻塞页面的渲染。
* 服务器：
    * 使用 gzip 对传输资源进行压缩，减少传输文件的体积
    * 使用cdn，减少资源的响应时间
    * 尽量减小cookie的大小

### 42.chrome中的waterfall



### 43. 第三方登录

* 自己的网站重定向到第三方登录
* 第三方服务器认证，携带code重定向回自己的网站
* 用code申请token等，继而获取用户信息
* 使用第三方或自己的登录态。

### 44. html规范中为什么要求引用资源不加协议头 `http`、`https`

协议相对URL，protocol-relative UR：浏览器将以相同的协议请求页面中的资源，避免出现警告信息。


