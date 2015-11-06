# 服务端渲染

服务端渲染与客户端渲染有些许不同，因为你需要：

- 发生错误时发送一个 `500` 的响应
- 需要重定向时发送一个 `30x` 的响应
- 在渲染之前获得数据 (用 router 帮你完成这点)

为了迎合这一需求，你要在 [`<Router>`](/docs/API.md#Router) API 下一层使用：

- 使用 [`match`](/docs/API.md#matchlocation-cb) 在渲染之前根据 location 匹配 route
- 使用 `RoutingContext` 同步渲染 route 组件

它看起来像一个虚拟的 JavaScript 服务器：

```js
import { renderToString } from 'react-dom/server'
import { match, RoutingContext } from 'react-router'
import routes from './routes'

serve((req, res) => {
  // 注意！这里的 req.url 应该是从初始请求中获得的
  // 完整的 URL 路径，包括查询字符串。
  match({ routes, location: req.url }, (error, redirectLocation, renderProps) => {
    if (error) {
      res.send(500, error.message)
    } else if (redirectLocation) {
      res.redirect(302, redirectLocation.pathname + redirectLocation.search)
    } else if (renderProps) {
      res.send(200, renderToString(<RoutingContext {...renderProps} />))
    } else {
      res.send(404, 'Not found')
    }
  })
})
```

至于加载数据，你可以用 `renderProps` 去构建任何你想要的形式——例如在 route 组件中添加一个静态的 `load` 方法，或如在 route 中添加数据加载的方法——由你决定。
