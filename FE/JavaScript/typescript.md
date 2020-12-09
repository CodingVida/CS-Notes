[TOC]

---

TypeScript 是js的一个超集，主要提供了 **类型系统**（静态检查） 和 **对ES6的支持**，由微软开源。



## 基础

ts的常用类型和基本概念。



### 原始数据类型

#### 布尔值

```typescript
let isDone: boolean = false;

// 使用构造函数Boolean生成的是对象，不是原始数据类型（primitive data type）
let createByNewBoolean: boolean = new Boolean(1);
// Type 'Boolean' is not assignable to type 'boolean'.'boolean' is a primitive, but 'Boolean' is a wrapper object. Prefer using 'boolean' when possible.ts(2322)
```



#### 数值

````typescript
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let notANumber: number = NaN;
let infinityNumber: number = Infinity;
// es6
let binaryLiteral: number = 0b101;
let octalLiteral: number = 0o744;
````

编译结果为：

```JavaScript
var decLiteral = 6;
var hexLiteral = 0xf00d;
var notANumber = NaN;
var infinityNumber = Infinity;
// es6
var binaryLiteral = 5;
var octalLiteral = 484;
```

其中，es6的 **二进制和八进制** 表示法被编译成 **十进制**。



#### 字符串

```typescript
let myName: string = 'yoto';
let myAge: number = 25;

// template string
let sentence: string = `Hello, my name is ${myName}.
I'll be ${myAge} next month`;
```

编译结果为：

```JavaScript
var myName = 'yoto';
var myAge = 25;
// template string
var sentence = "Hello, my name is " + myName + ".\nI'll be " + myAge + " next month";
```



#### 空值

JavaScript中没有空值（Void）的概念，在typescript中，可以使用 `void` 表示没有任何返回值的函数：

```typescript
function alertName(): void {
    alert('name');
}
```

当它用于声明一个变量时，这个变量只能被赋值为 `undefined` 或 null：

```JavaScript
let unusable: void = undefined;
```



#### Null 和 Undefined

在 typescript中，可以使用 `null` 和 `undefined` 来声明这两个原始类型：

````typescript
let u: undefined = undefined;
let n: null = null;
````

与空值类型不同的是，`null` 和 `undefined` 是所有其他类型的子类型，因此在 **非严格模式下** 可以被赋值给其他类型而不会出错，比如：

```typescript
let num: number = null;
let num2: number = undefined;
```



### 任意值

任意值 `any` 用来表示可以赋值为任意类型:

```typescript
let anyThing: any = 'number';
anyThing = 132;
```

对于一个任意值，访问它的任意方法和属性都是许可的，并且对它的任何操作，返回值都是任意值类型：

```typescript
anyThing.name;
anyThing.test('test');
```

此外，如果变量在声明的时候没有指定类型，那么会被识别为任意值类型。



### 类型推断

如果没有明确指定一个类型，那么ts会依照 类型推断（Type Inference）的规则推断出一个类型：

* 声明时有赋值，那么推断为值的类型；
* 声明时没有赋值，推断为 `any`



### 联合类型

联合类型（Union Types）表示可以有多种取值。

一般情况下：

```typescript
let test: string | number;
test = 234;
test = 'test';
```

当typescript不能确定一个联合类型的变量到底是哪个类型时，我们**只能访问此联合类型的所有类型里共用的方法或属性**：

```typescript
function getLength (sm: string | number): number {
    return sm.length;
}
// index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
// 这里 sm 的类型时无法判断的，而number类型没有 length 属性，故而报错。

function getString(something: string | number): string {
    return something.toString();
}
// toString 是两种类型公有的方法。
```

当联合类型的变量被赋值的时候，ts会为它推断出一个类型（它仍是联合类型），此时会对访问它的方法、属性有所限制：

````typescript
let test: string | number;
test = 135;
test.length; // error
test = '123';
test.length; // 3
````





### 接口 - 对象的类型

ts使用接口（Interface）来定义对象的类型。它是一个比较灵活的概念，除了用于 **对类的一部分行为进行抽象** 之外，它还常用于 **对“对象的形状”**进行描述。比如：

````typescript

interface Person {
    name: string,
    age: number
}

let tom: Person = {
    name: 'tom',
    age: 234
}
// tom 这个对象要有对应的key，缺了name或者age(或类型对应不上）都将报错。
````

声明的对象是不能缺少或者多了属性的，即赋值的时候，对象的“形状”就跟接口一致了。



#### 可选属性

有时某些属性不一定要有，那可选择使用可选属性，其含义是该属性可以不存在：`?`

```typescript
interface Person {
    name: string,
    age?: number
}

