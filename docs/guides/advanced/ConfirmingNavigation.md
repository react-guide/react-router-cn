# 跳转前确认

React Router 提供一个 [`routerWillLeave` 生命周期拦截](/docs/Glossary.md#routehook) 的方法，这使得 React [组件](/docs/Glossary.md#component)可以阻止正在发生的跳转，或在离开 [route](/docs/Glossary.md#route) 前提示用户。[`routerWillLeave`](/docs/API.md#routerwillleavenextlocation) 可能是以下的之一：

1. `return false` 取消此次跳转或
2. `return` 提示信息，提示用户需要在离开 route 进行确认。

用 `Lifecycle` mixin 其中一个 [route 组件](/docs/Glossary.md#routecomponent)，进而安装这个 hook。

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

如果你需要一个 [`routerWillLeave`](/docs/API.md#routerwillleavenextlocation) 去 hook 深层次的嵌套组件，这很简单，使用  [`RouteContext`](/docs/API.md#routecontext-mixin) mixin 你的 [route 组件](/docs/Glossary.md#routecomponent)，然后把 route 放到 context 中。

```js
import { Lifecycle, RouteContext } from 'react-router'

const Home = React.createClass({

  // 为了 descendants 能进一步继承这种层次结构，
  // Home 应该在 context 中提供它的 route。
  mixins: [ RouteContext ],

  render() {
    return <NestedForm />
  }

})

const NestedForm = React.createClass({

  // Descendants 使用 Lifecycle mixin 去获得
  // 一个 routerWillLeave 的方法。
  mixins: [ Lifecycle ],

  routerWillLeave(nextLocation) {
    if (!this.state.isSaved)
      return 'Your work is not saved! Are you sure you want to leave?'
  },

  // ...

})
```
