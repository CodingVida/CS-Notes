[TOC]

---



## 核心概念

### JSX

* 嵌入表达式：`{}`

    ```jsx
    const name = 'yoto';
    const element = <div>Hello, { name }!</div>
    ```

    大括号内可以嵌入任何有效的JavaScript表达式。

    当JSX拆分为多行时，建议使用括号包裹，这样可以避免 **自动插入分号陷阱**。

    

* 本身也是表达式：作为参数、返回值等

    

* 特定属性

    使用引号将属性值指定为字符串字面量 或 使用大括号插入一个 js表达式。

    **warning**：React DOM 使用 `camelCase`（小驼峰命名）来定义属性的名称，而不使用 HTML 属性名称的命名约定。比如 `class`变为`className`，`tabindex` 则变为 `tabIndex`。

    

* 自闭合、子元素

    

* 防止注入攻击

    jsx会默认进行 **转义**，有效防止xss。

    ```none
    & becomes &amp;
    < becomes &lt;
    > becomes &gt;
    ```

* JSX表示一个对象

    ```react
    const element = (
    	<h1 className="greeting">
        	hello world!
        </h1>
    );
    // equal to
    const element = React.createElement(
        'h1',
        { className: 'greeting' },
        'Hello, world!'
    );
    
    // React.createElement() 会预先执行一些检查，以帮助你编写无错代码，但实际上它创建了一个这样的对象：
    // 注意：这是简化过的结构
    const element = {
        type: 'h1',
        props: {
            className: 'greeting',
            children: 'Hello, world!'
        }
    };
    ```

    

### 元素渲染

> 元素是构成 React 应用的最小砖块。

* 元素描述了你在屏幕上想看到的内容：`const element = <h1>Hello, world</h1>;`

* 与浏览器的 DOM 元素不同，React 元素是创建开销极小的普通对象。React DOM 会负责更新 DOM 来与 React 元素保持一致。
* React元素是 **不可变对象**。



### 组件 & Props

> 组件，从概念上类似于 JavaScript 函数。它接受任意的入参（即 “props”），并返回用于描述页面展示内容的 React 元素。

#### 函数组件和class组件

接收props对象，并返回react元素的js函数，称为函数组件。

```react
function Welcome (props) {
    return <h1>hello, { props.name }</h1>
}
```

使用ES6的class定义组件：

```react
class Welcome extends React.Component {
    render () {
        return <h1>hello, { this.props.name }</h1>
    }
}
// 如果有调用 constructor，应该始终使用 props参数 调用父类的构造函数。
```

这两种组件在react中是 **等价的**。



#### 渲染组件

自定义组件。

**组件名称必须大写字母开头**。



#### 组合组件

```react
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}
```



#### 提取组件

将组件拆分为更小的组件

> 从组件自身的角度命名 props，而不是依赖于调用组件的上下文命名。



#### Props的只读性

> 组件无论是使用**函数声明**还是通过 **class 声明**，都决不能修改自身的 props。

纯函数：不会尝试更改入参，且多次调用下相同的入参始终返回相同的结果。比如：

```js
function sum (a, b) {
    return a + b;
}
```

**所有 React 组件都必须像纯函数一样保护它们的 props 不被更改。**



### State & 生命周期

> state与props类似，但state是私有的，并且受控于当前组件。

#### 正确使用state

* 不要直接修改state

    * `this.state.comment = 'Hello';` // wrong, 如此不会重新渲染组件

    * 使用 `this.setState`

    * 构造函数是唯一可以给 `this.state` 赋值的地方。

        

* state的更新可能是异步的

    `this.state`、`this.props` 的更新可能是异步的，因此不能依赖它们的值来更新下一个状态，比如：

    ```react
    // wrong
    this.setState({
        counter: this.state.counter + this.props.increment
    });
    ```

    要解决这个问题，可以给 `this.setState` 传入一个函数：

    ```react
    this.setState((state, props) => ({
        counter: state.counter + props.increment
    }));
    // 上一个 state 作为第一个参数，此次更新被应用时的 props 做为第二个参数
    ```

    