let tom: Person = {
    name: 'tom',
}
```

此时表明可以缺少可选的属性，但仍不能多出属性。



#### 任意属性

有时候希望接口允许有任意的属性，如下：

```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}
```

使用 `[propName: string]: string` 定义了，任意属性键为 string类型，值为 string类型。

需要注意的是，**一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集**：

```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}

let tom: Person = {
    name: 'Tom',
    age: 25,	// 值为 number类型，不是任意属性类型的子集，报错。
    gender: 'male'
};
```

因为任意属性的键类型被定义为了 `string`类型，其他的键也同样为 `string`类型，那么任意属性值的约束也会对其他属性值起作用。

#### 只读属性

有时希望对象的属性在初始化时赋值，那么可以使用 `readonly` 定义只读属性：

````typescript
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: string | number;
}
let tom: Person = {
    id: 1,
    name: 'Tom',
    age: 23,
    test: 'asdf'
};

tom.id = 2;  // Cannot assign to 'id' because it is a read-only property.
````



### 数组类型

ts中，数组有多种定义方式

#### 类型 + 方括号

```typescript
let fibonacci: number[] = [1, 1, 2, 3];

fibonacci.push('8');	// Argument of type 'string' is not assignable to parameter of type 'number'.
```

#### 数组泛型

使用 `Array<elementType>` 表示数组泛型。

```typescript
let fibonacci: Array<number> = [1, 1, 2, 3];
```

#### 数组接口

````typescript

interface arrayType {
    [index: number]: number
}

let fabonacci: arrayType = [1, 1, 2,3];

````

#### 类数组

类数组（Array-like Object）不是数组类型：

```typescript
function foo () {
    let num: number[] = arguments; 
    // Type 'IArguments' is missing the following properties from type 'number[]': pop, push, concat, join, and 24 more.
}
```

类数组的定义应该使用接口：

```typescript
 
function foo () {
    let args: {
        [index: number]: number,
        length: number,
        callee: Function
    } = arguments;
}
```

常见的类数组都有自己的接口定义，比如 `IArguments`、`NodeList`、`HTMLCollection` 等。

#### any在数组中的应用

比较常见的做法是用 `any`表示数组中允许出现任意类型：

```typescript
let list: any[] = ['test', 123, { foo: function () {} }];
```



### 函数类型

函数有输入和输出，使用typescript对其进行约束时，需要同时考虑到它们。函数的定义方式有两种：函数声明和函数表达式：

#### 函数声明

```typescript
function sum (x: number, y: number): number {
    return x + y;
}
```

#### 函数表达式

对匿名函数进行类型定义：

```typescript
let sum = function (x: number, y: number): number {
	return x + y;
}
```

此时 `sum` 的类型并不是我们定义的，而是通过类型推断得到的。

同时对 `sum` 进行类型定义：

```typescript
let sum: (x: number, y: number) => number = function (x: number, y: number): number {
	return x + y;
}
```

如上，typescript中也有箭头 `=>`，用来表示函数的定义，左侧是参数（需要括号），右侧是返回值。

#### 接口定义

```typescript
interface myFunc {
    (x: number, y: number): number;
}

