# [React Router 中文文档](https://github.com/react-guide/react-router-cn)

<img src="https://rackt.github.io/react-router/img/vertical.png" width="300"/>

在线 Gitbook 地址：http://react-guide.github.io/react-router-cn/

英文原版：https://github.com/rackt/react-router/tree/master/docs

**翻译正在进行中，[加入我们](https://github.com/react-guide/translation-guide)**

React Router 是完整的 React 路由解决方案

React Router 保持 UI 与 URL 同步。它拥有简单的 API 与强大的功能例如代码缓冲加载、动态路由匹配、以及建立正确的位置过渡处理。你第一个念头想到的应该是 URL，而不是事后再想起。

### 文档 & 帮助

- [API 文档与指南](/docs)
- [Upgrade Guide](https://github.com/rackt/react-router/blob/master/UPGRADE_GUIDE.md)
- [Changelog](https://github.com/rackt/react-router/blob/master/CHANGELOG.md)
- [#react-router channel on reactiflux](http://www.reactiflux.com/)

**注意：** **如果你仍然使用的是 React Router 0.13.x，可以在 [the 0.13.x branch](https://github.com/rackt/react-router/tree/0.13.x) 找到 [文档](https://github.com/rackt/react-router/tree/0.13.x/docs/guides)。**

### 浏览器支持

我们支持所有的浏览器和环境中运行 React。

### 安装

#### npm + webpack/browserify

首先通过 [npm](https://www.npmjs.com/) 安装：

    $ npm install history react-router@latest

请注意，你还需要安装 [history](https://www.npmjs.com/package/history)，因为它也是 React Router 的依赖，而且在 npm 3+ 下不会自动安装。

然后如你使用别的一样使用模块管理器或者 webpack：

```js
// 使用 ES6 的转译器，如 babel
import { Router, Route, Link } from 'react-router'

// 不使用 ES6 的转译器
var ReactRouter = require('react-router')
var Router = ReactRouter.Router
var Route = ReactRouter.Route
var Link = ReactRouter.Link
```

你可以从 `lib` 目录 require 你需要的部分：

```js
import { Router } from 'react-router/lib/Router'
```

在 `umd` 目录还有一个 UMD 模块格式来构建：

```js
import ReactRouter from 'react-router/umd/ReactRouter'
```

如果你要全局调用，你可以在 `window.ReactRouter` 找到这个库。

#### CDN

如果你想在页面上用 `<script>` 标签来完成它，你可以用 UMD/global 构建 [cdnjs 托管版本](https://cdnjs.com/libraries/react-router)。

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

React Router 灵感来源于 Ember's fantastic router。非常感谢 Ember 团队。

同时，也感谢 [BrowserStack](https://www.browserstack.com/) 提供一个平台让我们能在真实的浏览器中运行我们的项目。