* state的更新会被合并

    浅合并。



#### 数据是向下流动的

组件可以选择把它的 state 作为 props 向下传递到它的子组件中：

```react
<FormattedDate date={this.state.date} />
```

`FormattedDate` 组件会在其 props 中接收参数 `date`，但是组件本身无法知道它是来自于 `Clock` 的 state，或是 `Clock` 的 props，还是手动输入的：

```react
function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}
```

任何的 state 总是所属于特定的组件，而且从该 state 派生的任何数据或 UI 只能影响树中“低于”它们的组件。

这样就形成被叫做“自上而下”或是“单向”的数据流。



### 事件处理



#### 与DOM元素事件的区别

React元素的事件处理和DOM元素的很像，但在语法上有一些不同：

* react使用小驼峰（camelCase），而不是纯小写

* 使用jsx语法时，需要传入一个函数，而不是字符串

    ```react
    // html：onclick
    <button onclick="activateLasers()">Activate Lasers</button>
    
    // react：onClick
    <button onClick={ activateLasers }>Activate Lasers</button>
    ```

* react中阻止默认行为不能通过返回`false`的方法，而必须显式地使用 `e.preventDefault()`。

    其中 `e` 是一个合成事件。



#### this指向

需要注意JSX回调函数中的 `this` 指向，一般情况下会使用 `bind()` 方法在 `constructor` 中绑定this：

```react
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // 为了在回调中使用 `this`，这个绑定是必不可少的
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

```



除此之外，还可：

* 实验性质的 `public class fields` 语法（create react app 默认启用）：

    ```react
    class LoggingButton extends React.Component {
      // 此语法确保 `handleClick` 内的 `this` 已被绑定。
      // 注意: 这是 *实验性* 语法。
      handleClick = () => {
        console.log('this is:', this);
      }
    
      render() {
        return (
          <button onClick={this.handleClick}>
            Click me
          </button>
        );
      }
    }
    ```

* 箭头函数

    ```react
    class LoggingButton extends React.Component {
      handleClick() {
        console.log('this is:', this);
      }
    
      render() {
        // 此语法确保 `handleClick` 内的 `this` 已被绑定。
        return (
          <button onClick={() => this.handleClick()}>
            Click me
          </button>
        );
      }
    }
    ```

    这种方法的问题在于，每次渲染组件都会创建不同的回调函数。如果作为props传入子组件，那么子组件可能会进行额外的渲染。



#### 向事件处理程序传递参数

```react
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

上述两种方式是等价的，分别通过[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)和 [`Function.prototype.bind`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind) 来实现。

在这两种情况下，React 的事件对象 `e` 会被作为第二个参数传递。如果通过箭头函数的方式，事件对象必须显式的进行传递，而通过 `bind` 的方式，事件对象以及更多的参数将会被隐式的进行传递



### 条件渲染

React 中的条件渲染和 JavaScript 中的一样，使用 JavaScript 运算符 [`if`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) 或者[条件运算符](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)去创建元素来表现当前的状态，然后让 React 根据它们来更新 UI。



#### 元素变量

```react
if (isLoggedIn) {
    button = <LogoutButton onClick={this.handleLogoutClick} />;
} else {
    button = <LoginButton onClick={this.handleLoginClick} />;
}
```



#### 与运算符 &&

```react
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}
```

在 JavaScript 中，`true && expression` 总是会返回 `expression`, 而 `false && expression` 总是会返回 `false`。

需要注意的是，返回 false 的表达式会使 `&&` 后面的元素被跳过，但会返回 false 表达式。比如：

```react
render() {
  const count = 0;
  return (
    <div>
      { count && <h1>Messages: {count}</h1> }
    </div>
  );
}