let sum: myFunc = function (x, y) {
    return x + y;
}
```

#### 可选参数

用 `?` 表示可选的参数：

```typescript
function getName (firstName: string, lastName?: string): string {
    if (lastName) {
		return firstName + ' ' + lastName;
    }
    return firstName;
}
```

**可选参数之后不能再出现必需参数**。

#### 默认参数

```typescript
function getName (firstName: string, lastName: string = 'Cat'): string {
    if (lastName) {
		return firstName + ' ' + lastName;
    }
    return firstName;
}
```

#### 剩余参数

```typescript
function push (array: any[], ...items: any[]) {
	// 如同定义，剩余参数 items 实际上是一个数组。
    items.forEach(item => {
        array.push(item);
    })
}
```

**rest 参数只能是最后一个参数**。

#### 重载定义

可以为函数定义多个不同的类型。

```typescript
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    }
    return x.split('').reverse().join('');
}
```

TypeScript 会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。



### 类型断言

手动指定一个指的类型。

#### 语法

`值 as 类型` 或者 `<类型> 值`。

推荐使用前一种。



#### 用途

* 将一个联合类型断言为其中一个类型

    当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们**只能访问此联合类型的所有类型中共有的属性或方法**。当有时需要根据其中差异判断（比如能力检测），此时可以使用断言。

    ```typescript
    interface Cat {
        name: string;
        run(): void;
    }
    interface Fish {
        name: string;
        swim(): void;
    }
    
    function isFish(animal: Cat | Fish) {
        if (typeof (animal as Fish).swim === 'function') {
            return true;
        }
        return false;
    }
    ```

* 将一个父类断言为具体的子类

    ```ts
    interface ApiError extends Error {
        code: number;
    }
    interface HttpError extends Error {
        statusCode: number;
    }
    
    function isApiError(error: Error) {
        if (typeof (error as ApiError).code === 'number') {
            return true;
        }
        return false;
    }
    ```

* 将任意类型断言为any

    ```ts
    (window as any).foo = 1;
    ```

* 将any类型断言为某个类型

    ```ts
    function getCacheData(key: string): any {
        return (window as any).cache[key];
    }
    
    interface Cat {
        name: string;
        run(): void;
    }
    
    const tom = getCacheData('tom') as Cat;
    tom.run();
    ```

#### 限制

- 联合类型可以被断言为其中一个类型
- 父类可以被断言为子类
- 任何类型都可以被断言为 any
- any 可以被断言为任何类型
- 要使得 `A` 能够被断言为 `B`，只需要 `A` 兼容 `B` 或 `B` 兼容 `A` 即可



### 声明文件

#### 声明语句

假如使用了第三方库 jQuery，通常做法是通过script标签引入。这样在ts中会报错：

```ts
jQuery('#foo');
// Cannot find name 'jQuery'.
```

这时可以使用声明语句 `declare var` 来定义它的类型：

```ts
declare var jQuery: (selector: string) => any;
```

`declare var` 没有真正定义了一个变量，只是定义了全局变量 jQuery的类型，用于静态类型检查，在编译结果中会被删除。

将一系列声明语句抽到一个单独的文件（比如 `jQuery.d.ts`）中，这个文件就叫做**声明文件**，它的后缀为 `d.ts`。

通常情况下，ts会解析项目下的所有ts文件，此时也就有了相应的声明。（如果没有相应解析，可以检查下 `tsconfig.json` 中的 `files`、`include` 和 `exclude` 配置，确保其包含了 `jQuery.d.ts` 文件）



#### 第三方声明文件

对于模块导入的第三方库，可以如下导入：

```bash
npm install @types/jquery --save-dev
```



#### 书写声明文件

##### 全局声明

> 声明只能定义类型，不能包含有具体的实现。

* `declare var` 声明全局变量

    ```ts
    declare let jQuery: (selector: string) => any;
    ```

*  `declare function` 声明全局方法（支持重载）

    ````ts
    declare function jQuery (selector: string) => any;
    declare function jQuery (domReadyCallback: () => any): any;
    ````

*  `declare class` 声明全局类

    ```ts
    declare class Animal {
        name: string;
        constructor(name: string);
        getName(): string;
    }
    ```

    

* `declare enum` 声明全局枚举类型

    ```ts
    declare enum Directions {
        Up,
        Down,
        Left,
        Right
    }
    ```

    

* `declare namespace` 声明（含有子属性的）全局对象（可嵌套）

    ```ts
    declare namespace jQuery {
        function ajax(url: string, settings?: any): void;
        namespace fn {
            function extend(object: any): void;
        }
    }
    ```

* `interface` 和` type` 声明全局类型



##### npm包声明文件

* 跟npm包一起
* 单独发布 `@types/jQuery`

主要语法：跟ts语法一样，只是不能有具体的声明

* `export`  导出变量
* `export namespace` 导出有子属性的对象
* `export default` ES6默认导出
* `export = ` commonjs导出，eg：`modules.exports = {}` | `exports.foo = function() {}`

##### 拓展全局变量

比如通过 `interface String` 拓展 `String`：

````ts
interface String {
    prependHell(): string;
}
// 'foo'.prependHello();
````

通过 `declare namespace` 给已有的命名空间添加类型声明：

```ts
declare namespace jQuery {
    interface CustomOptions {
        bar: string;
    }
}
 
