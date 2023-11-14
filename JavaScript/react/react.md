# react

# hello world

## 在线编辑

https://codepen.io/gaearon/pen/oWWQNa?editors=0010

## nodejs 单页面环境

1. 安装 node js
2. `npx create-react-app my-app`,npx 为 npm 5.2+ 携带的 package 运行工具,create-react-app 内部使用 babel 和 webpack
3. `cd my-app`
4. `npm start`

## script

```html
<!-- 1.根标签 -->
<div id="like_button_container"></div>

<!-- Load React. -->
<!-- Note: when deploying, replace "development.js" with "production.min.js". -->
<!-- 2.react -->
<script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
<!-- react-demo -->
<script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
<!-- 使用 jsx -->
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
<!-- 生产环境压缩的代码 -->
<script src="https://unpkg.com/react@16/umd/react.production.min.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js" crossorigin></script>
<!-- 3.加载react组件-->
<script src="like_button.js"></script>

<!-- jsx 使用 -->
<div id="root"></div>
<script type="text/babel">
	function MyApp() {
		return <h1>Hello, world!</h1>
	}

	const container = document.getElementById("root")
	const root = ReactDOM.createRoot(container)
	root.render(<MyApp />)
</script>
```

```javascript
//like_button.js

"use strict"

// 创建react组件
const e = React.createElement
class LikeButton extends React.Component {
	constructor(props) {
		super(props)
		this.state = { liked: false }
	}

	render() {
		if (this.state.liked) {
			return "You liked this."
		}

		return e("button", { onClick: () => this.setState({ liked: true }) }, "Like")
	}
}
// 组件渲染
const domContainer = document.querySelector("#like_button_container")
ReactDOM.render(e(LikeButton), domContainer)
```

## 常用扩展

Next.js 是一个流行的、轻量级的框架，用于配合 React 打造静态化和服务端渲染应用。它包括开箱即用的样式和路由方案，并且假定你使用 Node.js 作为服务器环境。

Gatsby 是用 React 创建静态网站的最佳方式。它让你能使用 React 组件，但输出预渲染的 HTML 和 CSS 以保证最快的加载速度。

Neutrino 把 webpack 的强大功能和简单预设结合在一起。并且包括了 React 应用和 React 组件的预设。

Parcel 是一个快速的、零配置的网页应用打包器，并且可以搭配 React 一起工作。

Razzle 是一个无需配置的服务端渲染框架，但它提供了比 Next.js 更多的灵活性。

```javascript
//它将在页面上展示一个 “Hello, world!” 的标题。
ReactDOM.render(<h1>Hello, world!</h1>, document.getElementById("root"))
```

# JSX

```javascript
const element = <h1>Hello, world!</h1>
```

这个有趣的标签语法既不是字符串也不是 HTML,它被称为 JSX，是一个 JavaScript 的语法扩展.

我们建议在 React 中配合使用 JSX,JSX 可以很好地描述 UI 应该呈现出它应有交互的本质形式.

React 认为渲染逻辑本质上与其他 UI 逻辑内在耦合,比如,在 UI 中需要绑定处理事件,在某些时刻状态发生变化时需要通知到 UI,以及需要在 UI 中展示准备好的数据.React 并没有采用将标记与逻辑进行分离到不同文件这种人为地分离方式，而是通过将二者共同存放在称之为“组件”的松散耦合单元之中，来实现关注点分离.

React 不强制要求使用 JSX，但是大多数人发现，在 JavaScript 代码中将 JSX 和 UI 放在一起时，会在视觉上有辅助作用。它还可以使 React 显示更多有用的错误和警告消息。

