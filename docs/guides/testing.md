React Router Testing With Jest
====================
自从 React Router 1.x 版本的发布，测试就变得简单多了。想知道 React Router 以前版本的测试，请看 [旧的测试文档](https://github.com/rackt/react-router/blob/57543eb41ce45b994a29792d77c86cc10b51eac9/docs/guides/testing.md)。

在开始之前，建议你阅读以下前两个教程：
- [Jest Getting Started docs](https://facebook.github.io/jest/docs/getting-started.html)
- [Jest ReactJS docs](https://facebook.github.io/jest/docs/tutorial-react.html)
- [ReactJS TestUtils docs](https://facebook.github.io/react/docs/test-utils.html)

可以使用 React-Router 1.x 进行测试。但如果你有以下的问题。许多用户有这类问题，是因为之前配置从 react-router 0.x 升级了而造成的。

从 React-Router 0.x 到 1.x 带来的测试配置更新
----------------------------------------------
首先，确保每个你正在使用的包，至少是以下这些版本的：
- `"react": "^0.14.0"`
- `"react-dom": "^0.14.0"`
- `"react-router": "^1.0.0"`
- `"react-addons-test-utils": "^0.14.0"`
- `"jest-cli": "^0.5.10"`
- `"babel-jest": "^5.3.0"`

另外，确保你使用的是 node 4.x

在以前的配置中，react-tools 是必须的。但现在不是了。你要把它从 package.json 和环境中删除。

```json
"react-tools": "~0.13.3",
```

最后，你有任何类似如下的，需要将：

```js
var React = require('react/addons');
var TestUtils = React.addons.TestUtils;
```

替换成：

```js
var TestUtils = require('react-addons-test-utils');
var ReactDOM = require('react-dom');
var React = require('react');
```

确保你做了 npm clean、install 等等操作。并且确保在你的非模拟路径上添加了 react-addons-test-utils 和 react-dom to your 。

```json
  ...
  "unmockedModulePathPatterns": [
    "./node_modules/react",
    "./node_modules/react-dom",
    "./node_modules/react-addons-test-utils",
  ],
  ...

```

最后，在脚本预处理之前保证你使用了 babel-jest：

```js
  ...
  "scriptPreprocessor": "./node_modules/babel-jest",
  ...
```


示例：
----------------------------------------------
一个组件：
```js
//../components/BasicPage.js

let React = require('react');

import { Button } from 'react-bootstrap';
import { Link } from 'react-router';

let BasicPage =
  React.createClass({
    propTypes: {
      authenticated: React.PropTypes.bool,
    },
    render:function(){
      let mainContent;
      let authenticated = this.props.authenticated;

      if(authenticated) {
        mainContent = (
          <div>
            <Link to="/admin"><Button bsStyle="primary">Login</Button></Link>
          </div>
        );
      } else {
        mainContent = (
          <div>
            <Link to="/admin"><Button bsStyle="primary">Login</Button></Link>
          </div>
        );

      }

      return (
        <div>
          {mainContent}
        </div>
      );
    },
  });
module.exports = BasicPage;
```

测试该组件：
```js
//../components/__tests__/BasicPage-test.js

// 注意：不能使用 es6 模块的语法，因为
// jest.dontMock & jest.autoMockOff()
// 还不支持 ES6 模块

jest.dontMock('../BasicPage');

describe('BasicPage', function() {
  let BasicPage = require('../BasicPage');
  let TestUtils = require('react-addons-test-utils');
  let ReactDOM = require('react-dom');
  let React = require('react');

  it('renders the Login button if not logged in', function() {
    let page = TestUtils.renderIntoDocument(<BasicPage />);
    let button = TestUtils.findRenderedDOMComponentWithTag(page, 'button');
    expect(ReactDOM.findDOMNode(button).textContent).toBe('Login');
  });

  it('renders the Account button if logged in', function() {
    let page = TestUtils.renderIntoDocument(<BasicPage authenticated={true} />);
    let button = TestUtils.findRenderedDOMComponentWithTag(page, 'button');
    expect(ReactDOM.findDOMNode(button).textContent).toBe('Your Account');
  });
});
```
