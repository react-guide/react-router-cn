# [React Router 中文文档](https://github.com/react-guide/react-router-cn)

> 本文档是基于 React-Router 2.X 版本翻译。最新版变化较多请访问[英文站点](https://reacttraining.com/react-router/web/guides/philosophy)，实在跟不上节奏啊😂😂
在线 Gitbook 地址：http://react-guide.github.io/react-router-cn/

英文原版：https://github.com/rackt/react-router/tree/master/docs

React Router 是完整的 React 路由解决方案

React Router 保持 UI 与 URL 同步。它拥有简单的 API 与强大的功能例如代码缓冲加载、动态路由匹配、以及建立正确的位置过渡处理。你第一个念头想到的应该是 URL，而不是事后再想起。

**重点：这是 React Router 的 `master` 分支，其中包含了很多还没有发布的修改。如果要看到最新公布的代码，请浏览 [`latest` 标签](https://github.com/rackt/react-router/tree/latest)。**

### 文档 & 帮助

- [API 文档与指南](/docs)
- [Change Log](https://github.com/rackt/react-router/blob/master/CHANGELOG.md)
- [Stack Overflow](http://stackoverflow.com/questions/tagged/react-router)
- [Codepen Boilerplate](http://codepen.io/anon/pen/xwQZdy?editors=001) 用于反馈 bug

**注意：** **如果你仍然使用的是 React Router 0.13.x，可以在 [the 0.13.x branch](https://github.com/rackt/react-router/tree/0.13.x) 找到 [文档](https://github.com/rackt/react-router/tree/0.13.x/docs/guides)。升级信息可以查看 [change log](https://github.com/rackt/react-router/blob/master/CHANGELOG.md)。**

如果有疑问和技术难点，请到[我们的 Reactiflux 频道](https://discord.gg/0ZcbPKXt5bYaNQ46)或 [Stack Overflow](http://stackoverflow.com/questions/tagged/react-router) 提问。这里的 issue 是**专门**为反馈 bug 和新特性提出所设立的。

### 浏览器支持

我们支持所有的浏览器和环境中运行 React。

### 安装

首先通过 [npm](https://www.npmjs.com/) 安装：

    $ npm install --save react-router

然后使用一个支持 CommonJS 或 ES2015 的模块管理器，例如 [webpack](https://webpack.github.io/)：

```js
// 使用 ES6 的转译器，如 babel
import { Router, Route, Link } from 'react-router'

// 不使用 ES6 的转译器
var ReactRouter = require('react-router')
var Router = ReactRouter.Router
var Route = ReactRouter.Route
var Link = ReactRouter.Link
```

也可以在 [unpkg](https://unpkg.com) 上构建 UMD 格式：

```html
<script src="https://unpkg.com/react-router/umd/ReactRouter.min.js"></script>
```

你可以在 `window.ReactRouter` 找到这个库。

### 版本控制和稳定性

React Router 遵循语义化版本控制，并很好地诠释了它。我们希望 React Router 是一个稳定的依赖库，这样易于保持流行性。这是我们对你的应用的升级策略。

假设我们目前是 1.0 版本：

1. 2.0 完全向后兼容 1.0，所以你可以放心地升级，然后逐步更新你的代码。
2. 所有在 1.0 被弃用的 API 都会在控制台以 warn 的形式打印出来，并链接到升级指南。
3. 在 3.0 中将会完全移除 1.0 所弃用的东西。
4. 3.0 将发布不早于 2.0 三个月后。最坏的情况是，给你一个新的 API，你需要花费 6 个月的时间去完美升级。
5. 可以用 rackt/rackt-codemod 去自动升级你的代码

> 如果是完全向后兼容的，为什么这不是一个小版本呢？

如果我们不提供向后兼容性，然后你就不会问这个问题 —— 升级后的应用将不可运行。这不是我们想要的结果，我们想要平稳地，逐步地升级。

在实践中，这意味着你可以：

1. 从 1.0 升级到 2.0，你的应用仍可以运行。
2. 逐步更新你的代码到新的 API，在下一个版本发布之前，你有 3 个月的时间去完成。
3. 运行 codemods 去处理自动运行 (2) 部分。
4. 如果您的代码运行没有警告，你可以使用 3.0 版本重复这个列表

### 这看起来像什么？

```js
import React from 'react'
import { Router, Route, Link } from 'react-router'

const App = React.createClass({/*...*/})
const About = React.createClass({/*...*/})
// 等等。

const Users = React.createClass({
  render() {
    return (
      <div>
        <h1>Users</h1>
        <div className="master">
          <ul>
            {/* 在应用中用 Link 去链接路由 */}
            {this.state.users.map(user => (
              <li key={user.id}><Link to={`/user/${user.id}`}>{user.name}</Link></li>
            ))}
          </ul>
        </div>
        <div className="detail">
          {this.props.children}
        </div>
      </div>
    )
  }
})

const User = React.createClass({
  componentDidMount() {
    this.setState({
      // 路由应该通过有用的信息来呈现，例如 URL 的参数
      user: findUserById(this.props.params.userId)
    })
  },

  render() {
    return (
      <div>
        <h2>{this.state.user.name}</h2>
        {/* 等等。 */}
      </div>
    )
  }
})

// 路由配置说明（你不用加载整个配置，
// 只需加载一个你想要的根路由，
// 也可以延迟加载这个配置）。
React.render((
  <Router>
    <Route path="/" component={App}>
      <Route path="about" component={About}/>
      <Route path="users" component={Users}>
        <Route path="/user/:userId" component={User}/>
      </Route>
      <Route path="*" component={NoMatch}/>
    </Route>
  </Router>
), document.body)
```

更多请看 [介绍](/docs/Introduction.md)、[高级用法](/docs/guides/advanced/README.md)和 [示例](https://github.com/rackt/react-router/tree/master/examples)。

### 感谢

感谢[我们的赞助商](https://github.com/rackt/react-router/blob/master/SPONSORS.md)对于 React Router 开发的支持。

React Router 灵感来源于 Ember's fantastic router。非常感谢 Ember 团队。

同时，也感谢 [BrowserStack](https://www.browserstack.com/) 提供一个平台让我们能在真实的浏览器中运行我们的项目。


**本文档翻译流程符合 [ETC 翻译规范](https://github.com/react-guide/ETC)，欢迎你来一起完善**