1. **注入表达式**,语法: `{js表达式}`,可以在标签体和属性值上使用
2. **JSX 也是一个表达式**,可以作为返回值使用
3. **属性**可以使用`""`或`{}`
4. JSX 语法上更接近 JavaScript 而不是 HTML，所以 React DOM 使用 **camelCase**（小驼峰命名）来定义**属性**的名称
5. 可以使用**单标签**
6. 可以**嵌套子元素**
7. JSX 可以**防止注入攻击**,React DOM 在渲染所有输入内容之前，默认会进行转义。它可以确保在你的应用中，永远不会注入那些并非自己明确编写的内容。所有的内容在渲染之前都被转换成了字符串。这样可以有效地防止 XSS（cross-site-scripting, 跨站脚本）攻击。
8. Babel 会把 JSX 转译成一个名为 `React.createElement()`函数调用。

```js
//声明了一个名为 name 的变量
const name = "Josh Perez"
// 在 JSX 中使用它,并将它包裹在大括号中
const element = <h1>Hello, {name}</h1>

ReactDOM.render(element, document.getElementById("root"))

function getGreeting(user) {
	if (user) {
		return <h1>Hello, {formatName(user)}!</h1>
	}
	return <h1>Hello, Stranger.</h1>
}

// 1. 使用""
const element = <div tabIndex="0"></div>
// 2. 使用{}
const element = <img src={user.avatarUrl}></img>

// 单标签
const element = <img src={user.avatarUrl} />
//可以嵌套子元素
const element = (
	<div>
		<h1>Hello!</h1>
		<h2>Good to see you here.</h2>
	</div>
)

const element = <h1 className="greeting">Hello, world!</h1>
// 等效于
const element = React.createElement("h1", { className: "greeting" }, "Hello, world!")

// React.createElement() 会预先执行一些检查，以帮助你编写无错代码，但实际上它创建了一个这样的对象：// 注意：这是简化过的结构
const element = {
	type: "h1",
	props: {
		className: "greeting",
		children: "Hello, world!",
	},
}
// 这些对象被称为 “React 元素”。它们描述了你希望在屏幕上看到的内容。React 通过读取这些对象，然后使用它们来构建 DOM 以及保持随时更新。
```

# 元素渲染

1. 元素是构成 React 应用的最小砖块,描述了你在屏幕上想看到的内容,与浏览器的 DOM 元素不同,React 元素是创建开销极小的普通对象,React DOM 会负责更新 DOM 来与 React 元素保持一致.
2. React 元素是不可变对象。一旦被创建，你就无法更改它的子元素或者属性,它代表了某个特定时刻的 UI,更新 UI 唯一的方式是创建一个全新的元素,并将其传入` ReactDOM.render()`

```javascript
const element = <h1>Hello, world</h1>
ReactDOM.render(element, document.getElementById("root"))
```

# 组件 & Props

组件,从概念上类似于 JavaScript 函数.它接受任意的入参（即 “props”）,并返回用于描述页面展示内容的 React 元素.

1. 组件名称必须以大写字母开头
2. 函数式
3. es6 class
4. 通常来说,每个新的 React 应用程序的顶层组件都是 App 组件。
5. props 只读,不能也禁止修改

## 组件定义

```javascript
function Welcome(props) {
	return <h1>Hello, {props.name}</h1>
}
// 或 es6 的class
/**
    1. 创建一个同名的 ES6 class,并且继承于 `React.Component`
    2. 添加一个空的 `render()` 方法
    3. 将函数体移动到 `render()` 方法之中
    4. 在 `render()` 方法中使用 `this.props` 替换 `props`
    5. 删除剩余的空函数声明
*/
class Welcome extends React.Component {
	render() {
		return <h1>Hello, {this.props.name}</h1>
	}
}

ReactDOM.render(<Welcome name="lxh" />, document.getElementById("root"))
```

## 组件渲染

```javascript
// React 元素也可以是用户自定义的组件
const element = <Welcome name="Sara" />
```

当 React 元素为用户自定义组件时,它会将 JSX 所接收的属性（attributes）以及子组件（children）转换为单个对象传递给组件,这个对象被称之为 “props”

