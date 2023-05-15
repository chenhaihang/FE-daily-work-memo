# 1. 当vue为主项目时路由模式的区别：
## 路由模式是history时，我们在注册前端的子项目配置时的activeRule:

``` javascript
const apps = [
  {
    name: 'vue3-app',
    entry: '//localhost:8082/',
    container: '#micro-container',
    activeRule: '/vue3App'
  },
  {
    name: 'react-app',
    entry: '//localhost:3000/',
    container: '#micro-container',
    activeRule: '/react-app'
  }
]
```
react子应用的路由用BrowserRouter去创建
``` javascript
// App.js
import { Routes, Route, BrowserRouter  as Router } from 'react-router-dom'

function App () {
  return (
    <div className="App">
      {/* base设置，与主应用activeRule规则一致 */}
      <Router basename={window.__POWERED_BY_QIANKUN__ ? '/react-app' : '/'}>
        <Routes>
          <Route path="/" element={<Home />} />
        </Routes>
      </Router>
    </div>
  );
}

```

## 路由模式是hash时，我们在注册前端的子项目配置时的activeRule:
``` javascript
const getActiveRule = (hash: string) => (location:any) => {
  return location.hash.startsWith(hash)
}
const apps = [
  {
    name: 'vue3-app',
    entry: '//localhost:8082/',
    container: '#micro-container',
    activeRule: getActiveRule('#/vue3-app')
  },
  {
    name: 'react-app',
    entry: '//localhost:3000/',
    container: '#micro-container',
    activeRule: getActiveRule('#/react-app')
  }
]
```

react子应用的路由也需要对应用HashRouter去创建
``` javascript
// App.js
import { HashRouter,Routes ,Route  } from 'react-router-dom'

function App () {
  return (
    <div className="App">
      <HashRouter basename={window.__POWERED_BY_QIANKUN__ ? '/react-app' : '/'}>
        <Routes>
           <Route path="/" element={<Home />} />
         </Routes>
      </HashRouter>
    </div>
  );
}

```