// render 的结果为 <div>0</div>
// 需要区分的是 false, null, undefined, and true 是合法的子元素, 但它们并不会被渲染。
```



#### 三目运算符

```react
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn
        ? <LogoutButton onClick={this.handleLogoutClick} />
        : <LoginButton onClick={this.handleLoginClick} />
      }
    </div>
  );
}
```



#### 阻止组件渲染

在极少数情况下可能希望能隐藏组件，即使它已经被其他组件渲染。若要完成此操作，可以让 `render` 方法直接返回 `null`，而不进行任何渲染。

````react
function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }

  return (
    <div className="warning">
      Warning!
    </div>
  );
}
````



### 列表 & Key

#### 渲染多个组件

使用 `{}` 在 JSX内构建一个集合。

```react
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```



#### Key

key 帮助 React 识别哪些元素改变了，比如被添加或删除。

* 元素的 key 只有放在就近的数组上下文中才有意义。

    比方说，如果你[提取](https://zh-hans.reactjs.org/docs/components-and-props.html#extracting-components)出一个 `ListItem` 组件，你应该把 key 保留在数组中的这个 `<ListItem />` 元素上，而不是放在 `ListItem` 组件中的 `<li>` 元素上。

    ```react
    function ListItem(props) {
      // 正确！这里不需要指定 key：
      return <li>{props.value}</li>;
    }
    
    function NumberList(props) {
      const numbers = props.numbers;
      const listItems = numbers.map((number) =>
        // 正确！key 应该在数组的上下文中被指定
        <ListItem key={number.toString()} value={number} />
      );
      return (
        <ul>
          {listItems}
        </ul>
      );
    }
    ```

    

* key只是在兄弟节点之间必须唯一

    数组元素中使用的 key 在其兄弟节点之间应该是独一无二的。然而，它们不需要是全局唯一的。当我们生成两个不同的数组时，我们可以使用相同的 key 值。

* key 会传递信息给 React ，但不会传递给组件。也就是说没有 `this.props.key`的获取方式。



### 表单

#### 受控组件

渲染表单的React组件控制着，用户输入过程中表单发生的操作。



#### textarea

HTML中，其文本通过子元素定义；

React中，同input一致，通过value定义；



#### select

React不使用 `selected` 属性，而是在根select标签上使用value属性。



#### 文件input

`<input type="file" />`在React中始终是 **非受控组件**，需要通过 `File API` 进行交互。



#### 多个输入

给 `input` 添加 `name` 属性，然后通过 `event.target.name` 的值选择要执行的操作。



#### 受控输入空值

在[受控组件](https://zh-hans.reactjs.org/docs/forms.html#controlled-components)上指定 value 的 prop 会**阻止用户更改输入**。

如果指定了 `value`，但输入仍可编辑，那么有可能是意外地将`value` 设置为 `undefined` 或 `null`。



### 状态提升

> 通常，多个组件需要反映相同的变化数据，这时我们建议将共享状态提升到最近的共同父组件中去。



### 组合 vs 继承

> React 有十分强大的组合模式。我们推荐使用组合而非继承来实现组件间的代码重用。

#### 包含关系

将子组件传递到渲染结果中：

* 使用 `props.children`

    ```react
    function FancyBorder(props) {
      return (
        <div className={'FancyBorder FancyBorder-' + props.color}>
          {props.children}
        </div>
      );
    }
    // 别的组件可以通过 JSX 嵌套，将任意组件作为子组件传递给它们。
    function WelcomeDialog() {
      return (
        <FancyBorder color="blue">
          <h1 className="Dialog-title">
            Welcome
          </h1>
          <p className="Dialog-message">
            Thank you for visiting our spacecraft!
          </p>
        </FancyBorder>
      );
    }
    ```

    

* 自行约定：

    ```react
    export default function SplitPane (props) {
        return (
            <div>
                <div>
                    left: { props.left }
                </div>
                <div>
                    right: { props.right }
                </div>
            </div>
        )
    }
    
    <SplitPane
        left={ <PaneItem name="left" /> }
        right={ <PaneItem name="right" /> }
     />
    ```

    > 有点类似“slot“。但要更为灵活。

    

#### 特例关系

有些时候，我们会把一些组件看作是其他组件的特殊实例，比如 `WelcomeDialog` 可以说是 `Dialog` 的特殊实例。

在 React 中，我们也可以通过组合来实现这一点。“特殊”组件可以通过 props 定制并渲染“一般”组件：

```react
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
    </FancyBorder>
  );
}