```js
function Welcome(props) {
	return <h1>Hello, {props.name}</h1>
}
// props : {name: 'Sara'}
const element = <Welcome name="Sara" />
ReactDOM.render(element, document.getElementById("root"))
```

## 组件嵌套

```javascript
function Welcome(props) {
	return <h1>Hello, {props.name}</h1>
}

function App() {
	return (
		<div>
			<Welcome name="Sara" />
			<Welcome name="Cahal" />
			<Welcome name="Edite" />
		</div>
	)
}

ReactDOM.render(<App />, document.getElementById("root"))
```

# State & 生命周期

## state 使用 class 创建|更新元素

更新元素的方法

1. 再次调用`ReactDOM.render()`
2. 组件中添加 `state`,state 改变时会重新调用`render()`函数
    1. 只能在用 class 创建的 react 函数中使用
    2. 只由通过调用`this.setState({})`函数才能触发重新渲染,当你调用 setState() 的时候,React 会把你提供的对象合并到当前的 state,可以只更新单个元素
    3. 构造函数是唯一可以给 `this.state` 赋值的地方
    ```javascript
    constructor(props) {
		super(props)
		this.state = { date: new Date() }
	}
    ```
    4. 出于性能考虑，React 可能会把多个 `setState()` 调用合并成一个调用,因为 this.props 和 this.state 可能会异步更新,所以不要依赖他们的值来更新下一个状态,要解决这个问题，可以让 `setState()` 接收一个**函数**而不是一个对象。这个函数用上一个 state 作为第一个参数，将此次更新被应用时的 props 做为第二个参数
    ```javascript
    this.setState((state, props) => ({
      counter: state.counter + props.increment
    }));
    ```

使用

```javascript
function tick() {
	const element = (
		<div>
			<h1>Hello, world!</h1>
			<h2>It is {new Date().toLocaleTimeString()}.</h2>
		</div>
	)
	ReactDOM.render(element, document.getElementById("root"))
}

setInterval(tick, 1000)

// 改造组件

function Clock(props) {
	return (
		<div>
			<h1>Hello, world!</h1>
			<h2>It is {props.date.toLocaleTimeString()}.</h2>
		</div>
	)
}

function tick() {
	ReactDOM.render(<Clock date={new Date()} />, document.getElementById("root"))
}

setInterval(tick, 1000)

// 方法二

class Clock extends React.Component {
	constructor(props) {
		super(props)
		this.state = { date: new Date() } //同样之计算一次,但是可以进行修改,且每次修改都回更新页面
	}

	render() {
		return (
			<div>
				<h1>Hello, world!</h1>
				<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
			</div>
		)
	}
}

ReactDOM.render(<Clock />, document.getElementById("root"))
```

## 生命周期

1. `componentDidMount()` 方法会在组件已经被渲染到 DOM 中后运行
2. `componentWillUnmount()`方法在组件从 DOM 中被移除后调用

```javascript
class Clock extends React.Component {
	constructor(props) {
		super(props)
		this.state = { date: new Date() }
	}

	componentDidMount() {
		this.timerID = setInterval(() => this.tick(), 1000)
	}

	componentWillUnmount() {
		clearInterval(this.timerID)
	}

	tick() {
		this.setState({
			date: new Date(),
		})
	}

	render() {
		return (
			<div>
				<h1>Hello, world!</h1>
				<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
			</div>
		)
	}
}

ReactDOM.render(<Clock />, document.getElementById("root"))
```
# 事件处理

1. React 事件的命名采用**小驼峰式camelCase**,而不是纯小写
2. 使用 JSX 语法时你需要传入一个**函数**作为事件处理函数,而不是一个字符串
3. 在 React 中另一个不同点是你不能通过返回 false 的方式阻止默认行为。你必须显式的使用 `preventDefault`
   ```javascript
   function ActionLink() {
      function handleClick(e) {
        e.preventDefault();
        console.log('The link was clicked.');
      }
    
      return (
        <a href="#" onClick={handleClick}>
          Click me
        </a>
      );
    }
   ```
