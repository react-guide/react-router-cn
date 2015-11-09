# Component Lifecycle
# 组件生命周期

It's important to understand which lifecycle hooks are going to be called
on your route components to implement lots of different functionality in
your app. The most common thing is fetching data.

在开发应用时，理解路由组件的生命周期是非常重要的。后面我们会以获取数据这个最常见的场景为例，介绍一下路由改变时，路由组件生命周期的变化情况。

There is no difference in the lifecycle of a component in the router as
just React itself. Let's peel away the idea of routes, and just think
about the components being rendered at different URLs.

路由组件的生命周期和 React 组件相比并没有什么不同。现在让我们设置一些路由，然后思考一下在不同的 URL 下，这些组件是如何被渲染的。

Consider this route config:

路由配置如下：

```js
<Route path="/" component={App}>
  <IndexRoute component={Home}/>
  <Route path="invoices/:invoiceId" component={Invoice}/>
  <Route path="accounts/:accountId" component={Account}/>
</Route>
```

### Lifecycle hooks when routing

### 路由切换时，组件生命周期的变化情况

1. Lets say the user enters the app at `/`.
1. 当用户打开应用的 '/' 页面

    | 组件 | 生命周期 |
    |-----------|------------------------|
    | App | `componentDidMount` |
    | Home | `componentDidMount` |
    | Invoice | N/A |
    | Account | N/A |

2. Now they navigate from `/` to `/invoice/123`
2. 当用户从 '/' 跳转到 '/invoice/123'

    | 组件 | 生命周期 |
    |-----------|------------------------|
    | App | `componentWillReceiveProps`, `componentDidUpdate` |
    | Home | `componentWillUnmount` |
    | Invoice | `componentDidMount` |
    | Account | N/A |

    - `App` gets `componentWillReceiveProps` and `componentDidUpdate` because it
    stayed rendered but just received new props from the router (like:
    `children`, `params`, `location`, etc.)
    - `App` 从 router 中接收到新的 props（例如 `children`、`params`、`location` 等数据,所以 `App` 触发了 `componentWillReceiveProps` 和 `componentDidUpdate` 两个生命周期方法
    - `Home` is no longer rendered, so it gets unmounted.
    - `Home` 不再被渲染，所以它将被移除
    - `Invoice` is mounted for the first time.
    - `Invoice` 首次被挂载


3. Now they navigate from `/invoice/123` to `/invoice/789`
3. 当用户从 `/invoice/123` 跳转到 `/invoice/789`

    | 组件 | 生命周期|
    |-----------|------------------------|
    | App | componentWillReceiveProps, componentDidUpdate |
    | Home | N/A |
    | Invoice | componentWillReceiveProps, componentDidUpdate |
    | Account | N/A |

    All the components that were mounted before, are still mounted, they
    just receive new props from the router.
    所有的组件之前都已经被挂载，所以只是从 router 更新了 props.

4. Now they navigate from `/invoice/789` to `/accounts/123`
4. 当从 `/invoice/789` 跳转到 `/accounts/123`

    | 组件 | 生命周期|
    |-----------|------------------------|
    | App | componentWillReceiveProps, componentDidUpdate |
    | Home | N/A |
    | Invoice | componentWillUnmount |
    | Account | componentDidMount |

### Fetching Data

While there are other ways to fetch data with the router, the simplest
way is to simply use the lifecycle hooks of your components and keep
that data in state. Now that we understand the lifecycle of components
when changing routes, we can implement simple data fetching inside of
`Invoice`.

虽然还有其他通过 router 获取数据的方法，但是最简单的方法是通过组件生命周期 Hook 来实现。前面我们已经理解了当路由改变时组件生命周期的变化，我们可以在 `Invoice` 组件里实现一个简单的数据获取功能。

```js
let Invoice = React.createClass({

  getInitialState () {
    return {
      invoice: null
    }
  },

  componentDidMount () {
    // fetch data initially in scenario 2 from above
    // 上面的步骤2，在此初始化数据
    this.fetchInvoice()
  },

  componentDidUpdate (prevProps) {
    // respond to parameter change in scenario 3
    // 上面步骤3，通过参数更新数据
    let oldId = prevProps.params.invoiceId
    let newId = this.props.params.invoiceId
    if (newId !== oldId)
      this.fetchInvoice()
  },

  componentWillUnmount () {
    // allows us to ignore an inflight request in scenario 4
    // 上面步骤四，在组件移除前忽略正在进行中的请求
    this.ignoreLastFetch = true
  },

  fetchInvoice () {
    let url = `/api/invoices/${this.props.params.invoiceId}`
    this.request = fetch(url, (err, data) => {
      if (!this.ignoreLastFetch)
        this.setState({ invoice: data.invoice })
    })
  },

  render () {
    return <InvoiceView invoice={this.state.invoice}/>
  }

})
```