interface jQueryStatic {
    foo(options: jQuery.CustomOptions): string;
}
```



##### 在npm包或umd包中拓展全局变量

`declare global`。比如：

```ts
declare global {
    interface String {
        prependHello(): string;
    }
}

export {};
```



##### 拓展原有模块

`declare module`。比如：

```ts

import * as moment from 'moment';

declare module 'moment' {
    export function foo(): moment.CalendarKey;
}
```



##### 声明文件中的依赖

* `import`，如上
* 三斜线指令：`/// <reference types="jquery" />`

三斜线的其他用途：拆分声明文件，path（另一个文件）、types（另一个库）



##### 自动生成声明文件

* 命令行添加参数 `--declaration | -d`
* 配置文件添加编译参数：`declaration: true`

#### 发布声明文件

##### 跟源码发在一块

三种方式：

* `package.json` 中声明 `types`| `typings` 字段： `"types": "foo.d.ts"`
* 项目根目录下，编写一个 `index.d.ts`
* 针对入口文件（main字段指向的文件），编写一个同名不同后缀（`.d.ts`）的文件。

##### 发布到 @types



### 内置对象

typescript核心库中定义了浏览器环境所用到的所有类型，比如：

```typescript
Math.pow(2, 10);
```

但typescript核心库并不包含Nodejs部分。因此如果想要用 typescript书写nodejs，需要引入第三方文件：

```bash
npm install @types/node --save-dev
```





## 进阶



### 类型别名

用来给 **类型** 创建别名，比如：

```ts
type Name = string;
```

常用于 联合类型。

```ts
type UnionType = number | string;

const x = 'name';
```



### 字符串字面量类型

约束取值只能是 指定的字符串中的一个：

```ts
type EventName = 'click' | 'scroll' | 'mousemove';
function handleEvent (ele, Element, evnet: Eventname) {...}
```



### 元组

数组合并了相同类型的对象，元组（Tuple）合并不同的对象：

```ts
let tom: [string, number] = ['Tom', 25];
```

支持元素修改，但在整体赋值时需要提供所有指定的项目：

```ts
let tom: [string, number];
tom[0] = 'Tom';

tom = ['Tom']; //  Property '1' is missing in type '[string]' but required in type '[string, number]'.
```

添加越界元素时，会被限制为每个类型的联合类型，上述例子则会被限制为 `string | number`。



### 枚举

用于限定取值范围。比如一周只有七天：

```ts
enum Days { Sun, Mon, Tue, Wed, Thu, Fri, Sat };
```

`Days` 的枚举成员会被赋值为从 `0` 开始的数字，同时会进行反向映射。

```ts
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 0); // true
console.log(Days["Mon"] === 1); // true
console.log(Days["Tue"] === 2); // true
console.log(Days["Sat"] === 6); // true

console.log(Days[0] === "Sun"); // true
console.log(Days[1] === "Mon"); // true
console.log(Days[2] === "Tue"); // true
console.log(Days[6] === "Sat"); // true
```

其编译结果为：

```ts
var Days;
(function (Days) {
    Days[Days["Sun"] = 0] = "Sun";
    Days[Days["Mon"] = 1] = "Mon";
    Days[Days["Tue"] = 2] = "Tue";
    Days[Days["Wed"] = 3] = "Wed";
    Days[Days["Thu"] = 4] = "Thu";
    Days[Days["Fri"] = 5] = "Fri";
    Days[Days["Sat"] = 6] = "Sat";
})(Days || (Days = {}));
```



#### 手动赋值

```ts
enum Days {Sun = 7, Mon = 1, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 7); // true
console.log(Days["Mon"] === 1); // true
console.log(Days["Tue"] === 2); // true
console.log(Days["Sat"] === 6); // true
```

手动赋值在 覆盖的情况下，ts 察觉不出。因而需要注意。



#### 常数项和计算所得项

枚举项有两种类型：常数项（constant member）和计算所得项（computed member）。

```ts
enum Color {Red, Green, Blue = "blue".length};
```

上述例子是合法的，但如果blue这个计算所得项之后是未手动赋值的项，那么它们会因为无法获得初始值而报错。

