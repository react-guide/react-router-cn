# API 接口

> TODO: Need proofread https://github.com/rackt/react-router/blob/master/docs/API.md

* [组件](#components)
  - [Router](#router)
  - [Link](#link)
  - [IndexLink](#indexlink)
  - [RoutingContext](#routingcontext)

* [组件的配置](#configuration-components)
  - [Route](#route)
  - [PlainRoute](#plainroute)
  - [Redirect](#redirect)
  - [IndexRoute](#indexroute)
  - [IndexRedirect](#indexredirect)

* [Route 组件](#route-components)
  - [已命名的组件](#named-components)

* [Mixins](#mixins)
  - [生命周期](#lifecycle-mixin)
  - [History](#history-mixin)
  - [RouteContext](#routecontext-mixin)

* [Utilities](#utilities)
  * [useRoutes](#useroutescreatehistory)
  * [match](#matchlocation-cb)
  * [createRoutes](#createroutesroutes)
  * [PropTypes](#proptypes)


## 组件

### Router
React Router 的重要组件。它能保持 UI 和 URL 的同步。

#### Props
##### `children` (required)
一个或多个的 [`Route`](#route) 或 [`PlainRoute`](#plainroute)。当 history 改变时， `<Router>` 会匹配出 [`Route`](#route) 的一个分支，并且渲染这个分支中配置的[组件](#routecomponent)，渲染时保持父 route 组件嵌套子 route 组件。

##### `routes`
`children` 的别名。

##### `history`
Router 监听的 history 对象，由 `history` 包提供。

##### `createElement(Component, props)`
当 route 准备渲染 route 组件的一个分支时，就会用这个函数来创建 element。当你使用某种形式的数据进行抽象时，你可以想要获取创建 element 的控制权，例如在这里设置组件监听 store 的变化，或者使用 props 为每个组件传入一些应用模块。

```js
<Router createElement={createElement} />

// 默认行为
function createElement(Component, props) {
  // 确保传入了所有的 props！
  return <Component {...props}/>
}

// 你可能会使用什么，如 Relay
function createElement(Component, props) {
  // 确保传入了所有的 props！
  return <RelayContainer Component={Component} routerProps={props}/>
}
```

##### `stringifyQuery(queryObject)`
一个用于把 [`Link`](#link) 或调用 [`transitionTo`](#transitiontopathname-query-state) 函数的对象转化成 URL query 字符串的函数。

##### `parseQueryString(queryString)`
一个用于把 query 字符串转化成对象，并传递给 route 组件 props 的函数。

##### `onError(error)`
当路由匹配到时，也有可能会抛出错误，此时你就可以捕获和处理这些错误。通常，它们会来自那些异步的特性，如 [`route.getComponents`](#getcomponentslocation-callback)，[`route.getIndexRoute`](#getindexroutelocation-callback)，和 [`route.getChildRoutes`](#getchildrouteslocation-callback)。

##### `onUpdate()`
当 URL 改变时，需要更新路由的 state 时会被调用。

#### 示例
请看仓库中的示例目录 [`examples/`](/examples)，这些例子都广泛使用了 `Router`。



### Link
允许用户浏览应用的主要方式。`<Link>` 以适当的 href 去渲染一个可访问的锚标签。

`<Link>` 可以知道哪个 route 的链接是激活状态的，并可以自动为该链接添加 `activeClassName` 或 `activeStyle`。

#### Props
##### `to`
跳转链接的路径，如 `/users/123`。

##### `query`
已经转化成字符串的键值对的对象。

##### `hash`
URL 的 hash 值，如 `#a-hash`。

**注意：React Router 目前还不能管理滚动条的位置，并且不会自动滚动到 hash 对应的元素上。如果需要管理滚动条位置，可以使用 [scroll-behavior](https://github.com/rackt/scroll-behavior) 这个库。**

##### `state`
保存在 `location` 中的 state。

##### `activeClassName`
当某个 route 是激活状态时，`<Link>` 可以接收传入的 className。失活状态下是默认的 class。

##### `activeStyle`
当某个 route 是激活状态时，可以将样式添加到链接元素上。

##### `onClick(e)`
自定义点击事件的处理方法。如处理 `<a>` 标签一样 - 调用 `e.preventDefault()` 来防止过度的点击，同时 `e.stopPropagation()` 可以阻止冒泡的事件。

##### **其他**
你也可以在 `<a>` 标签上传入一些你想要的 props，如 `title`，`id`，`className` 等等。

#### 示例
如 `<Route path="/users/:userId" />` 这样的 route：

```js
<Link to={`/users/${user.id}`} activeClassName="active">{user.name}</Link>
// 变成它们其中一个依赖在 History 上，当这个 route 是
// 激活状态的
<a href="/users/123" class="active">Michael</a>
<a href="#/users/123">Michael</a>

// 修改 activeClassName
<Link to={`/users/${user.id}`} activeClassName="current">{user.name}</Link>

// 当链接激活时，修改它的样式
<Link to="/users" style={{color: 'white'}} activeStyle={{color: 'red'}}>Users</Link>
```

### IndexLink
敬请期待！

### RoutingContext
在 context 中给定路由的 state、设置 history 对象和当前的 location，`<RoutingContext>` 就会去渲染组件树。



## 组件的配置

## Route
`Route` 是用于声明路由映射到应用程序的组件层。

#### Props
##### `path`
URL 中的路径。

它会组合父 route 的路径，除非它是从 `/` 开始的，
将它变成一个绝对路径。

**注意**：在[动态路由](/docs/guides/advanced/DynamicRouting.md)中，绝对路径可能不适用于 route 配置中。

如果它是 undefined，路由会去匹配子 route。

##### `component`
当匹配到 URL 时，单个的组件会被渲染。它可以
被父 route 组件的 `this.props.children` 渲染。

```js
const routes = (
  <Route component={App}>
    <Route path="groups" component={Groups}/>
    <Route path="users" component={Users}/>
  </Route>
)

class App extends React.Component {
  render () {
    return (
      <div>
        {/* 这会是 <Users> 或 <Groups> */}
        {this.props.children}
      </div>
    )
  }
}
```

##### `components`
Route 可以定义一个或多个已命名的组件，当路径匹配到 URL 时，
它们作为 `name:component` 对的一个对象去渲染。它们可以被
父 route 组件的 `this.props[name]` 渲染。

```js
// 想想路由外部的 context — 如果你可拔插
// `render` 的部分，你可能需要这么做：
// <App main={<Users />} sidebar={<UsersSidebar />} />

const routes = (
  <Route component={App}>
    <Route path="groups" components={{main: Groups, sidebar: GroupsSidebar}}/>
    <Route path="users" components={{main: Users, sidebar: UsersSidebar}}>
      <Route path="users/:userId" component={Profile}/>
    </Route>
  </Route>
)

class App extends React.Component {
  render () {
    const { main, sidebar } = this.props
    return (
      <div>
        <div className="Main">
          {main}
        </div>
        <div className="Sidebar">
          {sidebar}
        </div>
      </div>
    )
  }
}

class Users extends React.Component {
  render () {
    return (
      <div>
        {/* 当路径是 "/users/123" 是 `children` 会是 <Profile> */}
        {/* UsersSidebar 也可以获取作为 this.props.children 的 <Profile> ，
            所以这有点奇怪，但你可以决定哪一个可以
            继续这种嵌套 */}
        {this.props.children}
      </div>
    )
  }
}
```
##### `getComponent(location, callback)`
与 `component` 一样，但是是异步的，对于 code-splitting 很有用。

###### `callback` signature
`cb(err, component)`

```js
<Route path="courses/:courseId" getComponent={(location, cb) => {
  // 做一些异步操作去查找组件
  cb(null, Course)
}}/>
```

##### `getComponents(location, callback)`
与 `component` 一样，但是是异步的，对于 code-splitting 很有用。

###### `callback` signature
`cb(err, components)`

```js
<Route path="courses/:courseId" getComponent={(location, cb) => {
  // 做一些异步操作去查找组件
  cb(null, {sidebar: CourseSidebar, content: Course})
}}/>
```

##### `children`
Route 可以被嵌套，`this.props.children` 包含了从子 route 组件创建的元素。由于这是路由设计中非常重要的部分，请参考 [Route 配置](/docs/guides/basics/RouteConfiguration.md)。

##### `onEnter(nextState, replaceState, callback?)`
当 route 即将进入时调用。它提供了下一个路由的 state，一个函数重定向到另一个路径。`this` 会触发钩子去创建 route 实例。

当 `callback` 作为函数的第三个参数传入时，这个钩子将是异步执行的，并且跳转会阻塞直到 `callback` 被调用。

##### `onLeave()`
当 route 即将退出时调用。



## PlainRoute
route 定义的一个普通的 JavaScript 对象。 `Router` 把 JSX 的 `<Route>` 转化到这个对象中，如果你喜欢，你可以直接使用它们。 所有的 props 都和 `<Route>` 的 props 一样，除了那些列在这里的。
#### Props
##### `childRoutes`
子 route 的一个数组，与在 JSX route 配置中的 `children` 一样。

##### `getChildRoutes(location, callback)`
与 `childRoutes` 一样，但是是异步的，并且可以接收 `location`。对于 code-splitting 和动态路由匹配很有用（给定一些 state 或 session 数据会返回不同的子 route）。

###### `callback` signature
`cb(err, routesArray)`

```js
let myRoute = {
  path: 'course/:courseId',
  childRoutes: [
    announcementsRoute,
    gradesRoute,
    assignmentsRoute
  ]
}

// 异步的子 route
let myRoute = {
  path: 'course/:courseId',
  getChildRoutes(location, cb) {
    // 做一些异步操作去查找子 route
    cb(null, [ announcementsRoute, gradesRoute, assignmentsRoute ])
  }
}

// 可以根据一些 state
// 跳转到依赖的子 route
<Link to="/picture/123" state={{ fromDashboard: true }}/>

let myRoute = {
  path: 'picture/:id',
  getChildRoutes(location, cb) {
    let { state } = location

    if (state && state.fromDashboard) {
      cb(null, [dashboardPictureRoute])
    } else {
      cb(null, [pictureRoute])
    }
  }
}
```

##### `indexRoute`
[index route](/docs/guides/basics/IndexRoutes.md)。这与在使用 JSX route 配置时指定一个 `<IndexRoute>` 子集一样。

##### `getIndexRoute(location, callback)`

与 `indexRoute` 一样，但是是异步的，并且可以接收 `location`。与 `getChildRoutes` 一样，对于 code-splitting 和动态路由匹配很有用

###### `callback` signature
`cb(err, route)`

```js
// 例如：
let myIndexRoute = {
  component: MyIndex
}

let myRoute = {
  path: 'courses',
  indexRoute: myIndexRoute
}

// 异步的 index route
let myRoute = {
  path: 'courses',
  getIndexRoute(location, cb) {
    // 做一些异步操作
    cb(null, myIndexRoute)
  }
}
```



## Redirect
在应用中 `<Redirect>` 可以设置重定向到其他 route 而不改变旧的 URL。

#### Props
##### `from`
你想由哪个路径进行重定向，包括动态段。

##### `to`
你想重定向的路径。

##### `query`
默认情况下，query 的参数只会经过，但如果你需要你可以指定它们。

```js
// 我们需要从 `/profile/123` 改变到 `/about/123`
// 并且由 `/get-in-touch` 重定向到 `/contact`
<Route component={App}>
  <Route path="about/:userId" component={UserProfile}/>
  {/* /profile/123 -> /about/123 */}
  <Redirect from="profile/:userId" to="about/:userId" />
</Route>
```

注意，在 route 层 `<Redirect>` 可以被放在任何地方，尽管[正常的优先](/docs/guides/basics/RouteMatching.md#precedence) 规则仍适用。如果你喜欢将下一个重定向到它各自的 route 上，`from` 的路径会匹配到一个与 `path` 一样的正常路径。

```js
<Route path="course/:courseId">
  <Route path="dashboard" />
  {/* /course/123/home -> /course/123/dashboard */}
  <Redirect from="home" to="dashboard" />
</Route>
```



## IndexRoute
当用户在父 route 的 URL 时，
Index Routes 允许你为父 route 提供一个默认的 "child"，
并且为使`<IndexLink>` 能用提供了约定。

请看 [Index Routes 指南](/docs/guides/basics/IndexRoutes.md)。

#### Props
与 [Route](#route) 的 props 一样，除了 `path`。



## IndexRedirect
Index Redirects 允许你从一个父 route 的 URL 重定向到其他 route。
它们被用于允许子 route 作为父 route 的默认 route，
同时保持着不同的 URL。

请看 [Index Routes 指南](/docs/guides/basics/IndexRoutes.md)。

#### Props
与 [Redirect](#redirect) 的 props 一样，除了 `from`。



## Route Components
当 route 匹配到 URL 时会渲染一个 route 的组件。路由会在渲染时将以下属性注入组件中：

#### `history`
Router 的 history [history](https://github.com/rackt/history/blob/master/docs)。

对于跳转很有用的 `this.props.history.pushState(state, path, query)`

#### `location`
当前的 [location](https://github.com/rackt/history/blob/master/docs/Location.md)。

#### `params`
URL 的动态段。

#### `route`
渲染组件的 route。

#### `routeParams`
`this.props.params` 是直接在组件中指定 route 的一个子集。例如，如果 route 的路径是 `users/:userId` 而 URL 是 `/users/123/portfolios/345`，那么 `this.props.routeParams` 会是 `{userId: '123'}`，并且 `this.props.params` 会是 `{userId: '123', portfolioId: 345}`。

#### `children`
匹配到子 route 的元素将被渲染。如果 route 有[已命名的组件](https://github.com/rackt/react-router/blob/master/docs/API.md#named-components)，那么此属性会是 undefined，并且可用的组件会被直接替换到 `this.props` 上。

##### 示例
```js
render((
  <Router>
    <Route path="/" component={App}>
      <Route path="groups" component={Groups} />
      <Route path="users" component={Users} />
    </Route>
  </Router>
), node)

class App extends React.Component {
  render() {
    return (
      <div>
        {/* 这可能是 <Users> 或 <Groups> */}
        {this.props.children}
      </div>
    )
  }
}
```

### 已命名的组件
当一个 route 有一个或多个已命名的组件时，其子元素的可用性是通过 `this.props` 命名的。因此 `this.props.children` 将会是 undefined。那么所有的 route 组件都可以参与嵌套。

#### 示例
```js
render((
  <Router>
    <Route path="/" component={App}>
      <Route path="groups" components={{main: Groups, sidebar: GroupsSidebar}} />
      <Route path="users" components={{main: Users, sidebar: UsersSidebar}}>
        <Route path="users/:userId" component={Profile} />
      </Route>
    </Route>
  </Router>
), node)

class App extends React.Component {
  render() {
    // 在父 route 中，被匹配的子 route 变成 props
    return (
      <div>
        <div className="Main">
          {/* 这可能是 <Groups> 或 <Users> */}
          {this.props.main}
        </div>
        <div className="Sidebar">
          {/* 这可能是 <GroupsSidebar> 或 <UsersSidebar> */}
          {this.props.sidebar}
        </div>
      </div>
    )
  }
}

class Users extends React.Component {
  render() {
    return (
      <div>
        {/* 如果在 "/users/123" 路径上这会是 <Profile> */}
        {/* UsersSidebar 也会获取到作为 this.props.children 的 <Profile> 。
            你可以把它放这渲染 */}
        {this.props.children}
      </div>
    )
  }
}
```


## Mixins

## 生命周期 Mixin
在组件中添加一个钩子，当路由要从 route 组件的配置中跳转出来时被调用，并且有机会去取消这次跳转。主要用于表单的部分填写。

在常规的跳转中， `routerWillLeave` 会接收到一个单一的参数：我们正要跳转的 `location`。去取消此次跳转，返回 false。

提示用户确认，返回一个提示信息（字符串）。在 web 浏览器 beforeunload 事件发生时，`routerWillLeave` 不会接收到一个 location 的对象（假设你正在使用 `useBeforeUnload` history 的增强方法）。在此之上，我们是不可能知道要跳转的 location，因此 `routerWillLeave` 必须在用户关闭标签之前返回一个提示信息。

#### 生命周期方法
##### `routerWillLeave(nextLocation)`
当路由尝试从一个 route 跳转到另一个并且渲染这个组件时被调用。

##### arguments
- `nextLocation` - 下一个 location



## History Mixin
在组件中添加路由的 history 对象。

**注意**：你的 route components 不需要这个 mixin，它在 `this.props.history` 中已经是可用的了。这是为了组件更深次的渲染树中需要访问路由的 `history` 对象。

#### Methods
##### `pushState(state, pathname, query)`
跳转至一个新的 URL。

###### arguments
- `state` - location 的 state。
- `pathname` - 没有 query 完整的 URL。
- `query` - 通过路由字符串化的一个对象。

##### `replaceState(state, pathname, query)`
在不影响 history 长度的情况下（如一个重定向），用新的 URL 替换当前这个。

###### 参数
- `state` - location 的 state。
- `pathname` - 没有 query 完整的 URL。
- `query` - 通过路由字符串化的一个对象。

##### `go(n)`
在 history 中使用 `n` 或 `-n` 进行前进或后退

##### `goBack()`
在 history 中后退。

##### `goForward()`
在 history 中前进。

##### `createPath(pathname, query)`
使用路由配置，将 query 字符串化加到路径名中。

##### `createHref(pathname, query)`
使用路由配置，创建一个 URL。例如，它会在 `pathname` 的前面加上 `#/` 给 hash history。

##### `isActive(pathname, query, indexOnly)`
根据当前路径是否激活返回 `true` 或 `false`。通过 `pathname` 匹配到 route 分支下的每个 route 将会是 true（子 route 是激活的情况下，父 route 也是激活的），除非 `indexOnly` 已经指定了，在这种情况下，它只会匹配到具体的路径。

###### 参数
- `pathname` - 没有 query 完整的 URL。
- `query` - 如果没有指定，那会是一个包含键值对的对象，并且在当前的 query 中是激活状态的 - 在当前的 query 中明确是 `undefined` 的值会丢失相应的键或 `undefined`
- `indexOnly` - 一个 boolean（默认：`false`）。

#### 示例
```js
import { History } from 'react-router'

React.createClass({
  mixins: [ History ],
  render() {
    return (
      <div>
        <div onClick={() => this.history.pushState(null, '/foo')}>Go to foo</div>
        <div onClick={() => this.history.replaceState(null, 'bar')}>Go to bar without creating a new history entry</div>
        <div onClick={() => this.history.goBack()}>Go back</div>
     </div>
   )
 }
})
```

假设你正在使用 bootstrap，并且在 Tab 中想让那些 `li` 获得 `active`：

```js
import { Link, History } from 'react-router'

const Tab = React.createClass({
  mixins: [ History ],
  render() {
    let isActive = this.history.isActive(this.props.to, this.props.query)
    let className = isActive ? 'active' : ''
    return <li className={className}><Link {...this.props}/></li>
  }
})

// 如 <Link/> 一样使用它，你会得到一个包进 `li` 的锚点
// 并且还有一个自动的 `active` class。
<Tab href="foo">Foo</Tab>
```

#### 但我仍然在使用 Classes
> 注意难道我们没有告诉你要使用 ES6 的 Classes？:)

https://twitter.com/soprano/status/610867662797807617

在应用中少数组件由于 `History` mixin 的缘故而用得不爽，此时有以下几个选项：

- 让 `this.props.history` 通过 route 组件到达需要它的组件中。
- 使用 context

```js
import { PropTypes } from 'react-router'

class MyComponent extends React.Component {
  doStuff() {
    this.context.history.pushState(null, '/some/path')
  }
}

MyComponent.contextTypes = { history: PropTypes.history }
```

- [确保你的 history 是一个 module](/docs/guides/advanced/NavigatingOutsideOfComponents.md)
- 创建一个高阶的组件，我们可能用它来结束跳转和阻止 history，只是没有时间去思考所有的方法。

```js
function connectHistory(Component) {
  return React.createClass({
    mixins: [ History ],
    render() {
      return <Component {...this.props} history={this.history} />
    }
  })
}

// 其他文件
import connectHistory from './connectHistory'

class MyComponent extends React.Component {
  doStuff() {
    this.props.history.pushState(null, '/some/where')
  }
}

export default connectHistory(MyComponent)
```



## RouteContext Mixin
RouteContext mixin 提供了一个将 route 组件设置到 context 中的便捷方法。这对于 route 渲染元素并且希望用 [生命周期 mixin](#lifecycle) 来阻止跳转是很有必要的。

简单地将 `this.context.route` 添加到组件中。



## Utilities

## `useRoutes(createHistory)`
返回一个新的 `createHistory` 函数，它可以用来创建读取 route 的 history 对象。

- listen((error, nextState) => {})
- listenBeforeLeavingRoute(route, (nextLocation) => {})
- match(location, (error, redirectLocation, nextState) => {})
- isActive(pathname, query, indexOnly=false)


## `match(location, cb)`

这个函数被用于服务端渲染。它在渲染之前会匹配一组 route 到一个 location，并且在完成时调用 `callback(error, redirectLocation, renderProps)`。

传给回调函数去 `match` 的三个参数如下：
* `error`：如果报错时会出现一个 Javascript 的 `Error` 对象，否则是 `undefined`。
* `redirectLocation`：如果 route 重定向时会有一个 [Location](/docs/Glossary.md#location) 对象，否则是 `undefined`。
* `renderProps`：当匹配到 route 时 props 应该通过路由的 context，否则是 `undefined`。

如果这三个参数都是 `undefined`，这就意味着在给定的 location 中没有 route 被匹配到。

**注意：你可能不想在浏览器中用它，除非你做的是异步 route 的服务端渲染。**


## `createRoutes(routes)`

创建并返回一个从给定对象 route 的数组，它可能是 JSX 的 route，一个普通对象的 route，或是其他的数组。

#### params
##### `routes`
一个或多个的 [`Route`](#route) 或 [`PlainRoute`](#plainRoute)。


## PropTypes
敬请期待！
