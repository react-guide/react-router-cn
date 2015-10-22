# Route Configuration
# 路由配置

[路由配置](/docs/Glossary.md#routeconfig)是一组指令，用来告诉 router 如何[匹配 URL](RouteMatching.md)以及匹配后如何执行代码。我们来通过[一个简单的例子](/docs/Introduction.md#adding-more-ui)解释一下如何编写路由配置。

A [route configuration](/docs/Glossary.md#routeconfig) is basically a set of instructions that tell a router how to try to [match the URL](RouteMatching.md) and what code to run when it does. To illustrate some of the features available in your route config, let's expand on the simple app from [the introduction](/docs/Introduction.md#adding-more-ui).

```js
import React from 'react'
import { Router, Route, Link } from 'react-router'

const App = React.createClass({
  render() {
    return (
      <div>
        <h1>App</h1>
        <ul>
          <li><Link to="/about">About</Link></li>
          <li><Link to="/inbox">Inbox</Link></li>
        </ul>
        {this.props.children}
      </div>
    )
  }
})

const About = React.createClass({
  render() {
    return <h3>About</h3>
  }
})

const Inbox = React.createClass({
  render() {
    return (
      <div>
        <h2>Inbox</h2>
        {this.props.children || "Welcome to your Inbox"}
      </div>
    )
  }
})

const Message = React.createClass({
  render() {
    return <h3>Message {this.props.params.id}</h3>
  }
})

React.render((
  <Router>
    <Route path="/" component={App}>
      <Route path="about" component={About} />
      <Route path="inbox" component={Inbox}>
        <Route path="messages/:id" component={Message} />
      </Route>
    </Route>
  </Router>
), document.body)
```

As configured, this app knows how to render the following 4 URLs:

通过上面的配置，这个应用知道如何渲染下面四个 URL：

URL                     | 组件
------------------------|-----------
`/`                     | `App`
`/about`                | `App -> About`
`/inbox`                | `App -> Inbox`
`/inbox/messages/:id`   | `App -> Inbox -> Message`

### Adding an Index
### 添加首页

想象一下当 URL 为`/`时，我们想渲染一个在`App`中的组件。不过在此时，`App`的`render`中的 `this.props.children`还是`undefined`。这种情况我们可以使用 [`IndexRoute`](/docs/API.md#indexroute) 来设置一个默认页面。

Imagine we'd like to render another component inside of `App` when the URL is `/`. Currently, `this.props.children` inside of `App`'s `render` method is `undefined` in this case. We can use an [`<IndexRoute>`](/docs/API.md#indexroute) to specify a "default" page.

```js
import { IndexRoute } from 'react-router'

const Dashboard = React.createClass({
  render() {
    return <div>Welcome to the app!</div>
  }
})

React.render((
  <Router>
    <Route path="/" component={App}>
      {/* Show the dashboard at / */}
      {/* 当 url 为/时渲染 Dashboard */}
      <IndexRoute component={Dashboard} />
      <Route path="about" component={About} />
      <Route path="inbox" component={Inbox}>
        <Route path="messages/:id" component={Message} />
      </Route>
    </Route>
  </Router>
), document.body)
```

现在，`App`的`render`中的 `this.props.children` 将会是 `<Dashboard>`这个元素。这个功能类似 Apache 的[`DirectoryIndex`](http://httpd.apache.org/docs/2.4/mod/mod_dir.html#directoryindex) 以及 nginx的 [`index`](http://nginx.org/en/docs/http/ngx_http_index_module.html#index)指令，上述功能都是在当请求的 URL 匹配某个目录时，允许你制定一个类似`index.html`的入口文件。

Now, inside `App`'s `render` method `this.props.children` will be a `<Dashboard>` element! This functionality is similar to Apache's [`DirectoryIndex`](http://httpd.apache.org/docs/2.4/mod/mod_dir.html#directoryindex) or nginx's [`index`](http://nginx.org/en/docs/http/ngx_http_index_module.html#index) directive, both of which allow you to specify a file such as `index.html` when the request URL matches a directory path.

我们的 sitemap 现在看起来如下：

Our sitemap now looks like this:

URL                     | 组件
------------------------|-----------
`/`                     | `App -> Dashboard`
`/about`                | `App -> About`
`/inbox`                | `App -> Inbox`
`/inbox/messages/:id`   | `App -> Inbox -> Message`

### Decoupling the UI from the URL
### 让 UI 从 URL 中解耦出来

如果我们可以将 `/inbox` 从 `/inbox/message/:id` 中去除，并且还能够让 `Message` 嵌套在 `App -> Inbox` 中渲染，那会非常赞。绝对路径可以让我们做到这一点。

It would be nice if we could remove the `/inbox` segment from the `/inbox/messages/:id` URL pattern, but still render `Message` nested inside the `App -> Inbox` UI. Absolute `path`s let us do exactly that.

```js
React.render((
  <Router>
    <Route path="/" component={App}>
      <IndexRoute component={Dashboard} />
      <Route path="about" component={About} />
      <Route path="inbox" component={Inbox}>
        {/* Use /messages/:id instead of messages/:id */}
        {/* 使用 /messages/:id 替换 messages/:id */}
        <Route path="/messages/:id" component={Message} />
      </Route>
    </Route>
  </Router>
), document.body)
```

在多层嵌套路由中使用绝对路径的能力让我们对 URL 拥有绝对的掌控。我们无需在 URL 中添加更多的层级，从而可以使用更简洁的 URL。

The ability to use absolute paths in deeply nested routes gives us complete control over what the URL looks like! We don't have to add more segments to the URL just to get nested UI.

我们现在的 URL 对应关系如下：

We can now render the following URLs:

URL                     | Components
------------------------|-----------
`/`                     | `App -> Dashboard`
`/about`                | `App -> About`
`/inbox`                | `App -> Inbox`
`/messages/:id`         | `App -> Inbox -> Message`

**提醒**：绝对路径可能在[动态路由](/docs/guides/advanced/DynamicRouting.md)中无法使用。

**Note**: Absolute paths may not be used in route config that is [dynamically loaded](/docs/guides/advanced/DynamicRouting.md).

### Preserving URLs
###  URL


Wait a minute ... we just changed a URL! [That's not cool](http://www.w3.org/Provider/Style/URI.html). Now everyone who had a link to `/inbox/messages/5` has a **broken link**. :(

等一下，我们刚刚改变了一个 URL! [这样不好](http://www.w3.org/Provider/Style/URI.html)。 现在任何人访问 `/inbox/message/5` 都会看到一个错误页面。:(

不要担心。我们可以使用 [`<Redirect>`](/docs/API.md#redirect) 去让这个 URL 正常工作起来。

Not to worry. We can use a [`<Redirect>`](/docs/API.md#redirect) to make sure that URL still works!

```js
import { Redirect } from 'react-router'

React.render((
  <Router>
    <Route path="/" component={App}>
      <IndexRoute component={Dashboard} />
      <Route path="about" component={About} />
      <Route path="inbox" component={Inbox}>
        <Route path="/messages/:id" component={Message} />
        {/* 跳转 /inbox/messages/:id 到 /messages/:id */}
        {/* Redirect /inbox/messages/:id to /messages/:id */}
        <Redirect from="messages/:id" to="/messages/:id" />
      </Route>
    </Route>
  </Router>
), document.body)
```

Now when someone clicks on that link to `/inbox/messages/5` they'll automatically be redirected to `/messages/5`. :raised_hands:

现在当有人点击 `/inbox/message/5` 这个链接，他们会被自动跳转到 `/message/5`。 :raised_hands:

### Enter and Leave Hooks
### 进入和离开的Hook

[Route](/docs/Glossary.md#route) 可以定义 [`onEnter`](/docs/Glossary.md#enterhook) 和 [`onLeave`](/docs/Glossary.md#leavehook) 两个hook，这些hook会在页面跳转[确认](/docs/guides/advanced/ConfirmingNavigation.md)时触发一次。这些 hook 对于一些情况非常的有用，例如[权限验证](https://github.com/rackt/react-router/tree/master/examples/auth-flow)或者在路由跳转前将一些数据持久化保存起来。

[Route](/docs/Glossary.md#route)s may also define [`onEnter`](/docs/Glossary.md#enterhook) and [`onLeave`](/docs/Glossary.md#leavehook) hooks that are invoked once a transition has been [confirmed](/docs/guides/advanced/ConfirmingNavigation.md). These hooks are useful for various things like [requiring auth](https://github.com/rackt/react-router/tree/master/examples/auth-flow) when a route is entered and saving stuff to persistent storage before a route unmounts.

在路由跳转过程中，[`onLeave` hook](/docs/Glossary.md#leavehook) 会在所有将离开的路由中触发，从最下层的子路由开始直到最外层父路由结束。然后[`onEnter` hook](/docs/Glossary.md#enterhook)会从最外层的父路由开始直到最下层子路由结束。

During a transition, [`onLeave` hooks](/docs/Glossary.md#leavehook) run first on all routes we are leaving, starting with the leaf route on up to the first common ancestor route. Next, [`onEnter` hooks](/docs/Glossary.md#enterhook) run starting with the first parent route we're entering down to the leaf route.

继续我们上面的例子，如果一个用户点击链接，从 `/message/5` 跳转到 `/ablout`，下面是这些 hook 的执行顺序：

Continuing with our example above, if a user clicked on a link to `/about` from `/messages/5`, the following hooks would run in this order:

  - `onLeave` on the `/messages/:id` route
  - `/messages/:id` 的 `onLeave`
  - `onLeave` on the `/inbox` route
  - `/inbox` 的 `onLeave`
  - `onEnter` on the `/about` route
  - `/about` 的 `onEnter`

### Alternate Configuration
### 替换的配置方式

因为 [route](/docs/Glossary.md#route) 一般被嵌套使用，所以使用 [JSX](https://facebook.github.io/jsx/) 这种天然具有简洁嵌套型语法的结构来描述它们的关系非常方便。然而，如果你不想使用 JSX，也可以直接使用原生 [route](/docs/Glossary.md#route) 数组对象。

Since [routes](/docs/Glossary.md#route) are usually nested, it's useful to use a concise nested syntax like [JSX](https://facebook.github.io/jsx/) to describe their relationship to one another. However, you may also use an array of plain [route](/docs/Glossary.md#route) objects if you prefer to avoid using JSX.

上面我们讨论的路由配置可以被写成下面这个样子：

The route config we've discussed up to this point could also be specified like this:

```js
const routeConfig = [
  { path: '/',
    component: App,
    indexRoute: { component: Dashboard },
    childRoutes: [
      { path: 'about', component: About },
      { path: 'inbox',
        component: Inbox,
        childRoutes: [
          { path: '/messages/:id', component: Message },
          { path: 'messages/:id',
            onEnter: function (nextState, replaceState) {
              replaceState(null, '/messages/' + nextState.params.id)
            }
          }
        ]
      }
    ]
  }
]

React.render(<Router routes={routeConfig} />, document.body)
```
