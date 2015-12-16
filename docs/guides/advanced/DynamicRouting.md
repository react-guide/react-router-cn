# 动态路由

React Router 适用于小型网站，比如 [React.js Training](https://reactjs-training.com)，也可以支持 [Facebook](https://www.facebook.com/) 和 [Twitter](https://twitter.com/) 这类大型网站。

对于大型应用来说，一个首当其冲的问题就是所需加载的 JavaScript 的大小。程序应当只加载当前渲染页所需的 JavaScript。有些开发者将这种方式称之为“代码分拆” —— 将所有的代码分拆成多个小包，在用户浏览过程中按需加载。

对于底层细节的修改不应该需要它上面每一层级都进行修改。举个例子，为一个照片浏览页添加一个路径不应该影响到首页加载的 JavaScript 的大小。也不能因为多个团队共用一个大型的路由配置文件而造成合并时的冲突。

路由是个非常适于做代码分拆的地方：它的责任就是配置好每个 view。

React Router 里的[路径匹配](/docs/guides/basics/RouteMatching.md)以及组件加载都是异步完成的，不仅允许你延迟加载组件，**并且可以延迟加载路由配置**。在首次加载包中你只需要有一个路径定义，路由会自动解析剩下的路径。

Route 可以定义 [`getChildRoutes`](/docs/API.md#getchildrouteslocation-callback)，[`getIndexRoute`](/docs/API.md#getindexroutelocation-callback) 和 [`getComponents`](/docs/API.md#getcomponentslocation-callback) 这几个函数。它们都是异步执行，并且只有在需要时才被调用。我们将这种方式称之为 “逐渐匹配”。 React Router 会逐渐的匹配 URL 并只加载该 URL 对应页面所需的路径配置和组件。

如果配合 [webpack](http://webpack.github.io/) 这类的代码分拆工具使用的话，一个原本繁琐的构架就会变得更简洁明了。

```js
const CourseRoute = {
  path: 'course/:courseId',

  getChildRoutes(location, callback) {
    require.ensure([], function (require) {
      callback(null, [
        require('./routes/Announcements'),
        require('./routes/Assignments'),
        require('./routes/Grades'),
      ])
    })
  },

  getIndexRoute(location, callback) {
    require.ensure([], function (require) {
      callback(null, require('./components/Index'))
    })
  },

  getComponents(location, callback) {
    require.ensure([], function (require) {
      callback(null, require('./components/Course'))
    })
  }
}
```
现在，可以看一下 webpack 都做了哪些。开玩笑，我现在不想让你伤心。

运行 [huge apps](https://github.com/rackt/react-router/tree/master/examples/huge-apps) 实例，打开浏览器的审查元素选项。你会发现在路由发生改变时，资源按需加载。
