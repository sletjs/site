title: 控制器
---

## 基础用法

在Slet里，一个路由就一个带有path路径的Controller，而Controller里的无参数的HTTP method是具体的verb。

```js
'use strict';

const ViewController = require('slet').ViewController

module.exports = class MyBasicController extends ViewController {
  constructor (app, ctx, next) {
    super(app, ctx, next)

    this.path = '/'
  }

  get () {
    .. show something ..
  }

  post () {
    .. create something ..
  }

  put () {
    .. replace something ..
  }

  patch () {
    .. modify something ..
  }

  delete () {
    .. annihilate something ..
  }

  options () {
    .. appease something ..
  }

  link () {
    .. affiliate something ..
  }

  unlink () {
    .. separate something ..
  }
}
```

路由是通过它们定义的顺序来匹配的。匹配的第一个路由会处理该请求。

## Controller

- 分类
    - base: 所有controller的基类
      - [BaseController](https://github.com/sletjs/BaseController)
    - basic：直接返回json或其他内容
      - [BasicController](https://github.com/sletjs/BasicController)
    - view：返回tpl和data，如果无，从this变量上获取
      - [ViewController](https://github.com/sletjs/ViewController)
    - upload：上传文件
      - [UploadController](https://github.com/sletjs/UploadController)
      - [UploadViewController](https://github.com/sletjs/UploadViewController)
    - session：会话

### 生命周期

在src/Slet.js中，调用如下

```js
// before
ctrl.before()

// only {verb} filter
ctrl.{verb}_filter = []

// execute {verb}()，比如get(){}、post(){}
ctrl.{verb}()

// after
ctrl.after()
```

- before 是{verb}()之前调用的
- after 是{verb}()之后调用的
- ctrl.{verb}_filter = [] 利用koa 2.x中间件原理，拦截{verb}()请求前和请求后

另外还提供了一个辅助定义别名的 alias 方法

alias 别名定义：如果支持bodyparser，那么把this.ctx.request.body简化为this.pp，避免特别长的方法调用。

```js
alias() {
  if (this.ctx.request.body) {
      this.pp = this.ctx.request.body
  }
}
```

all方法适用于处理所有verb的请求

如果有all方法，也有对应的verb请求，此种情况下，只会执行all()

### BaseController

```js
module.exports = class BaseController {
  constructor(app, ctx, next) {
    this.app = app
    this.ctx = ctx
    this.next = next
    this.renderType = 'default'
    this.data = {}
    this.tpl = 'index'
    
    this.result = '{verb}() call result'
  }

  // lifecycle
  before() {
    
  }
  
  alias() {
    if (this.ctx.request.body) {
        this.pp = this.ctx.request.body
    }
  }
  
  after() {
    
  }
}
```

说明

- renderType：default | view