function WelcomeDialog() {
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visiting our spacecraft!" />
  );
}
```



### React哲学

> React 最棒的部分之一是引导我们思考如何构建一个应用

* 第一步：将设计好的UI划分为组件层级
* 第二步：用React创建一个静态版本
* 第三步：确定UI State 的最小且完整表示
* 第四步：确定state的放置位置
* 第五步：添加反向数据流



## 高级指引



### 无障碍



### 代码分割

#### import()

#### React.lazy

> 配合组件 `Suspense` 做优雅降级。

```jsx
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherCompoent'));

function MyComponent () {
	return (
    	<div>
        	<Suspense fallback={ <div>Loading......</div> }>
            	<OtherComponent />
            </Suspense>
        </div>
    );
}
```

#### 基于路由的代码分割

#### 命名导出

`React.lazy` 目前只支持默认导出（default export）。

可以创建一个中间模块，来重新导出为默认模块。这能保证 tree shaking 不会出错，并且不必引入不需要的组件。



### Context

> 组件间数组传递。

* `React.createContext`

    ```jsx
    const MyContext = React.createContext(defaultValue);
    ```

    **只有 **当组件所处的树中没有匹配到 Provider 时，其 `defaultValue` 参数才会生效。

* `Context.Provider`

    ```jsx
    <MyContext.Provider value={  } />
    ```

    * Provider 接收一个 `value` 属性，传递给消费组件。一个 Provider 可以和多个消费组件有对应关系。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。
    * 当 Provider 的 `value` 值发生变化时，它内部的所有消费组件都会重新渲染。(变化比较是通过 `Object.is` 进行的)。

* `Class.contextType`

    ```jsx
    class MyClass extends React.Component {
       componentDidMount() {
         let value = this.context;
         /* 在组件挂载完成后，使用 MyContext 组件的值来执行一些有副作用的操作 */
       }
    }
    MyClass.contextType = MyContext;
    
    // or:
    class MyClass extends React.Component {
      static contextType = MyContext;
      render() {
        let value = this.context;
        /* 基于这个值进行渲染工作 */
      }
    }
    
    ```

* `Context.Consumer`

    ```jsx
    <MyContext.Consumer>
        { value }
    </MyContext.Consumer>
    ```

    一个 React 组件可以订阅 context 的变更，这让你在[函数式组件](https://zh-hans.reactjs.org/docs/components-and-props.html#function-and-class-components)中可以订阅 context。

* `Context.displayName`

    for dev tools.



### Refs转发

#### 转发 refs 到 DOM组件



####  在高阶组件中转发refs



### Fragment

> React 中的一个常见模式是一个组件返回多个元素。Fragments 允许你将子列表分组，而无需向 DOM 添加额外节点。

用法：

```jsx
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}

// OR: 短语法
class Columns extends React.Component {
  render() {
    return (
      <>
        <td>Hello</td>
        <td>World</td>
      </>
    );
  }
}
```

> `key` 是目前唯一可以传递给 `Fragment` 的属性。

```jsx
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // 没有`key`，React 会发出一个关键警告
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```



### 高阶组件

> 高阶组件（HOC）是 React 中用于复用组件逻辑的一种高级技巧。HOC 自身不是 React API 的一部分，它是一种基于 React 的组合特性而形成的设计模式。

参数为组件，返回一个新组件的 **函数**。



### 深入JSX

> JSX 仅仅只是 `React.createElement(component, props, ...children)` 函数的语法糖。

#### 指定React元素类型

* React必须在作用域内，即便没有被直接使用。

  ```jsx
  import React from 'react';
  
  function WarningButton () {
    return <CustomButton color="red" />;
  }
  ```

* jsx类型中使用点语法：比如一个模块导出多个组件；

* 自定义组件大写字母开头

* 运行时选择类型： 需要首先将类型赋值给一个大写字母开头的变量。

#### props

* 表达式作为props
* 字符串变量
* 默认值为true：`<MyTextBox autocomplete />``
* 属性展开：`<Greeting {...props} />;`

