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



