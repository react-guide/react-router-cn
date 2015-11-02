# 在组件外部使用导航

虽然在路由组件内部，可以获取 `this.props.history` 来实现导航。也可以引入 `History` mixin，并用它提供的
 `this.history` 来实现导航。然而，很多应用仍然希望可以在他们的组件外部使用导航功能。


这个非常简单，要做的就是拿到你的 history 对象：

你可以在应用的任何地方，创建一个模块来输出你的 history 对象。


```js
// history.js
import createBrowserHistory from 'history/lib/createBrowserHistory'
export default createBrowserHistory()
```

然后引入这个模块渲染一个 `<Router>`:

```js
// index.js
import history from './history'
React.render(<Router history={history}/>, el)
```

现在你可以在应用的其他地方使用这个 history 对象了，例如一个 flux action 文件


```js
// actions.js
import history from './history'
history.replaceState(null, '/some/path')
```
