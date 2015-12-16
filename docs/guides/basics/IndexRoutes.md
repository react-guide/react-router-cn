# 默认路由（IndexRoute）与 IndexLink

## 默认路由（IndexRoute）

在解释 `默认路由(IndexRoute)` 的用例之前，我们来设想一下，一个不使用默认路由的路由配置是什么样的：

```js
<Router>
  <Route path="/" component={App}>
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```

当用户访问 `/` 时, App 组件被渲染，但组件内的子元素却没有，
`App` 内部的 `this.props.children` 为 undefined 。
你可以简单地使用 `{this.props.children ||
<Home/>}` 来渲染一些默认的 UI 组件。

但现在，`Home` 无法参与到比如 `onEnter` hook 这些路由机制中来。 
在 `Home` 的位置，渲染的是 `Accounts` 和 `Statements`。
由此，router 允许你使用 `IndexRoute` ，以使 `Home` 作为最高层级的路由出现.


```js
<Router>
  <Route path="/" component={App}>
    <IndexRoute component={Home}/>
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```

现在 `App` 能够渲染 `{this.props.children}` 了，
我们也有了一个最高层级的路由，使 `Home` 可以参与进来。

## Index Links

如果你在这个 app 中使用 `<Link to="/">Home</Link>` , 
它会一直处于激活状态，因为所有的 URL 的开头都是 `/` 。
这确实是个问题，因为我们仅仅希望在 `Home` 被渲染后，激活并链接到它。

如果需要在 `Home` 路由被渲染后才激活的指向 `/` 的链接，请使用 `<IndexLink to="/">Home</IndexLink>`
