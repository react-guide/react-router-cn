# 跳转前确认

React Router 提供一个 [`routerWillLeave` 生命周期钩子](/docs/Glossary.md#routehook)，这使得 React [组件](/docs/Glossary.md#component)可以拦截正在发生的跳转，或在离开 [route](/docs/Glossary.md#route) 前提示用户。[`routerWillLeave`](/docs/API.md#routerwillleavenextlocation) 返回值有以下两种：

1. `return false` 取消此次跳转
2. `return` 返回提示信息，在离开 route 前提示用户进行确认。

你可以在 [route 组件](/docs/Glossary.md#routecomponent) 中引入 `Lifecycle` mixin 来安装这个钩子。

```js
import { Lifecycle } from 'react-router'

const Home = React.createClass({

  // 假设 Home 是一个 route 组件，它可能会使用
  // Lifecycle mixin 去获得一个 routerWillLeave 方法。
  mixins: [ Lifecycle ],

  routerWillLeave(nextLocation) {
    if (!this.state.isSaved)
      return 'Your work is not saved! Are you sure you want to leave?'
  },

  // ...

})
```
如果你在组件中使用了 ES6 类，你可以借助 [react-mixin](https://github.com/brigand/react-mixin) 包将 `Lifecycle` mixin 添加到组件中，不过我们推荐使用 `React.createClass` 来创建组件，初始化路由的生命周期钩子函数。

如果你想在一个深层嵌套的组件中使用 [`routerWillLeave`](/docs/API.md#routerwillleavenextlocation) 钩子，只需在 [route 组件](/docs/Glossary.md#routecomponent) 中引入 [`RouteContext`](/docs/API.md#routecontext-mixin) mixin，这样就会把 `route` 放到 context 中。

```js
import { Lifecycle, RouteContext } from 'react-router'

const Home = React.createClass({

  // route 会被放到 Home 和它子组件及孙子组件的 context 中，
  // 这样在层级树中 Home 及其所有子组件都可以拿到 route。
  mixins: [ RouteContext ],

  render() {
    return <NestedForm />
  }

})

const NestedForm = React.createClass({

  // 后代组件使用 Lifecycle mixin 获得
  // 一个 routerWillLeave 的方法。
  mixins: [ Lifecycle ],

  routerWillLeave(nextLocation) {
    if (!this.state.isSaved)
      return 'Your work is not saved! Are you sure you want to leave?'
  },

  // ...

})
```
