[TOC]

### 1. `let`、`const` 的注意点
* 不存在声明提升
* 存在暂时性死区，声明前使用会报错
* 不允许重复声明
* 声明的变量只在声明的代码块内有效

### 2. rest

res参数，`(...rest)`，用于获取函数的多余参数



### 3. Symbol 注意点

1. Symbol 不能通过new 调用
2. Symbol 接受一个字符串作为参数，表示对该实例的描述，主要是为了在 控制台中显示或者转为字符串时做区分。
3. Symbol作为属性时，不会出现在 `for-in` 、`for-of` 遍历中，也不会在 `Object.keys`、`Object.getOwnPropertyNames`、`JSON.stringify`中返回。
4. `Object.getOwnPropertySymbols()` 返回当前对象的所有作为属性名的symbol
5. `Symbol.for(str)` 接收一个字符串作为参数，然后搜索是否存在以该字符串为名称的symbol值，如果有则返回；如果没有则创建一个以该字符串为名称的symbol，并注册到全局以供搜索。`Symbol(str)`则是每次都创建新的，并且不会注册。
6. `Symbol.keyFor()` 返回一个注册登记了的Symbol值的key。

### 4. Set 与 WeakSet

* 相同点：都是不重复值的集合
* 差别：两个
    * `WeakSet`中的值类型只能是对象；
    * `WeackSet`中的对象都是弱引用，即垃圾回收机制不会考虑该对象的引用。也由于这个原因，它的成员会随时消失（因为垃圾回收机制的触发不可预测），因此ES6规定它不能遍历。

> Set是构造函数（可以传入具有iterabel特性的数据结构，比如Array、Map、Set、类数组（arguments、DOM List）和字符串。
>
> 内部对值的相同判断类似 `===`，不同之处在于Set认为 NaN等于自身。



### 5. Map 和 WeakMap

* `map` 数据结构类似于对象，是键值对的集合。但“键”的范围不局限与字符串，各种类型的值（包括对象）都可以作为键。
* `weakMap` 也是键值对的集合，但它的键只能是 `对象`（null除外），并且对这个对象的引用是弱引用。同map相比，
    * 没有遍历操作：keys、entries、values、forEach
    * 也没有清空方法`clear()`（因此只有 set、get、has 和 delete 方法）。

### 6. Proxy 是什么

* Proxy用于 **修改某些操作的默认行为**，等同于在语言层面进行修改，属于一种**元编程（meta programming）**，即对编程语言进行编程。
* Proxy的原意是“代理”，在ES6中它代理了某些操作，可以理解为“代理器“。它在**目标对象**之前设置了一层”拦截“，外界的访问需要先通过这个拦截。因此这就提供了一种机制，可以对外界的访问进行**过滤和改写**。



### 7. Reflect 对象创建的目的

1. 挂载 `Object` 对象的一些明显属于语言内部的方法，比如 `Object.defineProperty`
2. 修改某些 `Object`方法的返回值，使之更为合理。比如 `Object.defineProperty`在无法定义属性时是抛出错误，而 `Reflect.defineProperty` 是返回 false。
3. 让 `Object`的命令式操作转为`函数行为`，比如 `name in obj` 和 `delete obj[name]`，对应的函数行为为 `Reflect.has(obj, name)` 和 `Reflect.deleteProperty(obj, name)`。
4. Reflect 与 Proxy对象的方法一一对应，可以保留相应方法的默认行为。



### 8. require引入的查找逻辑

​	当 node 遇到 `require(x)` 时，区分三种情况讨论如下：

1. x 是内置模块，比如 `http`，直接返回不再查找。
2. x 是以 **路径分隔符** 开头的，比如 `./` ：首先根据 x 所在的父模块确定绝对路径，然后分两种情况依次处理：
    1. 先当做文件，依次查找：`x`、`x.js`、`x.json`、`x.node` 文件；
    2. 找不到则当做目录，依次查找：
        * `x/package.json(用于读取其中main字段)`
        * `x/index.js`
        * `x/index.json`
        * `x/index.node`
3. x 不带路径：从父模块开始，依次确定安装目录（node_modules）

如果上述三种情况都未能找到符合条件的文件，则抛出 `Not Found`错误。







