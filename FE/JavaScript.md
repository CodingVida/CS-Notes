[TOC]



### 1. 基本数据类型

`undefined`、`null`、`number`、`string`、`boolean` 和 `symbol`。

> symbol 是 es6 中新增的类型，表示创建后 **独一无二** 且 **不可变** ，它的出现主要是为了解决属性名冲突问题。

> js共 七种 数据类型（加上 Object，即引用类型，栈只存储值在堆中的地址）。

### 2. 堆栈

> 堆栈的概念存在于数据结构和操作系统内存中。

* 数据结构中：
    * 栈的数据存取方式是先进后出。
    * 堆是一个优先队列，按优先级（比如大小）进行排序。完全二叉树是堆的一种实现方式。
* 操作系统内存分为 **栈区** 和 **堆区**：
    * 栈区内存由编译器自动分配释放，存放函数的参数值、局部变量的值等。操作方式类似数据结构中的栈。
    * 堆区内存一般由程序员分配释放，若程序员不释放，将在程序结束时由os释放。 

### 3.  内部属性[[Class]]

所有 `typeof` 的返回值为 `object` 的对象（如数组）都包含一个内部属性 `[[Class]]`。这个属性无法直接访问，一般通过对象原型上的 `toString` 方法来访问，如：

````javascript
Object.prototype.toString.call([1, 2, 3]);
// "[object Array]"
````

### 4. js的内置对象

* js的内置对象是指由js定义在全局作用域里的一些值属性、函数和用来实例化其他对象的构造函数对象等。
* 如 全局变量值 undefined、null、NaN
* 全局函数如 parseInt、
* 构造函数如 Date、RegExp、Array、Map、Set
* 单体内置对象如 Math

### 5. undefined 和 undeclared

* 已在作用域中声明但未赋值的是 `undefined`，相反的，还未在作用域中声明的为 `undeclared`。
* 对于 undeclared 的变量引用，浏览器会报 **引用错误**，如 `ReferenceError：b is undefined`。因为对于 `undeclared`的变量，`typeof`返回 `undefined`，因此可以使用 `typeof` 的安全机制避免，

### 6. undefined 和 null

* undefined 和 null 都是js的基本类型，它们的值有且只有它们自身。
* undefined 的含义是声明了但未赋值，null代表的含义是空对象。一般变量声明了但还没赋值的时候返回 undefined，null一般用于赋值给返回对象的变量的初始化。
* undefined 在js中不是一个保留字，这意味着我们可以使用它作为变量名，这是一个危险的行为，它会影响对undefined的操作。但我们可以使用一些方法获得安全的undefined，比如 `void 0` 。
* 对 null 使用 typeof 会返回 object，这是一个历史遗留问题（记住就行）；对undefined使用 typeof 返回 `undefined`。
* 对这两种类型进行比较的时候，双等号返回 `true`，三等号返回 `false`。

### 7. 如何获取安全的 undefined

* 因为 undefined 是一个标识符，所以可以被当作变量来使用和赋值，但是这样会影响 undefined 的正常判断。

* 表达式 void ___ 没有返回值，因此返回结果是 `undefined`。void **并不改变表达式的结果，只是让表达式不返回值。**

* **按惯例** 我们用 `void 0` 来获得 `undefined`

### 8. 原型与原型链

#### 8.1 原型

> 有三个属性需谨记：`prototype`、`constructor`、`__proto__`。

* 函数对象的prototype

    每个函数都有一个prototype属性，这是一个指向原型对象的指针。原型对象包含了可以被特定类型的所有实例所共享的属性和方法。

    ````javascript
    function Person (name) {
    	this.name = name;
    }
    Person.prototype.sayName = function () {
        alert(this.name);
    }
    ````

    ![](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu18-2.png)

* 原型对象的 constructor 属性

    当函数对象创建的时候，它的prototype指向一个原型对象。在默认情况下，该原型对象会新建constructor属性，该属性是一个指向prototype所在函数对象的指针。

    ![](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu18-3.png)