#### JSX中的子元素

> 包含在开始标签和结束标签之间的JSX表达式内容将作为特定属性 `props.children`传递给外层组建。

* 字符串字变量
* jsx子元素
* js表达式
* 函数
* 布尔类型、Null 以及 Undefined 将会忽略

### Portals

> Portal 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的优秀的方案。

```jsx
render () {
  return ReactDOM.createPortal(
  	this.props.children,
    domNode
  );
}
```



### Refs & DOM

> Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素。	

#### 何时使用

* 管理焦点，文本选择或媒体播放。
* 触发强制动画。
* 继承第三方dom库

#### 创建Refs

`React.createRef()`

```jsx
class MyComponent extends React.Component {
  constructor (props) {
    this.myRef = React.createRef();
  }
  render () {
    return <div ref={ this.myRef } />;
  }
}
```

#### 访问Refs

```jsx
const node = this.myRef.current;
```

ref 的值根据节点类型的不同而有所不同：

* html元素，ref接受底层DOM元素作为 `current`属性；
* 自定义class组件，ref接收挂载实例作为 `current`属性；
* 默认情况下，函数式组件上不能使用 ref属性（因为它们没有实例）。

#### 将Ref暴露给父组件

#### 回调Refs

### Render Props

> 一种在 React 组件之间使用一个值为函数的 prop 共享代码的简单技术。
>
> 代码复用。

```tsx
<DataProvider render={data => (
	<h1>hello, {data.target}</h1>
)}/>
```





## HOOK

> 它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。



### 概览

**定义：**

`Hook` 是一些可以在函数组件里“**钩入**” React State 及生命周期等特性的**特殊函数**。`Hook` 不能在 class 组件中使用 —— 这使得你不使用 class 也能使用 React。

**动机：**

* 组件间复用逻辑
* 复杂组件逻辑混乱（比如订阅与取消订阅的逻辑分散到不同的生命周期中）
* 难以理解的class



**内置hook：**

* `useState`

* `useEffect`

    

### useState

像 class 中的 `this.setState`，更新 state 变量总是 **替换** 它而不是合并它。

因此在更新对象时需要留意。



### useEffect

> 在 React 组件中执行数据获取、订阅或者手动修改 DOM等的这些操作称为“**副作用**”，或者简称为“**作用**”。

`useEffect` 就是一个 Effect Hook，它给函数增加了操作副作用的能力，跟class组件中的 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount`具有相同的用途，只是被合并为一个API。

当调用 `useEffect` 时，就是在告诉 React **在完成对 DOM 的更改后运行“副作用”函数**。由于副作用函数是在组件内声明的，所以它们可以访问到组件的 props 和 state。默认情况下，React 会在每次渲染后调用副作用函数 —— **包括**第一次渲染的时候。

````react
 // 相当于 componentDidMount 和 componentDidUpdate:
useEffect(() => {
    // 使用浏览器的 API 更新页面标题
    document.title = `You clicked ${count} times`;
});
````

同时，副作用函数可以通过返回一个函数，指定如何“清除”副作用（它在调用一个新的 effect 之前对前一个 effect 进行清理）。比如：

```react
useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
});
// 通过副作用函数，可以把组件内相关的副作用组织在一起（例如创建订阅及取消订阅），而不要把它们拆分到不同的生命周期函数里。
```



* 为什么每次更新的时候都要运行 Effect：“清除”副作用。
* 通过跳过 Effect 进行性能优化：指定第二个参数。



### HOOK规则

* 只在最顶层使用

    不在条件、循环或嵌套函数中使用

* 只在react函数中使用

`eslint-plugin-react-hooks`

```
 React 怎么知道哪个 state 对应哪个 useState？答案是 React 靠的是 Hook 调用的顺序。
```



### 自定义hook

> 自定义hook是一个以 `use` 开头的函数，其内部可以调用其他的hook。

两个组件之间使用同一个hook只是状态逻辑的重用，不会共享state。