4. 使用 React 时，你一般不需要使用 `addEventListener` 为已创建的 DOM 元素添加监听器。事实上，你只需要在该元素初始渲染的时候添加监听器即可。
5. 当你使用 ES6 class 语法定义一个组件的时候，通常的做法是将事件处理函数声明为 class 中的方法。
   ```javascript
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
    
    ReactDOM.render(
      <Toggle />,
      document.getElementById('root')
    );
    
    // es6 实验性,Create React App 默认启用此语法。
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
    // 如果你没有使用 class fields 语法，你可以在回调中使用箭头函数,
    // 此语法问题在于每次渲染 LoggingButton 时都会创建不同的回调函数。在大多数情况下，这没什么问题，但如果该回调函数作为 prop 传入子组件时，这些组件可能会进行额外的重新渲染。我们通常建议在构造器中绑定或使用 class fields 语法来避免这类性能问题。
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
6. 传递参数
    ```javascript
    <button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
    <button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
    ```
# 条件渲染
在 React 中，你可以创建不同的组件来封装各种你需要的行为。然后，依据应用的不同状态，你可以只渲染对应状态下的部分内容。
1. &&
2. ?:
3. `return null;` 不渲染
```javascript
function UserGreeting(props) {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}
// 根据 isLoggedIn 的值来渲染不同的问候语
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

ReactDOM.render(
  // Try changing to isLoggedIn={true}:
  <Greeting isLoggedIn={false} />,
  document.getElementById('root')
);

// 逻辑与 (&&) 运算符。它可以很方便地进行元素的条件渲染
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

// ?:
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
//在极少数情况下，你可能希望能隐藏组件，即使它已经被其他组件渲染。若要完成此操作，你可以让 render 方法直接返回 null，而不进行任何渲染。

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
```
# 列表 & Key

1. 使用`map()`
```javascript
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);
```
2. 给每个列表元素分配一个 `key` 属性,数组元素中使用的 key 在其兄弟节点之间应该是独一无二的
```javascript
// 重构1的例子,会有一个警告,a key should be provided for list items,意思是当你创建一个元素时，必须包括一个特殊的 key 属性
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>//警告
    <li key={number.toString()}>
        {number}
    </li>// 增加key,解决警告
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
3. JSX 允许在大括号中嵌入任何表达式，所以我们可以内联 map() 返回的结果
```javascript
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()} value={number} />
      )}
    </ul>
  );
}
```
# 表单
在 React 里，HTML 表单元素的工作方式和其他的 DOM 元素有些不同，这是因为表单元素通常会保持一些内部的 state。例如这个纯 HTML 表单只接受一个名称：
```javascript
<form>
  <label>
    名字:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="提交" />
</form>
```
此表单具有默认的 HTML 表单行为，即在用户提交表单后浏览到新页面。如果你在 React 中执行相同的代码，它依然有效。但大多数情况下，使用 JavaScript 函数可以很方便的处理表单的提交， 同时还可以访问用户填写的表单数据。实现这种效果的标准方式是使用“受控组件”。
## 受控组件
1. form
2. input,`value={this.state.value} onChange={this.handleChange}`,当需要处理多个 input 元素时，我们可以给每个元素添加 name 属性，并让处理函数根据 event.target.name 的值选择要执行的操作。
3. textarea,与input同
4. select-option,。React 并不会使用 selected 属性，而是在根 select 标签上使用 value 属性

```javascript
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('提交的名字: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          名字:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <label>
          文章:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <label>
          选择你喜欢的风味:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">葡萄柚</option>
            <option value="lime">酸橙</option>
            <option value="coconut">椰子</option>
            <option value="mango">芒果</option>
          </select>
        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}
```
## 非受控组件
1. 文件,`<input type="file" />`,value 只读,