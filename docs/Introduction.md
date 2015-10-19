# 简介

React Router 是一个基于 [React](http://facebook.github.io/react/) 之上的强大路由库，为了帮助你快速地添加视图和数据流到你的应用中，同时保持页面与 URL 间的同步。

为了向你说明 React Router 的问题，让我们来创建一个不使用它的应用。

### 不使用 React Router

```js
import React from 'react'

const About = React.createClass({/*...*/})
const Inbox = React.createClass({/*...*/})
const Home = React.createClass({/*...*/})

const App = React.createClass({
  getInitialState() {
    return {
      route: window.location.hash.substr(1)
    }
  },

  componentDidMount() {
    window.addEventListener('hashchange', () => {
      this.setState({
        route: window.location.hash.substr(1)
      })
    })
  },

  render() {
    let Child
    switch (this.state.route) {
      case '/about': Child = About; break;
      case '/inbox': Child = Inbox; break;
      default:      Child = Home;
    }

    return (
      <div>
        <h1>App</h1>
        <ul>
          <li><a href="#/about">About</a></li>
          <li><a href="#/inbox">Inbox</a></li>
        </ul>
        <Child/>
      </div>
    )
  }
})

React.render(<App />, document.body)
```

作为 URL 变化的散列部分，`<App>` 会渲染从 `this.state.route` 分支而来的不同 `<Child>`。一个很简单的东西，却被弄得如此复杂。

现在想象一下 `Inbox` 有一些在不同的 URL 下的嵌套 UI，主页的详细视图可能像这样：

```
path: /inbox/messages/1234

+---------+------------+------------------------+
| About   |    Inbox   |                        |
+---------+            +------------------------+
| Compose    Reply    Reply All    Archive      |
+-----------------------------------------------+
|Movie tomorrow|                                |
+--------------+   Subject: TPS Report          |
|TPS Report        From:    boss@big.co         |
+--------------+                                |
|New Pull Reque|   So ...                       |
+--------------+                                |
|...           |                                |
+--------------+--------------------------------+
```

然后这可能是一个不查看 message 时的静态页面：

```
path: /inbox

+---------+------------+------------------------+
| About   |    Inbox   |                        |
+---------+            +------------------------+
| Compose    Reply    Reply All    Archive      |
+-----------------------------------------------+
|Movie tomorrow|                                |
+--------------+   10 Unread Messages           |
|TPS Report    |   22 drafts                    |
+--------------+                                |
|New Pull Reque|                                |
+--------------+                                |
|...           |                                |
+--------------+--------------------------------+
```

我们必须让我们的 URL 解析得更智能，然后我们会得到很多的代码去找出给定的 URL 应该是哪一个要被渲染的嵌套组件分支：`App -> About`, `App -> Inbox -> Messages -> Message`, `App -> Inbox -> Messages -> Stats`，等等。

### 使用 React Router

让我们用 React Router 重构这个应用。

```js
import React from 'react'

// 首先我们需要导入一些组件...
import { Router, Route, Link } from 'react-router'

// 然后我们从应用中删除一堆代码和
// 增加一些 <Link> 元素...
const App = React.createClass({
  render() {
    return (
      <div>
        <h1>App</h1>
        {/* 把 <a> 变成 <Link> */}
        <ul>
          <li><Link to="/about">About</Link></li>
          <li><Link to="/inbox">Inbox</Link></li>
        </ul>

        {/*
          接着用 `this.props.children` 替换 `<Child>`
          router 会帮我们找到这个 children
        */}
        {this.props.children}
      </div>
    )
  }
})

// 最后，我们用一些 <Route> 来渲染 <Router>。
// 这些就是路由提供的我们想要的东西。
React.render((
  <Router>
    <Route path="/" component={App}>
      <Route path="about" component={About} />
      <Route path="inbox" component={Inbox} />
    </Route>
  </Router>
), document.body)
```

React Router 知道如何为我们搭建嵌套的 UI，因此我们不用手动地去找出哪些 `<Child>` 组件是用于渲染的。 在内部，router 会将你元素等级的 `<Route>` 转变成一个 [路由配置](/docs/Glossary.md#routeconfig)。但如果你对 JSX 没有研究，那么你可以用普通对象来替代：

```js
const routes = {
  path: '/',
  component: App,
  childRoutes: [
    { path: 'about', component: About },
    { path: 'inbox', component: Inbox },
  ]
}

React.render(<Router routes={routes} />, document.body)
```

## 添加更多的 UI

好了，现在我们准备在 inbox UI 内部嵌套 inbox messages。

```js
// 新建一个组件让其在 Inbox 内部渲染
const Message = React.createClass({
  render() {
    return <h3>Message</h3>
  }
})

const Inbox = React.createClass({
  render() {
    return (
      <div>
        <h2>Inbox</h2>
        {/* 渲染这个 child 路由组件 */}
        {this.props.children || "Welcome to your Inbox"}
      </div>
    )
  }
})

React.render((
  <Router>
    <Route path="/" component={App}>
      <Route path="about" component={About} />
      <Route path="inbox" component={Inbox}>
        {/* 添加一个路由，嵌套进我们想要嵌套的 UI 里 */}
        <Route path="messages/:id" component={Message} />
      </Route>
    </Route>
  </Router>
), document.body)
```

现在访问 URL `inbox/messages/Jkei3c32` 将会匹配到一个新的路由并且嵌套了 `App -> Inbox -> Message` 这个 UI 的分支。

### 获取 URL 参数

我们需要知道一些来自服务器的 message 为了获取它。当你需要渲染的时候，Route 组件会获取一些有用的属性注入其中，尤其是路径中动态部分的参数。就如我们例子中的 `:id`。

```js
const Message = React.createClass({

  componentDidMount() {
    // 来自于路径 `/inbox/messages/:id`
    const id = this.props.params.id

    fetchMessage(id, function (err, message) {
      this.setState({ message: message })
    })
  },

  // ...

})
```

你也可以通过查询字符串来访问参数。假如你访问 `/foo?bar=baz`，你可以通过访问 `this.props.location.query.bar` 从 Route 组件中获得 `"baz"` 的值。

这些就是 React Router 的要点。应用的 UI 就是盒中盒的形式；现在你可以使这些盒子与 URL 和链接保持同步。

这个关于 [路由配置](/docs/guides/basics/RouteConfiguration.md) 的文档深入地描述了 router 的功能。