常数项的完整定义：

* 不具有初始化函数，并且之前的成员均是常数；
* ts表达式的子集，可以在编译阶段求值：
  * 数字字面量
  * 引用之前的成员（可以是不同的枚举）
  * 。。。

#### 常数枚举

常数枚举是使用 `const enum` 定义的枚举类型。

跟普通枚举的区别是，它会在编译阶段删除，并且不能包含计算成员。

```ts
const enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
// var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```



#### 外部枚举

外部枚举是使用 `declare enum` 定义的枚举类型。

`declare` 定义的类型只会用于编译时的检查，在编译结果中会被删除。



### 类

#### ES6中类的用法

#### ES7中类的用法

#### ts中类的用法

* 修饰符
  * `public`:
  * `private`: 外部不能访问，包括子类。
  * `protected`: 
    * 外部不能访问，子类可以。
    * 当修饰 `constructor` 时，表明该类只能被继承，不能被实例化。
* 参数属性：修饰符 和 readonly 可以使用在构造函数参数中，等同于定义属性的同时赋值。
* `readonly`
* 抽象类：abstract class`
  * 不能实例化
  * 抽象方法必须被字类实现

#### 类的类型

给类加上类型与接口类似：

```ts
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  sayHi (): string {
    return `My name is ${this.name}`;
	}
}

let a: Animal = new Animal('tom');
console.log(a.sayHi());
```



### 类与接口

#### 类实现接口

类可以实现多个接口：

```ts
interface Alarm {
  alert(): void;
}
interface Light {
  lightOn(): void;
  lightOff(): void;
}
class Car implements Alarm, Light {
  ...
}
```



#### 接口继承接口

```ts
interface LightableAlarm extends Alarm {
  lightOn(): void;
  lightOff(): void;
}
```



#### 接口继承类

> 常见的面向对象的语言中，接口不能继承类。

但ts可以：实际上，ts中创建类的同时，也创建了一个**同名的类型**。因此，既可以使用 `new` 实例化，也可以作为一个类型的使用。



### 泛型

>  泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

```ts
function createArray<T> (length: number, value: T): Array<T> {
  const res: T[] = [];
  for (let i = 0; i < length; i++) {
    res[i] = value;
  }
  return res;
}

createArray<string>(3, 'x');
```

在函数名后添加 `<T>` 指代任意类型，在后续的输入value 和 输出中使用。

接着，在调用的时候指定类型。也可以不指定，使用类型推断。



#### 多个类型参数

```ts
function swap<T, U>(tuple: [T, U]): [U, T] {
	return [tuple[1], tuple[0]];
}

swap([7, 'str']);
```



#### 泛型约束

在函数内部使用泛型时，由于不知道它的具体类型，因此不能随便使用它的属性和方法，此时可以对它进行约束。

```ts
interface Lengthwise {
  length: number;
}
function loggingIdentity<T extends Lengthwise>(args: T): T {
  console.log(args.length);
  return arg;
}
```

多类型间相互约束：

```ts
function copyFields<T extends U, U> (target: T, source: U): T {
  for (let id in source) {
    target[id] = source[id];
  }
  return target;
}

let target = { a: 1, b: 2, c: 3, d: 4};
let source = { b: 10, c: 20 };
copyFields(target, source);
```

这里要求 T 继承 U，可以保证 U上不会出现 T 不存在的字段。



#### 泛型接口



#### 泛型类

```ts
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}

const genericNumber = new GenericeNumber<number>();
```



#### 泛型参数的默认类型

```ts
function createArra<T = string> (length: number, value: T): Array<T> {
  const res: T[] = [];
  for (let i = 0; i < length; i++) {
    res[i] = value;
  }
  return res;
}
```



### 声明合并

> 定义了相同名字的函数、接口 和 类，会合并成一个类型。

#### 函数的合并

重载。

```ts
function reverse(x: number): number;
function reverse(x: number): string;
function reverse(x: number | string);
```



#### 接口的合并

属性合并。

属性的类型需要唯一：类型一致不会报错。



#### 类的合并

同接口一致。



## 工程

### 代码检查

* ts - 类型检查
* eslint - 风格检查
* prettier - 格式修复

### 编译选项

#### allowJs

允许编译js文件，ts/js混合开发有用。



