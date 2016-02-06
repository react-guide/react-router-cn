# 词汇表
这是 React Router 库以及文档中常用术语的词汇表，并附有 [type signatures（类型签名）](http://flowtype.org/docs/quick-reference.html)，以首字母顺序列出。

* [Action（动作）](#action)
* [Component（组件）](#component)
* [EnterHook](#enterhook)
* [LeaveHook](#leavehook)
* [Location](#location)
* [LocationKey](#locationkey)
* [LocationState](#locationstate)
* [Path（路径）](#path)
* [Pathname（路径名）](#pathname)
* [Params（参数）](#params)
* [Query](#query)
* [QueryString](#querystring)
* [RedirectFunction（重定向函数）](#redirectfunction)
* [Route（路由）](#routeF)
* [RouteComponent（路由组件）](#routecomponent)
* [RouteConfig（路由配置）](#routeconfig)
* [RouteHook](#routehook)
* [RoutePattern](#routepattern)
* [Router（路由器）](#router)
* [RouterListener](#routerlistener)
* [RouterState](#routerstate)

## Action

```js
type Action = 'PUSH' | 'REPLACE' | 'POP';
```
*Action* 描述了URL变化的类型。有以下几种类型：

  -  `PUSH` — 表示有一个新条目加入了 history
  -  `REPLACE` — 表示 history 中的当前条目被修改
  -  `POP` — 表示有一个新的当前条目，例如“current pointer（当前指针）”被修改

## Component

```js
type Component = ReactClass | string;
```
这里的 “组件” 指的是React组件对应的类或者字符串（比如“div”）。基本上，它是任何可以作为第一个参数传入 [`React.createElement`](https://facebook.github.io/react/docs/top-level-api.html#react.createelement) 的对象。

## EnterHook

```js
type EnterHook = (nextState: RouterState, replaceState: RedirectFunction, callback?: Function) => any;
```

*enter hook* 是一个用户定义的函数，当路由将要开始被渲染之前，它会被调用。它接受下一个 [router state](#routerstate) 作为第一个参数。第二个参数 [`replaceState` function](#redirectfunction) 可以被用于触发URL的变化。

如果 enter hook 需要异步执行，那么第三个参数 `callback` 可以被用于设置回调，以便变换过程继续进行

**注意：** 在enter hook中使用 `callback` 会让变换过程处于阻塞状态，直到 `callback` 被回调。**如果你不能快速地回调的话，这可能会导致整个UI失去响应。**


## LeaveHook

```js
type LeaveHook = () => any;
```
*leave hook* 是一个用户定义的函数，在离开当前路由之前，它会被调用。

## Location

```js
type Location = {
  pathname: Pathname;
  search: QueryString;
  query: Query;
  state: LocationState;
  action: Action;
  key: LocationKey;
};
```

*location* 回答了两个重要的（哲学）问题：

  - 我在哪里？
  - 我是怎么到这里的？

当URL变化时，新的locations将会产生。你可以在[`history`的文档 ](https://github.com/rackt/history/blob/master/docs/Location.md)中更详细地了解loaction。

## LocationKey

```js
type LocationKey = string;
```

*location key* 是能代表一个 [`location`](#location) 的唯一字符串。它能帮助你准确地回答“我在哪”这个问题。


## LocationState

```js
type LocationState = ?Object;
```

*location state* 是一个由任意数据构成的对象，以便和某个特殊的 [`location`](#location) 联系起来。它可以被用于在 location 上绑定一些不能通过URL获取的额外状态。


这个类型得名于 HTML5 中 [`pushState`][pushState] 和 [`replaceState`][replaceState] 的第一个参数

[pushState]: https://developer.mozilla.org/en-US/docs/Web/API/History_API#The_pushState()_method
[replaceState]: https://developer.mozilla.org/en-US/docs/Web/API/History_API#The_replaceState()_method

## Path

```js
type Path = Pathname + QueryString;
```
*path（路径）* 代表URL的路径。

## Pathname

```js
type Pathname = string;
```

*pathname（路径名）* 是URL中用于描述分层路径的一部分，包括最前的 `/`。比如，在 `http://example.com/the/path?the=query` 这个URL中，`/the/path` 就是 pathname。它和浏览器中的 `window.location.pathname` 是同样的意思。


## QueryString

```js
type QueryString = string;
```

*query string* 是URL中紧跟在 [pathname（路径名）](#pathname) 后面的一部分，包括最前的 `?`。比如，在 `http://example.com/the/path?the=query` 这个URL中，`?the=query` 就是 query string。它和浏览器中的 `window.loaction.search` 是同样的意思。


## Query

```js
type Query = Object;
```
*query* 是 [query string](#querystring) 解析后的版本。


## Params

```js
type Params = Object;
```

*params（参数）* 指的是由URL中的 [pathname（路径名）](#pathname) 经过解析之后生成的键/值对。它的值在通常情况下是字符串类型，只有当一个键对应了多个值时，才可以是数组。

## RedirectFunction

```js
type RedirectFunction = (state: ?LocationState, pathname: Pathname | Path, query: ?Query) => void;
```

*redirect function（重定向函数）* 在 [`onEnter` hooks](#enterhook) 中被用于触发跳转到新的URL。


## Route

```js
type Route = {
  component: RouteComponent;
  path: ?RoutePattern;
  onEnter: ?EnterHook;
  onLeave: ?LeaveHook;
};
```

*route（路由）*指定了一个 [component（组件）](#component) 作为UI的一部分。它应该嵌套在一个树状结构中，以便符合组件的层次。


它或许有助于让你把路由看做整个 UI 的“入口”。当然，你不需要为组件层中的每个组件都设置路由，只需要为那些因 URL 变化而不同的组件设置即可。


## RouteComponent

```js
type RouteComponent = Component;
```

*route component（路由组件）* 指的是一个直接被 [route（路由）](#route)（例如 `<Route component>`）渲染出的 [component（组件）](#component)。router（路由器）会从路由组件中创建元素，并且把它们作为 `this.props.children` 提供给上一层的路由组件。除了 `children`，路由组件还会接收以下props：

  - `router` – [router（路由器）](#router)实例
  - `location` – 当前的 [location](#location)
  - `params` – 当前的 [params（参数）](#params)
  - `route` – 声明了这个组件的 [route（路由）](#route)
  - `routeParams` – 在路由的 [`path`](#routepattern) 中指定参数的集合

## RouteConfig

```js
type RouteConfig = Array<Route>;
```

*route config（路由配置）* 是一个由 [route（路由）](#route) 组成的数组，它指定了路由器使用路由匹配URL时的先后顺序。


## RouteHook

```js
type RouteHook = (nextLocation?: Location) => any;
```

*route hook* 是用于防止用户离开某个路由的函数。在正常的路由变换时，它接收下一个 
[location](#location) 作为参数，并且必须 `return false` 以取消变换，或者 `return` 提示消息给用户。 当它在web浏览器的 `beforeunload ` 事件中被调用时，它不会接收任何参数，且必须 `return` 一个提示信息以取消变换。


## RoutePattern

```js
type RoutePattern = string;
```

*route pattern*（或者 “path”）是用于描述部分URL特征的字符串。Pattern 会被编译成函数，这些函数会被用于尝试匹配URL。Pattern 必须使用以下特殊字符：

  - `:paramName` – 在下一个 `/`、`?` 或 `#` 前，匹配 URL 的局部。被匹配的部分被称为 [param（参数）](#params)
  - `()` – 匹配URL的一部分，是可选的
  - `*` – 匹配任意字符（非贪婪）直到 Pattern 中的下一个字符，如果没有下一个字符，那么会一直匹配到URL尾部。同时生成一个 `splat` [param（参数）](#params参数)

Route pattern 都是是相对于parent route（父路由）而言的，除非是以 `/` 开头，才会从URL的开头进行匹配。

## Router

```js
type Router = {
  transitionTo: (location: Location) => void;
  pushState: (state: ?LocationState, pathname: Pathname | Path, query?: Query) => void;
  replaceState: (state: ?LocationState, pathname: Pathname | Path, query?: Query) => void;
  go(n: Number) => void;
  listen(listener: RouterListener) => Function;
  match(location: Location, callback: RouterListener) => void;
};
```

*router（路由器）*是一个 [`history`](http://rackt.github.io/history) 对象（类似于 web 浏览器中的 `window.history`），用于改变 URL 或者监听 URL 的变化。

有两个主要接口用于计算路由器的下一个 [state](#routerstate)：

- `history.listen` 在有状态且需要在一段时间后更新UI的环境（例如 web 浏览器）下使用。此方法会立即调用它的 `listener` 参数一次，然后返回一个函数，这个函数必须被调用以停止监听变化。
- `history.match` 一个纯异步函数，它不会更新 history 的内部状态。这使得它非常适合服务器端环境中，在同一时间需要处理多个任务的需求。

## RouterListener

```js
type RouterListener = (error: ?Error, nextState: RouterState) => void;
```

*router listener* 是一个用于监听 [router（路由器）](#router) 下 [state](#routerstate) 变化的函数。

## RouterState

```js
type RouterState = {
  location: Location;
  routes: Array<Route>;
  params: Params;
  components: Array<Component>;
};
```

*router state* 代表当前路由的状态，它包括了：

  - 当前的 [`location`](#location)
  - 匹配当前 location 的 [`routes（路由）`](#route) 的数组
  - 由 URL 解析生成的 [`params（参数）`](#params) 对象
  - 当前页渲染的 [`components（组件）`](#component) 组成的数组，并按照层级排列。