* 实例对象的 `__proto__` 属性

    使用构造函数创建实例之后，实例内部将包含一个指向构造函数的原型对象的指针 `__proto__`，叫做[[Prototype]]。所有对象都包含[[Prototype]]属性，指向它们的原型对象。

    > 需要注意的是，js中没有标准的方式访问[[Prototype]] 属性，只是Firefox、Chrome、Safari提供了`__proto__`属性访问。
    >
    > 此外，可以通过 `isPrototypeOf`（或者Object.getPrototypeOf） 方法用于检测对象的原型：`Persoon.prototype.isPrototypeOf(student)`。

    

    ````javascript
    const student = new Person();
    consle.log(student.__proto__ === Person.prototype); // true
    ````

    ![](https://cavszhouyou-1254093697.cos.ap-chongqing.myqcloud.com/peitu18-5.png)



#### 8.2 原型链

当我们访问一个对象的属性时，如果这个对象内部不存在这个属性，那么就会去它的原型查找，而这个原型又会有自己的原型。这种上下游的原型对象之间的连接就形成了 **原型链**。原型链的尽头是 null（因为`Object.prototype.__proto__`为null，所以一般情况下，访问到Object.prototype 就可以认为到尽头了）。

原型链还是继承的主要方法。（继承意味着 **复制**，所以原型链的做法称作 **委托** 要好一些。）

> 获取原型的方法：
>
> * `p.__proto__`
> * p.constructor.prototype | P.prototype
> * Object.getPrototypeOf(p)
>
> ps：`instanceof`：用于检测构造函数的prototype是否出现在某个实例的原型链中。

### 9.  继承

> 了解到的继承方式有六种：原型链、经典构造函数、组合继承、原型式继承、寄生式继承、寄生式组合继承。
>
> es6 的 class extends 本质上仍是原型继承。
>
> 以下记录它们的实现以及优缺点。

#### 9.1 原型链继承

```javascript
function Parent () {
    this.name = 'yoto';
}
Parent.prototype.getName = function () {
    console.log(this.name);
}

// extends
function Child () {}
Child.prototype = new Person();
var child = new Child();
console.log(child.getName()); // yoto

```

缺点：

1. 引用类型的实例会被所有实例共享：

    ```javascript
    function Parent () {
        this.names = ['yoto', 'panp'];
    }
    
    // extends
    function Child () {}
    Child.prototype = new Person();
    
    var child1 = new Child();
    child1.names.push('kevin');
    console.log(child1.names); // ['yoto', 'panp', 'kevin']
    
    var child2 = new Child();
    console.log(child2.names); //['yoto', 'panp', 'kevin']
    ```

2. 创建子类时，不能向父类传参。

    

#### 9.2 经典继承（借助构造函数）

> 在子类构造方法中调用父类构造方法来实现。

```javascript
function Parent (name) {
    this.name = name;
}
function Child (name) {
    Parent.call(this, name);
}
```

优点：

1. 避免引用类型的属性被所有实例共享。
2. 可以向父类传参。

缺点：

1. 和构造函数模式一样的问题，所有的方法都在构造函数（父类）中定义，因此就无法做到函数的复用。

2. 子类无法访问定义在父类原型中的方法（本质上是断开了原型链）。

    

#### 9.3 组合继承

> 原型链继承 和 经典继承 的组合。

```JavaScript
function Parent (name) {
	this.name = name;
    this.colors = ['red', 'blue'];
}
Parent.prototype.getName = () {
    console.log(this.name);
}

// extends
function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
}
Child.prototype = new Person();
Child.prototype.constructor = Child;

var child1 = new Child('kevin', 18);
child1.colors.push('green'); // colors = ['red', 'blue', 'green'];

var child2 = new Child('daisy', 19);
console.log(child2.colors) // ['red', 'blue'] 

```

优点：融合了 原型链继承 和 经典继承 的优点（传参、访问原型上的方法、避免实例共享引用类型值等），是 JavaScript 中 **最常用** 的继承模式。 

缺点：调用了两次父类的构造函数，子类的原型对象增添了不必要的父类实例对象中的所有属性。



#### 9.4 原型式继承

> 是ES5中 Object.create 的模拟实现，将传入的对象作为创建的新对象的原型。

```JavaScript
function createObj (o) {
    function F () {};
    F.prototype = o;
    return new F();
}
```

缺点跟原型链相同。



#### 9.5 寄生式继承

> 创建一个用于封装继承过程的函数，该函数在内部以某种方式增强对象，最后返回这个对象。

```JavaScript
function createAnother (o) {
    var clone = createObj(o);
    clone.sayName = () {
        console.log('yoto');
    }
    reutrn clone;
}
```

优点：考虑的点主要是对象，而不是构造函数的情况下，实现简单的继承。

缺点：在为对象添加函数的时候，没法做到函数的复用。



#### 9.6 寄生式组合继承

> 组合继承的最大问题是调用了两次父类构造函数，子类原型中增添了不必要的父类实例的所有属性。
>
> 寄生式组合继承正是解决这个问题的，它与组合继承最大的不同在于，子类原型不直接指向父类实例，而是通过一个中间对象访问到父类原型。

```javascript
function Parent (name) {
    this.name = name;
}
Parent.prototype.getName = function () {
    console.log(this.name);
}

function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
}

// key steps
function F () {}
F.prototype = Parent.prototype;
Child.prototype = new F();
Child.prototype.constructor = Child;
```

封装继承方法如下：

```javascript
function object (o) {
    function F() {}
    F.prototype = o;
    return new F();
}

function extend (Child, Parent) {
    var prototype = object(Parent.prototype);
    prototype.constructor = Child;
    Child.prototype = prototype;
}
```

这种方式的高效之处在于它只调用了一次父类构造函数，并且避免了在 子类原型上添加多余的属性；与此同时，原型链还能保持不变，还能够继续使用 `instanceof` 和 `isPrototypeOf` 。

#### 9.7 class 的 extends

> ES6的 `class` 可以看作是一个语法糖，背后仍是原型继承的思想，只是写法上看起来更清晰了。
>
> 在 `class` 中添加的语法，是添加在类的原型上的。



### 10. 不同进制表示法

* 十六进制：`0X`、`0x`，比如 `0xAA`
* 八进制：`0、0O、0o`，比如 `0o7`
* 二进制：`0B`、`0b`



### 11. 整数的安全范围

* 安全整数是指，在这个范围内的整数转储为二进制的时候不会出现进度丢失，能够被“安全”呈现的最大证书是 `2^53 - 1`.

* Number.MIN_SAFE_INTEGER, Number.MAX_SAFE_INTEGER。9007199254740991。

* 如果某次计算得到超过js数值范围的值，将会被转化为 Infinity，且无法参与下一次的运算。判断一个数值是否有限，可以使用 `isFinite` 函数进行判断。

    

### 12.  typeof  NaN

* NaN 意指“不是一个数字”（not a number），NaN 是一个“警戒值”（sentinel value，有特殊用途的常规值），用于指出
    数字类型中的错误情况，即“执行数学运算没有成功，这是失败后返回的结果”。
* `typeof NaN;  // "number"`

* NaN 是一个特殊值，它和自身不相等，是唯一一个 **非自反**（自反，reflexive，即 x === x 不成立）的值。而 

    `NaN !== NaN` 为 true。

> 补充：`isNaN` 与 `Number.isNaN`：判断是否为 NaN。
>
> 相比 NaN ，Number.isNaN 多了一步判断：是否为数字（即没有先进行类型转换）。
>
> 因此：
>
> 	* isNaN('A String')				// true
> 	* Number.isNaN('A String')  // false
>
> 当然也可以利用非自反性：`n !== n`。



### 13.  转换规则

> 即其他 六种数据类型 到某一种数据类型的转换规则。

#### 13.1 其他值到字符串

> ES5 定义了 toString 抽象操作，负责处理非字符串到字符串的强制类型转换

* `null` 和 `undefined` 类型：分别转换为 'null' 和 ‘undefined’。
* Boolean 类型：`true` -> `'true'`, `false` -> `'false'`
* Number类型：直接转换。但比较大和比较小的数字会转为 指数形式的字符串。
* Symbol类型：直接转换。但只能显式转换 "symbol.toString()"，隐式转换会产生错误。
* Object类型：调用 `toString` 方法返回 `[[Class]]` 的值（如"[object Object]" ），或自定义的值。

#### 13.2 其他值到数字值

* undefined：转换为 NaN
* null：转换为 0
* Boolean：ture -> 1, false -> 0
* String：调用 `Number` 函数进行转换，包含非数字值则转为 `NaN`，空字符串转为 0。（这个规则与字符串解析数字方法parseInt不同，parseInt从左向右解析，遇到非数字字符就停止）。
* Symbol：无法转换为数字，会产生错误。
* Object：
    * 会首先被转换为相应的基本类型，如果返回的是非数字的基本类型，再遵循以上规则继续转化。
    * 为了转化为相应的基本类型，抽象操作 toPrimitive 首先会检查是否有 valueOf 方法，如果有且返回值为基本类型，就使用该值进行转换；否则检查是否有 toString 方法，如果有返回 toString 的值进行类型转换。如果两个方法都不存在，则产生 TypeError 错误。

#### 13.3 其他值到布尔类型的值 

假值的布尔强制类型转换结果为 false。从逻辑上将，假值列表之外的都应该转换为 true。假值列表如下：

* undefined

* null

* false

* +0，-0 和 NaN

* 空字符串 ''

    

### 14. {}、[] 的 valueOf 和 toString

* {} 的 valueOf 为 `{}`，toString 为 `[object Object]`

* []  的 valueOf 为 []，toString 为 `''`

    

### 15.  生成随机数

[JS - 生成随机数的方法汇总（不同范围、类型的随机数）](https://www.hangge.com/blog/cache/detail_1872.html)



### 16 执行上下文栈

js引擎执行代码的过程是一个边解析边执行的过程：在执行一段可执行代码之前，会先进行代码解析，为它创建相应的 **执行上下文**。

可执行代码可以分为三种：

* 全局执行代码
* 函数执行代码
* eval执行代码

执行每一段可执行代码都会创建对应的一个执行上下文，为了管理它们，js引擎创建了执行上下文栈（Execution Context Stack）。执行代码时压入相应的上下文，执行完毕则弹出。显然，全局上下文一直处于栈底。

[JavaScript深入之执行上下文栈](https://github.com/mqyqingfeng/Blog/issues/4)



### 17. 执行上下文

**执行上下文** 定义了代码执行所需的所有信息，在不同的版本中，其组成部分的定义也有所不同。

* 在 ES3 中，包含三个部分：
    * scope：作用域。
    * variable object：变量对象，用于存储变量的对象。
    * this value：this值。
* 在 ES5 中，三个部分改为：
    * lexical environment：词法环境，**获取变量** 时使用。
    * variable environment：变量环境，**声明变量** 时使用。
    * this value：this值。
* 在 ES2018 中，this值被归入 lexical environment中，同时新增了一些内容：
    * lexical environment：词法环境，获取变量或this时使用。
    * variable environment：变量环境，声明变量时使用。
    * code evaluation state：用于恢复代码执行位置。
    * Function：表示正在执行的函数，执行的任务是函数时使用。
    *  ScriptOrModule：表示正在执行的代码，执行的任务是脚本或者模块时使用。
    * Realm：使用的基础库和内置对象实例。
    * Generator：表示当前生成器，仅生成器上下文有这个属性。

### 18. 作用域链

当查找变量时，会先从当前上下文中的变量对象中查找，如果未能找到，就向上从词法层面上的父级执行上下文的变量对象中去查找，直到全局上下文的变量对象，也就是全局对象。

这样由多个执行上下文的变量对象构成的连接就叫做作用域链。

### 19. this值

`this` 是执行上下文的一个属性，它指向最后一次调用当前方法的对象。在 实际开发中，this的指向可以通过四种调用模式来判断：

* 函数调用模式：当一个函数不是作为某个对象的方法，而是直接作为函数调用时，this 指向全局对象。
* 方法调用模式：当一个函数作为一个对象的方法调用时，this 指向这个对象。
* 构造器调用模式：当一个函数用new调用时，函数执行前会新创建一个对象，this 指向这个新创建的对象。
* `apply`、`call` 和 `bind` 调用模式：显式指定this的指向。

这四种模式的优先级：构造器模式最高，apply、call和bind次之，方法调用模式再次之，最后是函数调用模式。



### 20. 闭包

​	MDN对闭包的解释为：

	> 闭包是能够访问 自由变量 的函数。

​	什么是自由变量？

	> 自用变量是指，在函数中使用的，既不是函数参数，也不是函数的局部变量的变量。

​	由此可以看出闭包有两个部分：

	> 闭包 = 函数 + 函数能够访问的自由变量

​	从两个角度理解闭包：

* 从理论角度：所有的函数都是闭包。因为函数在创建的时候会将上层上下文的数据保存起来，哪怕是访问一个全局变量也是在访问一个自由变量。
* 从实践角度，以下函数才算是闭包：
    * 即使创建它的上下文已经销毁了，它仍然存在，比如内部函数从父函数中返回
    * 在代码中引用了自由变量。

### 21. new操作符

 * `new` 操作符具体做了什么？

    1. 首先创建了一个空对象
    2. 设置该对象的原型为构造函数的prototype对象
    3. 将构造函数的this指向该对象，执行其代码（为这个对象添加属性）
    4. 判断构造函数的返回值类型，若为对象，返回这个对象；若无，返回创建的对象。

* 实现

    ```JavaScript
    function ObjecFactory () {
        const Constructor = [].shift.call(arguments);
        const newObj = Object.create(Constructor.prototype);	// 创建新对象 和 设置原型(Object.create的内在逻辑)
        const ret = Constructor.apply(newObj, arguments);
        return typeof ret === 'Object' ? ret : newObj;
    }
    
    // how to use ?
    const obj = ObjFactory(构造函数，参数1，参数2...)
    ```

    

### 22. eval

​	它的功能是将传入的字符串解析成js代码并运行。



### 23. 事件

* 事件是用户操作网页时发生的交互动作，比如click、move、文档加载。

* 事件处理机制：IE只支持事件冒泡，FireFox同时支持事件捕获和事件冒泡。

* 阻止冒泡：`event.stopPropogation()` 或 `event.cancelBubble = true`（IE）;

* 取消默认行为：`event.preventDefault()` 或 `event.returnValue = false(IE)`;

* 三种事件模式：

    * DOM0事件模型
    * IE事件模型
    * DOM2事件模型

* 事件委托

    * 事件委托本质上利用了浏览器事件冒泡的机制。因为事件在冒泡过程中会上传到父节点，并且父节点可以通过事件对象获取到子节点。这种在父节点上定义监听函数，处理多个子节点的事件的方式，称作事件委托（事件代理）。

    * 好处：

        * 需要绑定的事件数少了，减少内存消耗。
        * 动态绑定：事件的绑定与目标元素的增减无关。

        

### 24. ['1', '2', '3'].map(parseInt)

> API参数理解。

### 25. js延迟加载

* 为什么延迟加载：
    * 因为js的加载、解析和执行会阻塞页面的渲染过程。
* 方式：
    * 将js放在文档底部
    * defer
    * async
    * 动态创建dom：监听文档的加载时间，当文档加载完毕后创建script标签加载js文件（`document.onload`）

### 26. AJAX

* 定义：2005 年 2 月，AJAX 这个词第一次正式提出，它是 **Asynchronous JavaScript and XML** 的缩写，指的是通过 JavaScript 的 **异步通信**，从服务器获取 XML 文档从中提取数据，再更新当前网页的对应部分，而不用刷新整个网页。

* 具体来说，AJAX 包括以下几个步骤：

    1. 创建 `XMLHttpRequest` 对象，也就是创建一个 异步调用对象
    2. 开启一个http请求，并设置该请求的方法、URL和验证信息
    3. 设置响应请求**状态变化**的函数
    4. 发送http请求
    5. 获取异步调用返回的数据并使用

* 实现：

    ```JavaScript
    // 创建
    const xhr = new XMLHttpRequest();
    // 开启请求
    xhr.open('GET', REQ_URL, true); // true : async
    // 设置头部信息
    xhr.responseType = 'json';
    xhr.setRequestHeader('Accept', 'application/json');
    // 设置响应状态变化的函数
    xhr.onreadystatechange = function () {
        if (this.readyState === XMLHttpRequest.DONE && xhr.status === 200) {
        	// XMLHttpRequest.UNSENT: 0, OPEND: 1, HEADERS_RECEIVED: 2, LOADING: 3, DONE: 4    
            console.log(xhr.responseText);
        }
    }
    // 发送请求
    xhr.send(null);
    ```

* 如何解决缓存问题：

    * url后加一个**随机数**或者**时间戳**(本质上是请求不同的url)：`fresh=Math.random()`
    * 发送请求前加上：`xhr.setRequestHeader('If-Modified-Since': 0)` 或者 `xhr.setRequestHeader('Cache-Control': 'no-cache')`



### 27. 同步异步、阻塞非阻塞

* 同步异步，关注的是“消息通信机制”，是否要主动询问。比如同步的select、poll、epoll，异步的AIO（Linux下的异步IO，会发出IO完成的通知）。
* 阻塞非阻塞，关注的是”在等待调用结果”时的状态是否被挂起（还能否做其他事）。epoll操作是在异步操作下的主动询问行为，即异步非阻塞操作。

### 28. 模块化

#### 28.1 理解

​	一个模块是为实现一个特定功能的一组方法。在最开始的时候，js实现的功能比较简单，也就无所谓模块化的概念。但随着应用复杂度的上升，代码量随之增大，为了方便对代码的维护，模块化也就越来越重要。

* 最开始的做法是利用函数具有独立作用域的特点，将函数作为模块。但这种方式很容易造成全局变量的污染，而且模块之间没有联系。
* 后来提出了对象写法，通过将函数作为对象的方法来实现。但这种做法会暴露所有的模块成员，外部可以直接访问修改内部属性的值。
* 现在最常见的是立即执行函数的写法，利用闭包实现模块的私有作用域，同时不会对全局变量造成污染。

#### 28.2 模块化规范

​	现在比较成熟的模块化规范有四种：

* CommonJS
    * `requre` 导入模块，`module.exports` 导出模块。
    * 由于是同步加载，因此更适用于服务器端。
* AMD：Asynchromous Module Definition
    * 异步加载模块，所有依赖这些模块的代码定义在回调函数中。
    * requirejs实现了这个规范。
* CMD: Common Module Definition
    * 也是异步加载模块。
    * seajs实现了这个规范。
* ES6 Module
    * `import` 导入模块
    * `export` 导出模块

#### 28.3 AMD和CMD的区别

> !!! 二者都是**异步加载**模块！！！
>
> 区别是在 定义模块时对依赖的处理 和 依赖模块的执行时机。

* 定义模块时对依赖的处理：
    * AMD推崇 **依赖前置**，在定义时就要说明依赖的模块。
    * CMD推崇 **依赖就近**，在用到时才去 require，需要解析成字符串才能知道需要的依赖，但开发便利。
* 执行时机：
    * AMD在加载完模块后立即执行
    * CMD加载完后不执行，遇到require才执行。

代码例子如下：

````javascript
// AMD
define(['./a', './b'], function (a, b) {
    ...
})

// CMD
define(function (require, exports, module) {
    var a = require('./a');
    ...
    var b = require('./b');
    ...
})
````



#### 28.4 ES6模块与CommonJS

​	它们有两个重大差异：

* CommonJS输出的是一个值的浅拷贝，ES6模块输出的是指的引用。
* CommonJS模块在运行时加载，ES6模块在编译时输出接口。

第二个差异产生的原因是，CommonJS加载的是一个对象，即 `module.exports`，这个对象只有在脚本运行的时候会生成；而ES6 模块不是对象，它的对外接口只是一种静态定义（export），在代码解析阶段就会生成。



#### 28.5 requirejs的核心原理

* 动态加载
    * 动态创建 `script` 标签异步引入脚本
    * 监听load事件，如果依赖全部加载完则执行回调。
* 避免多次加载
    * 加载时，缓存其标识，状态status设置为loading
    * 再次导入这个模块时，识别到其已被缓存，但状态不为loaded，则将回调加到 onload 回调队列的尾部。 
* 缓存
    * 存储在内部对象 moduleCache 中。

### 29. document.write 和 innerHTML的区别

* document.write 的内容会代替整个文档的内容
* innerHTML的内容只替换指定元素的内容（document.documentElement 返回根元素的**只读对象**）



### 30. DOM操作

#### 30.1 创建新节点

* `document.createDocumentFragment`：存在于内存中，在上边添加子节点不会引起页面的回流，能有更好的性能
* `document.createElement(tagName[, options])`
* `document.createTextNode(str)`

#### 30.2 添加、移除、替换、插入

* `parentNode.appendChild(aChild)`
* `parentNode.removeChild(child)`：删除并返回。`node.parentNode && node.parentNode.removeChild(node)`
* `parentNode.replaceChild(newChild, oldChild)`: return the same node as oldChild
* `parentNode.insertBefore(newNode, referenceNode)`: reference 为null，则插入到末尾。
* `element.insertAdjacentElement(position, element)`: position = beforebegin | afterbegin | beforeend | afterend

#### 30.3 查找

* `document.getElementById(id)`: 仅作为 `document` 的方法时有效（id唯一）。
* `document.getElementsByTagName`
* `document.getElementsByName`
* `document.getElementsByClassName`
* `document.querySelector()`
* `document.querySelectroAll`

#### 30.4 属性操作

* `getAttribute(key)`
* `setAttribute(key, value)`
* `hasAttribute(key)`
* `removeAttribute(key)`



### 31. 类数组对象

* 一个用于 `length`属性 和 若干索引属性的对象，可以称为类数组对象。但它不能调用数组的方法。

* 常见的类数组对象有 arguments、DOM方法的返回结果、函数（length属性返回参数个数）

* 常见的转为数组的方法：

    * 通过call调用数组的 `slice` 切片方法

        ````JavaScript
        Array.prototype.slice.call(arrayLike);
        ````

    * 通过call调用数组的 `splice` 插入方法

        ```JavaScript
        Array.prototype.splice.call(arrayLike, 0);
        ```

    * 通过call调用数组的 `concat` 拼接方法

        ```javascript
        Array.prototype.concat.call([], arrayLike);
        ```

    * 通过 `Array.from` 方法进行转换

        ```javascript
        Array.from(arrayLike);
        ```

### 32. [,,,]的长度

> 3

​	**尾后逗号** => 版本控制：上一行已经使用了尾后逗号，你可以仅仅添加新的一行，而不需要修改上一行。这使得版本控制更加清晰，以及代码维护麻烦更少。

### 33. 变量声明提升

* 变量声明提升的表现是，无论在函数的何处声明位置，好像都被提升到了函数的首部，因此可以在变量声明前访问到而不会报错。

* 出现的 **根本原因** 是，js在执行代码前有一个代码解析的过程，此时会创建执行上下文。当我们访问一个变量时，会先从当前执行上下文的作用域链首端中去查找，而作用域链的首端指向当前上下文的变量对象（包含了函数的形参、声明的变量等）。这个对象作为上下文的一个属性，也是在代码解析阶段创建，这也就出现了变量的声明提升。

    

### 34. v8引擎的垃圾回收机制

v8引擎的垃圾回收机制基于**分代回收机制**，这个机制又基于**世代假说**，这个假说有两个特点：

1. 新生的对象容易早死
2. 不死的对象会活得更久。

基于这个假说，v8引擎将内存分为 **新生代**  和 **老生代**。新创建的或者只经历过一次垃圾回收的对象称为新生代；经历过多次垃圾回收的称为老生代。

#### 34.1 新生代

新生代被分为 `From` 和 `To` 两个空间 ，`To`空间一般是闲置的。当 From空间满了之后，执行 `Scavenge`算法进行垃圾回收。当垃圾回收算法执行的时候，**应用逻辑会被停止**。这个算法分为三步：

1. 首先检查From空间的对象，对于存活的对象判断是否满足晋升老生代的条件，如果满足则晋升到老生代，否则移动到To空间。
2. 对于不存存活的对象，释放其空间。
3. From和To空间进行角色互换。

新生代晋升至老生代有两个条件：

1. 判断对象是否经历过一次Scavenge回收，若经历过，则满足条件晋升为老生代；否则移入To空间。
2. 当对象在移至To空间时，若判断其空间占比将超过To空间的 25%，晋升为老生代。设置 25% 的原因主要是因为算法结束后，两个空间结束后会交换位置，如果 To 空间的内存太小，会影响后续的内存分配。

#### 34.2 老生代

老生代采用了 **标记清除法** 和 **标记压缩法**。

* 标记清除法：首先对存活的对象进行标记，然后立即清除没有标记的对象。

> 标记清除后会形成很多内存碎片，不利于后续的内存分配。为了解决这个问题引入了 标记压缩法。

* 标记压缩法：标记阶段相同，但对于未标记的对象不会立即清除，而是将存活对象先移动到一边，再清理端边界外的内存。

> 由于需要移动对象，速度上要慢一些。因此v8在一般情况下使用 mark-sweep算法，在内存分配不足的时候使用 mark-compact 算法。

![](https://upload-images.jianshu.io/upload_images/1674837-023c7d9f472d8f1b.png?imageMogr2/auto-orient/strip|imageView2/2/w/480/format/webp)

#### 34.3  增量标记

垃圾回收过程中，应用会被停顿。对于新生代而言，存活的对象少，回收效率高，停顿时间短，造成的影响小；在老生代中，存回对象多，停顿时间较长，影响比较明显。

v8因此对标记进行了优化，将原本的标记过程分为多步，交替执行，使得每次停顿时间较短。

![](https://upload-images.jianshu.io/upload_images/1674837-3b49f9629c8abf8e.png?imageMogr2/auto-orient/strip|imageView2/2/w/555/format/webp)



### 35. 内存泄漏

* 未声明而使用了全局变量
* 使用了 setInterval 而忘记取消，并且其循环函数引用了外部变量
* 无效的DOM引用：比如js中持有对某个dom节点的引用，但文档删除了它，此时这个dom节点会继续存活在内存中。
* 不合理的闭包使用。

36. #### 实现一个页面操作不会整页刷新的网站，并且能在浏览器前进、后退时正确响应

> `pushState + ajax`

### 37.  移动端点击延迟

* 300ms
* 是因为移动端有双击缩放的操作，因此浏览器在click之后要等待300ms，看是否有再一次点击，来判断是否是缩放。
* 解决：
    * meta中禁止缩放 user-scalable=0
    * js库fastclick处理
    * meta设置viewport为 ideal viewport，width=device-width

#### 38. 移动端点击穿透

​	假如页面上有两个元素A和B。B元素在A元素之上。我们在B元素的touchstart事件上注册了一个回调函数，该回调函数的作用是隐藏B元素。我们发现，当我们点击B元素，B元素被隐藏了，随后，A元素触发了click事件，因为 300ms 后触发 click事件。



### 39. 前端路由

1. 什么是前端路由？

    前端路由就是把不同路由对应不同内容或页面的任务交由前端完成。（路由：源到目的地的寻址过程）

2. 什么时候使用？

    在单页面应用，大部分页面结构不变，只改变部分内容时使用。

3. 优缺点

    * 优点：不需要每次都从服务器获取，快速展示，用户体验好
    * 缺点：单页面无法记住之前滚动的位置。

4. 实现方式：

    * hash + onhashchange
    * pushState

### 40. 如何获取文件拓展名

````javascript
// 对于 'filename' 和 '.hiddenfile' ，lastIndexOf 的返回值分别为 0 和 -1 无符号右移操作符(>>>) 将 -1 转换为 4294967295 ，将 -2 转换为 4294967294 ，这个方法可以保证边缘情况时文件名不变。
function getFileExtension(filename) {
	return filename.slice( ((filename.lastIndexOf('.') - 1) >>> 0) + 2 );
}
````

### 41. 节流防抖

* 防抖
    * 函数在触发后的n毫秒后执行，如果在这段时间内被再次触发，则重新计时
    
    * 可以用在用户点击发送请求的场景中，避免多次点击而重复发送请求
    
        ```javascript
        function debounce (fn, delay) {
            let timer = null;
            return function () {
                const args = arguments;
                const ctx = this;
                clearTimeout(timer);
                timer = setTimeout(() => {
                    fn.apply(ctx, args);
                }, delay);
            }
        }
        ```
    
* 节流
    * 在一定时间内多次触发，函数只执行一次。
    
    * 比如在scroll函数的监听事件中，通过节流降低事件的调用评率。
    
        ````javascript
        function throttle (fn, delay) {
            let pre = Date.now();
            return function () {
                const ctx = this;
                const args = arguments;
                let cur = Date.now();
                if (cur - pre > delay) {
                    fn.apply(ctx, args);
                    pre = cur;
                }
        	}
        }
        ````

### 42. escape、encodeURI、encodeURIComponent

* escape 
    * 编码对象为字符串，使得它们在所有电脑上可读，
    * 不会被编码的：`ASCII字母 数字 @*/+`。
    * 同encodeURI基本相似，都是属于 `percent-encoding`， 但在编码 `0xff` 之外的字符时，是直接在uincode编码前加上 `%u`，而 encodeURI是先转换为 utf-8，而后在前边加上 `%`
* encodeURI 
    * 对 URL，
    * 合法的：`ASCII字母 数字 ~!@#$&\*()=:/,;?+'`
* encodeURIComponent 
    * 对 URL，
    * 合法的：`ASCII字母 数字 ~!\*()'`，要比encodeURI
    * 对参数进行编码

### 43. unicode 与 utf-8

* unicode只是一个字符集（可以容纳100多万个字符），只规定了字符的二进制代码**表示**，而没有规定这个二进制代码该怎么**存储**。
* utf-8 是 Unicode的实现方式之一，采用变长的编码方式，可以使用 1 ~ 4 个字节来表示一个字符。

[字符编码笔记：ASCII，Unicode 和 UTF-8]:http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html



### 44. js的事件循环

js引擎的执行过程可以简单描述为：

1. 函数(实际上是执行上下文)压入调用栈形成栈帧，执行其中的同步代码，如果遇到函数，继续压入栈，然后进入这个执行环境执行代码；当执行到异步任务时，将其移交给 Web APIS处理，接着执行同步任务，直到栈为空；
2. 与此同时，Web APIs 处理异步任务事件（DOM、Ajax、Timeout等），完成事件后，将回调函数放入回调队列 Callback Queue （也叫事件队列 Task Queue）中；
3. 调用栈为空时，**主线程** 将 回调队列中的 **一个** 回调函数压入调用栈中，执行其中的同步代码...

如此反复，形成一个无限的过程，因此称为“事件循环” Event Loop。

![](https://picb.zhimg.com/80/v2-da078fa3eadf3db4bf455904ae06f84b_720w.jpg)



 以上对事件循环的描述是一个宏观的过程，实际上因为异步任务之间并不相同，也因此有执行优先级的差异。不同的异步任务被分为两大类：宏任务（Micro Task）和 微任务（Micro Task）。

* 宏任务：
    * script脚本的执行
    * setTimeout、setInterval、setImmediate一类的定时任务
    * I/O操作
* 微任务：
    * promise
    * mutationObserve: 对dom树变动进行监测

也因此也将事件队列区分为 **微任务队列** 和 **宏任务队列**。

在调用栈空了的情况下，如果微任务队列不为空，首先会先将之全部执行完毕，而后在宏任务队列中取出一个事件。换句话说，在一次事件循环中，微任务永远在宏任务之前执行，且全部执行完。

```javascript
setTimeout(function () {
    console.log(1);
});

new Promise(function(resolve,reject){
    console.log(2)
    resolve(3)
}).then(function(val){
    console.log(val);
})

// 2
// 3
// 1
```



### 45. 深浅拷贝

深浅拷贝的差异在于拷贝引用类型的值的处理方式。浅拷贝只是简单复制了引用对象的地址，实际上仍是指向同一个对象；深拷贝则是创建了新的对象。

#### 45.1 浅拷贝

```javascript
function shallowCopy (obj) {
    if (!obj || typeof obj !== 'object') {
        return;
    }
    const newObj = Array.isArray(obj) ? [] : {};
    for (const key in obj) {
        if (obj.hasOwnProperty(key)) {
            newObj[key] = obj[key];
        }
    }
    return newObj;
}
```



#### 45.2 深拷贝

```javascript
function deepCopy (obj) {
    if (!obj || typeof obj !== 'object') {
        return;
    }
    const newObj = Array.isArray(obj) ? [] : {};
    for (const key in obj) {
        if (obj.hasOwnProperty(key)) {
            newObj[key] = typeof obj[key] === 'object' ? deepCopy(obj[key]) : obj[key];
        }
    }
    return obj;
}
```



### 46. call、apply、bind函数手写

#### 46.1 call

* 实现步骤：

    * 判断调用对象是不是函数，因为call方法定义在函数原型上
    * 获取参数
    * 设置上下文context，如果未传入，则将其设置为window
    * 将调用函数设置为context对象的方法
    * 传入参数调用
    * 返回结果

* 实现

    ```javascript
    Function.prototype.myCall = function (context, ...args) {
        if (typeof this !== 'function') {
            return new Error('Type Error');
        }
        const ctx = context || window;
        let result;
        
        ctx.fn = this;
        result =  ctx.fn(...args);
        
        delete ctx.fn;
        return result;
    }
    ```

#### 46.2 apply

* 实现步骤

* 代码

```JavaScript
Function.prototype.myApply = function (context, args) {
	...

	result = ctx.fn(ctx, args);

	...
}	
```

#### 46.3 bind

* `bind()` 函数创建一个新的函数，当这个函数被调用时，bind的第一个参数作为运行时this，其余的参数将会在传递的实参前传入作为新函数的参数。

* 实现步骤

    * 类型判断

* 实现

```JavaScript
Function.prototype.myBind = function (context) {
    if (typeof this !== 'function') {
    	throw new Error('Type Error');
    }
    const fn = this;
    const args = [].slice.call(arguments, 1);
    
    function fNOP () {}
    function fBound () {
        const bindArgs = [].slice.call(arguments);
        return fn.apply(
        	this instanceof fNOP ? this : context,
            args.concat(bindArgs)
        );
    }
    
    fNOP.prototype = fn.prototype;
    fBound.prototype = new fNOP();
    return fBound;
}
```

​        


### 47.  函数柯里化

柯里化是一种将 使用**多个参数的一个**函数 转换成 **一系列使用一个参数**的函数 的技术。

```javascript
function curry (fn, args) {
    const len = fn.length;
    args = args || [];
    
    return function () {
        const subArgs = args.slice(0);
    	for (let i = 0; i < arguments.length; i++) {
            subArgs.push(arguments[i]);
        }
        if (subArgs.length >= len) {
            return fn.apply(this, subArgs);
        } else {
            return curry.call(this, fn, subArgs);
        }
    }
}

// es6
function curry (fn, ...args) {
    return fn.length <= args.length ? fn.apply(this, args) : curry.bind(this, fn, ...args);
}
```



### 48. 原码、反码、补码

* 原码
    * 计算机中对数字二进制的定点表示法，最高位表示 **符号**，其余为表示 **数值位**。
    * 优点是易于分辨，缺点是不能直接参与运算。
* 反码：
    * 正数的反码等于原码，负数的反码符号位为2，数值位按位取反。
    * `[+7] 原 = 0000 0111`，`[+7] 反 = 0000 0111`
    * `[-7] 原 = 1000 0111`，`[-7] 反 = 1111 1000`
* 补码：
    * 正数的补码等于原码，负数的补码为其反码 + 1
    * `[+7] 原 = 0000 0111`，`[+7] 反 = 0000 0111`，`[+7] 补 = 0000 0111`
    * `[-7] 原 = 1000 0111`，`[-7] 反 = 1111 1000`，`[-7] 补 = 1111 1001`

> 计算机使用补码表示负数的原因在于，可以将**加法运算拓展**到所有的数值上，数字电路中只需要设计**加法器**就可以了。

[关于2的补码]:http://www.ruanyifeng.com/blog/2009/08/twos_complement.html



### 49. Object.defineProperty

三个参数：

* 要定义的对象obj
* 要定义的属性prop
* 属性描述符
    * value：值
    * enumerable：枚举
    * writable：可否**赋值修改**
    * configurable：是否可以从对象上**删除**这个属性，以及是否可以修改除value、writable之外的其他特性。
* set、get

### 50. 函数式编程

​	函数式编程是一种“编程范式”，也就是如何编程的方法论。它有五个鲜明的特点：

* 函数是”第一等公民“。第一等公民指的是，函数跟其他数据类型一样处于平等的地位，可以赋值给其他变量，可以作为参数，也能作为返回值。
* 只用“表达式”，不用“语句”。表达式是一个单纯的运算过程，总有返回值；语句是一个操作过程，没有返回值。
* 没有副作用（side effect）。函数保持独立，所有功能仅返回值，而没有其他行为，尤其是改变外部变量的值。
* 不修改状态
* 引用透明：不依赖外部变量或“状态”，只要参数相同，函数的返回值相同。

​	

### 51. 异步编程的实现方式

* 回调函数

    上下层函数的代码耦合度高，不利于维护。

* 事件

    流程不够清晰

* promise

    可以将嵌套的回调函数换位链式调用。但多个 then 的链式调用可能会造成语义的不清晰。

* generator

    * generator 可以在函数执行的过程中，将函数的执行权转移出去（yield），在函数外部我们可以将执行权转移回来（next）。
    * 那么，当遇到异步函数的时候，将执行权转移出去，在异步函数执行完毕的时候将执行权转移回来。如此，在generator函数内部可以用同步的顺序书写异步逻辑。
    * 要考虑的问题是，何时将函数的执行权转移回来。因此就需要有一个自动执行generator的机制，比如 TJ 的co模块。

* async/await

    * async函数时 generator 和 promise 实现的一个自动执行的语法糖，它内部自带执行器。
    * 函数在执行到 await 语句的时候，如果语句返回的是一个promise对象，那么函数将等待至promise对象的状态变为 resolve后再继续向下执行。因此可以用同步的顺序书写异步逻辑，并且这个函数会自动执行。
    * try...catch.. 的错误捕获可能不太讨喜。

### 52. js动画与css动画

css3 动画由于浏览器的优化在性能上要好一些，代码相对也要简单一些。但在动画控制上不够灵活，而且也存在兼容性问题。

js动画则弥补了这两个缺点。

### 53. 图片的懒加载和预加载

* 懒加载

    * 也叫延迟加载，指的是在长网页中延迟加载图片的时机，等到用户需要访问时再加载，这样可以提高首屏的加载速度，提高用户体验，并且可以降低服务器的压力。

    * 适用网页较长，图片较多的场景。

    * 原理一般为：开始时img的src设置为空字符串，同时将其真实路径保存在一个自定义属性中。当页面滚动的时候，判断其是否在可视区域内，如果在则替换src属性加载。

        ```JavaScript
        const { top, bottom } = imgItem.getBoundingClientRect();
        const viewHeight = document.documentElement.clientHeight || window.innerHeight;
        if (top < viewHeight && bottom > 0) {
            imgItem.setAttribute('src',  imgItem.dataset.original);
        }
        ```

* 预加载

    * 将所需的资源预先请求加载到本地，在需要的时候从缓存中获取。预加载可以减少用户的等待时间，提高用户体验
    * 图片预加载可以使用 `new Image()`设置src属性来预加载
    * 会加载服务器的压力。

### 54. mouseover 和 mouseenter

当鼠标移动至元素上时就会触发 `mouseenter`时间，与 `mouseover` 类似，不同之处在于 `mouseenter` 不会冒泡。



### 55. jsonp实现

```JavaScript
function myJsonp (url, params, callback) {
    let queryString = url.indexOf('?') === -1 ? '?' : '&';
    for (const key in params) {
		if (params.hasOwnProperty(key)) {
            queryString += `${key}=${params[key]}&`;
        }
    }
    const callbackName = 'myJsonp' + Math.random().toString().replace('.', '');
    url += queryString + 'callback=' + callbackName
    
    const scriptEle = document.createElement('script');
    scriptEle.src = url;
    window[callbackName] = function() {
		callback(...arguments);
        delete window[callbackName];
        document.getElementsByTagName('head')[0].removeChild(scriptEle);
    }
    document.getElementsByTagName('head')[0].appendChild(scriptEle); // 跟图片不同，需要插入到文档中才会下载
}
```



### 56. 类型判断

```javascript
function getType (value) {
    // null
    if (value === null) {
        return value + '';
    }

    // 引用类型
    if (typeof value === 'object') {
        return 
        Object.prototype.toString
            .call(value).split(' ')[1]
            .slice(0, -1)
            .toLowerCase();
    } else {
    // 函数 和 基本类型
        return typeof value;    
    }
}
```



### 57. performance API

> 如何确定页面的可用性时间？

此前为了得到脚本的运行耗时，一般使用Date对象的 `getTime` 方法。但存在两个局限性：

1. 精度不够：毫秒级
2. 无法获悉后台事件的运行进度，比如浏览器用了多少时间从服务器加载网页。

为了解决这个两个问题，ES5 引入了"高精度时间戳"的 API，部署在 `performance` 对象上。

* `performance.timing`：包含了各种与浏览器性能相关的时间数据，提供浏览器处理网页时各个阶段的耗时，比如 `performance.timing.navigationStart` 就是浏览器处理当前网页的启动时间。
    * TCP连接耗时：`tcp = t.connectEnd - t.connectStart`
    * 域名解析：`dns = t.domainLookupEnd - t.domainLookupStart`
    * 读取页面第一个字节前的耗时：`ttfb = t.responseStart - t.navigationStart`
    * 页面加载耗时：`pageLoadTime = t.loadEventStart - t.navigationStart`
* `performance.now()`：返回 `navigationStart` 到当前时间的毫秒数，有小数，精度更高。
* `performance.navigation`：用户行为信息
    * type: 网页加载来源 0、 1、 2、 255
    * redirectCount：当前网页时经过了几次重定向到来的。
* `performance.getEntries()`: 获取各个请求对象的时间统计信息。
* ...



### 58. 为什么要使用 setTimeout 模拟 setInterval

`setInterval`的作用是每隔一段时间执行一次函数，但其真正的作用是每隔一段时间往事件队列中放入一个回调函数，但只有当前执行栈空了之后，才会从事件队列中取出一个回调执行。导致的结果就是，如果有一段逻辑执行时间太长（比如多个时间间隔长度），那么事件队列中就可能积累了多个相同回调，当执行栈空的时候，多个回调被依次取出执行了。

针对这个缺点，可以使用 `setTimeout` 模拟其作用，在确保一个函数执行完毕后再触发下一个定时器。

```JavaScript
function myInterval (fn, timeout) {
    const timer = {
        flag: true
    };
    
    function interval () {
        if (timer.flag) {
            fn();
            setTimeout(interval, timeout);
        }
    }
    setTimeout(interval, timeout);
    
    return timer;
} 
```

