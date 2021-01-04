---
title: 前端vue优化
date: 2019-09-18 20:15:22
tags:
    - vue
    - vuex
    - axios
categories: 
    - 个人博客搭建
---
> 上一节我们用django和vue构建了基础的实现了前后端分离的项目开发环境，本节我们将为前端架构进行一定地优化，让我们可以更加优雅地进行前端的编写。

## 1、vuex

首先在frontend目录下运行以下命令安装vuex：

```bash
npm install vuex --save # 顺带一提，加了--save后运行npm install初始化项目时就会下载该模块，方便部署
```

在项目根目录/frontend/src目录下增加store文件夹，存放vuex的全局状态管理的各个模块。

在store下依次创建如下文件：

```bash
store
├── index.html
└── modules // vuex的各个子模块
```

在store/index.html中加入如下代码：

```javascript
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
const files = require.context('./modules', false, /\.js$/)
const modules = {}

files.keys().forEach(key => {
  modules[key.replace(/(\.\/|\.js)/g, '')] = files(key).default
})

export default new Vuex.Store({
  modules: {
    modules
  }
})
```

这样一来便可以将store/modules中的所有子模块都export到store/index.js的出口中了。

最后再在程序入口(frontend/main.js)中引入这些子模块：

```javascript
import Vuex from 'vuex' // vuex
// store
import store from '@/store/index'
// ...
Vue.use(Vuex)
new Vue({
  // ...
  store,
  render: h => h(App)
}).$mount('#app')
```

如此一来我们便可以优雅地在modules(frontend/src/store/modules)中编写和创建vuex子模块了。

## 2、axios

首先安装axios，在项目frontend目录下运行：

```bash
npm install axios
```

然后在frontend/src下创建plugin目录，用来存放插件，并创建axios/index.js目录：

```bash
plugin
└── axios
		└── index.js
```

在index.js中创建并export出axios实例：

```javascript
import axios from 'axios'

// 创建一个 axios 实例
const service = axios.create({
	// ...
})
export default service
```

然后在frontend/src下创建api目录，用来存放各种各种请求api：

```bash
src
└── api
		└── ....js
```

api具体编写如下：

```javascript
import request from '@/plugin/axios'

export function test1 () {
  return request({
    url: 'test/',
    method: 'get'
  })
}

export function test2 (data) {
  return request({
    url: 'test/',
    method: 'post',
    data
  })
}
```

这样便将axios实例，和请求的代码分离了出来，在实际使用的时候只先在api中编写好对应的请求方法，然后在页面中优雅地调用即可，例：

```javascript
test2({
  params: ...
})
  .then(res => {
    console.log(res.data)
		// ...
  })
```

## 3、路由配置

安装，frontend下运行：

```bash
npm install vue-router --save
```

在frontend/src下创建router目录用来管理vue-router，并在router下创建如下目录：

```
router
├── index.js
└── routes.js
```

首先在index.js中注册组件并引入routes.js中记录的路由信息，并可利用[路由守卫](https://www.jianshu.com/p/dcf5ce5f3ed7)来增加相应的路由拦截，进行权限验证，标题修改等操作：

```javascript
import Vue from 'vue'
import VueRouter from 'vue-router'
// 路由数据
import routes from './routes'

Vue.use(VueRouter)

// 导出路由 在 main.js 里使用
const router = new VueRouter({
  routes
})
/**
 * 路由拦截
 * 权限验证
 */
router.afterEach(async (to, from, next) => {
  /* 路由发生变化修改页面title */
  if (to.meta.title) {
    document.title = to.meta.title
  } else {
    document.title = '大头博客'
  }
})
export default router
```

然后在routes.js中编写具体的路由信息：

```js
import nav from '@/components/navigation-bar'
// 简化加载路径
const _import = file => () => import('@/views/' + file)
// 主框架
const frameIn = [
  {
    path: '/',
    redirect: { name: 'index' },
    component: nav,
    children: [
      // 首页
      {
        path: 'index',
        name: 'index',
        meta: {
          title: '...'
        },
        component: _import('pages/index')
      },
      // ...
    ]
  }
]
export default [
  ...frameIn
]
```

在程序入口文件(main.js)中引入该配置：

```js
import router from './router' // 路由设置
//...
new Vue({
  router,
  //...
  render: h => h(App)
}).$mount('#app')
```

---

> 有了vuex管理全局状态和方法，axios进行前后端数据交互，vue-router进行前端路由管理，前端的