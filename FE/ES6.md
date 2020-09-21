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
> 内部对值的相同与否判断类似 `===`，不同之处在于Set认为 NaN等于自身。



### 5. Map 和 WeakMap



