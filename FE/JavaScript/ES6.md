[TOC]

### `let`、`const` 的注意点

* 不存在声明提升
* 存在暂时性死区，在声明前使用会报错
* 不允许重复声明
* 声明的变量只在声明的代码块内有效

### rest

res参数，`(...rest)`，用于获取函数的多余参数



###  Symbol 注意点

1. Symbol 不能通过new 调用
2. Symbol 接受一个字符串作为参数，表示对该实例的描述，主要是为了在 控制台中显示或者转为字符串时做区分。
3. Symbol作为属性时，不会出现在 `for-in` 、`for-of` 遍历中，也不会在 `Object.keys`、`Object.getOwnPropertyNames`、`JSON.stringify`中返回。
4. `Object.getOwnPropertySymbols()` 返回当前对象的所有作为属性名的symbol
5. `Symbol.for(str)` 接收一个字符串作为参数，然后搜索是否存在以该字符串为名称的symbol值，如果有则返回；如果没有则创建一个以该字符串为名称的symbol，并注册到全局以供搜索。`Symbol(str)`则是每次都创建新的，并且不会注册。
6. `Symbol.keyFor()` 返回一个注册登记了的Symbol值的key。

###  Set 与 WeakSet

* 相同点：都是不重复值的集合
* 差别：两个
    * `WeakSet`中的值类型只能是对象；
    * `WeackSet`中的对象都是弱引用，即垃圾回收机制不会考虑该对象的引用。也由于这个原因，它的成员会随时消失（因为垃圾回收机制的触发不可预测），因此ES6规定它不能遍历。

> Set是构造函数（可以传入具有iterabel特性的数据结构，比如Array、Map、Set、类数组（arguments、DOM List）和字符串。
>
> 内部对值的相同判断类似 `===`，不同之处在于Set认为 NaN等于自身。



###  Map 和 WeakMap

* `map` 数据结构类似于对象，是键值对的集合。但“键”的范围不局限与字符串，各种类型的值（包括对象）都可以作为键。
* `weakMap` 也是键值对的集合，但它的键只能是 `对象`（null除外），并且对这个对象的引用是弱引用。同map相比，
    * 没有遍历操作：keys、entries、values、forEach
    * 也没有清空方法`clear()`（因此只有 set、get、has 和 delete 方法）。

###  Proxy 是什么

* Proxy用于 **修改某些操作的默认行为**，等同于在语言层面进行修改，属于一种**元编程（meta programming）**，即对编程语言进行编程。
* Proxy的原意是“代理”，在ES6中它代理了某些操作，可以理解为“代理器“。它在**目标对象**之前设置了一层”拦截“，外界的访问需要先通过这个拦截。因此这就提供了一种机制，可以对外界的访问进行**过滤和改写**。



### Reflect 对象创建的目的

1. 挂载 `Object` 对象的一些明显属于语言内部的方法，比如 `Object.defineProperty`
2. 修改某些 `Object`方法的返回值，使之更为合理。比如 `Object.defineProperty`在无法定义属性时是抛出错误，而 `Reflect.defineProperty` 是返回 false。
3. 让 `Object`的命令式操作转为`函数行为`，比如 `name in obj` 和 `delete obj[name]`，对应的函数行为为 `Reflect.has(obj, name)` 和 `Reflect.deleteProperty(obj, name)`。
4. Reflect 与 Proxy对象的方法一一对应，可以保留相应方法的默认行为。







### Promise 和 Promise/A+规范

​	Promise 对象是异步编程的一种解决方案，最早由社区提出。Promise/A+规范 是 JavaScript Promise的标准，规定了Promise的特性：

* Promise 是一个 构造函数，接受一个函数作为参数，返回promise实例；
* 一个promise实例有三种状态：pending、resolved 和 rejected，分别表示进行中、已成功 和 已失败。resolved 和 rejected 状态只能从 pending中转变而来，且一经改变就凝固了，无法再改变。
* 这两种转变通过调用方法 `resolve()` 和 `reject()`，可以在 **异步** 操作结束后调用这两个方法改变promise实例的状态。
* Promise的原型上还定义了 then 方法，可以使用它为两个状态定义回调函数（属于微任务）。

Promise的代码实现：

````JavaScript
function MyPromise (fn) {
    
}
````



### async/await

`async`可以看做是 自带执行器的 generator；await 命令可以看做是 内部 `then`命令的语法糖（调用后边promise的then方法，并且在其中包裹后续的逻辑）。

#### 基本用法

`async`函数返回一个promise对象，可以使用 `then`方法添加回调函数。函数执行时，遇到 `await` 就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。比如：

````JavaScript
async function timeout(ms) {
	return new Promise((resolve, reject) => {
        setTimeout(resolve, ms)
    });
}

async function asyncPrint (value, ms) {
    await timeout(ms);
    console.log(value);
}

asyncPrint('test', 500); // 延迟500ms后打印
````

async 函数的多种使用形式：

```JavaScript
// function declaration
async function foo() {}

// function expression 
const foo = async function () {};

// arrow function 
const foo = async () => {};

// obj method
const obj = { async foo () {} };
obj.foo().then(...);
               
// method of Class
class Test {
	async foo () {
	
	}
}
const tes = new Test();
test.foo().then(...);
```



#### 语法

* **返回promise**

    * return 的值会成为 then方法的参数
    * 内部抛出错误，会使得返回的promise对象变为 rejected状态；抛出的错误也会成为catch方法的参数

* **promise状态改变**

    async函数返回的promise对象，必须等到内部所有await之后的promise全部执行完，状态才能改变为resolved；除非遇到return语句或者抛出错误。

* **await命令**

    * 正常情况下，await之后是一个promise，返回该对象的结果，如果不是promise，直接返回对应的值。
    * 当后边是一个 thenable对象（即定义了then方法的对象），等同于promise处理。
    * 任何一个await命令后边的promise变为rejected状态，整个async都将中断执行。解决？
        * `try..catch...` 包裹，处理错误
        * `await` 后边的promise再添加catch回调：`await Promise.reject('出错了').catch(e => console.log(e));`

* **错误处理**：`try...catch...`

* **注意的点**：



#### async函数的实现原理

async函数的实现原理，就是将 `generator`函数 和 **自动执行器**，包装在一个函数里。

```JavaScript
async function fn(args) {
    
}

//  等同于
function fn (args) {
	return spwan(function * () {
        ...
    })
}
```

spaw函数实现如下：

```JavaScript
function spwan (genF) {
    return new Promise(function(resolve, reject) {
        const gen = genF();
        function step (nextF) {
            let next;
            try {
                next = nextF();
            } catch(err) {
               return reject(err);
            }
            
            if (next.done) {
                return resolve(next.value);
            }
            
            Promise.resolve(next.value).then(function(v) {
                step(function() { return gen.next(v); })
            }, function (err) {
                step(function() { return gen.throw(err); })
            });
            
            step(function () { return gen.next() });
        }
    });
}
```



