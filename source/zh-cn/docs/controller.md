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

  get (req, res) { 
    .. show something ..
  }

  post (req, res) { 
    .. create something ..
  }

  put (req, res) { 
    .. replace something ..
  }

  patch (req, res) { 
    .. modify something ..
  }

  delete (req, res) { 
    .. annihilate something ..
  }

  options (req, res) { 
    .. appease something ..
  }

  link (req, res) { 
    .. affiliate something ..
  }

  unlink (req, res) { 
    .. separate something ..
  }
}
```

路由是通过它们定义的顺序来匹配的。匹配的第一个路由会处理该请求。

## verb方法写法

slet支持2种写法，显式声明

```js
  get (req, res) { 
    req.xxx
    .. show something ..
  }
```

和隐式声明

```js
  get () { 
    this.xxxx
    .. show something ..
  }
```


## 显式声明verb方法的参数req和res

请求（koa Request的都可以用）

> req = ctx.request : A koa Request object.

响应（koa Response的都可以用）

> res = ctx.response : A koa Response object.

比如

- req.query   (koa内置)
- req.body    (slet内置)
- req.params  (slet内置)

比如
  
- res.body    (koa内置)
- res.status  (koa内置)

## 构造方法

```js
  constructor(app, ctx, next) {
    super(app, ctx, next)
    ...
  }
```

一般不用重写，如果重写，请记得`super(app, ctx, next)`

比较典型的，比如配置请求path的时候

```js
  constructor (app, ctx, next) {
    super(app, ctx, next)
    this.path = '/'
  }
```

## all方法

```js
  all (req, res) { 
    .. show something ..
  }
```

拦截所有verb对应的请求，一般测试用。

如果同时存在all或其他verb方法，由all方法优先响应。
  
## 配置path

### path属性

```js
class PathController extends ApiController {
  constructor(ctx, next) {
    super(ctx, next)
    
    this.path = '/c'
  }
  
  get() {
    var a = this.query.a
    console.log(a)
    return {
      dddd:1,
      b: a
    }
  } 
}

module.exports = PathController
```

### 静态path

```js
class PathController extends ApiController {
  get() {
    var a = this.query.a
    console.log(a)
    return {
      dddd:1,
      b: a
    }
  } 
}

PathController.path = '/b'

module.exports = PathController
```


## Named Parameters

Route patterns may include named parameters, accessible via the params hash:

```js
'use strict';

const ViewController = require('slet').ViewController

module.exports = class MyBasicController extends ViewController {
  constructor (app, ctx, next) {
    super(app, ctx, next)

    this.path = '/hello/:name'
  }

  get () {
    // matches "GET /hello/foo" and "GET /hello/bar"
    // params['name'] is 'foo' or 'bar'
    return `Hello ${this.params['name']}!`
  }
}
```

## Modified Parameters

### Optional

Parameters can be suffixed with a question mark (?) to make the parameter optional. This will also make the prefix optional.

```js
var re = pathToRegexp('/:foo/:bar?', keys)
// keys = [{ name: 'foo', ... }, { name: 'bar', delimiter: '/', optional: true, repeat: false }]

re.exec('/test')
//=> ['/test', 'test', undefined]

re.exec('/test/route')
//=> ['/test', 'test', 'route']
```

### Zero or more

Parameters can be suffixed with an asterisk (*) to denote a zero or more parameter matches. The prefix is taken into account for each match.

```js
var re = pathToRegexp('/:foo*', keys)
// keys = [{ name: 'foo', delimiter: '/', optional: true, repeat: true }]

re.exec('/')
//=> ['/', undefined]

re.exec('/bar/baz')
//=> ['/bar/baz', 'bar/baz']
```

### One or more

Parameters can be suffixed with a plus sign (+) to denote a one or more parameter matches. The prefix is taken into account for each match.

```js
var re = pathToRegexp('/:foo+', keys)
// keys = [{ name: 'foo', delimiter: '/', optional: false, repeat: true }]

re.exec('/')
//=> null

re.exec('/bar/baz')
//=> ['/bar/baz', 'bar/baz']
```

### Custom Match Parameters

All parameters can be provided a custom regexp, which overrides the default ([^\/]+).

```js
var re = pathToRegexp('/:foo(\\d+)', keys)
// keys = [{ name: 'foo', ... }]

re.exec('/123')
//=> ['/123', '123']

re.exec('/abc')
//=> null
```

Please note: Backslashes need to be escaped with another backslash in strings.

### 参考资料

只要https://github.com/pillarjs/path-to-regexp支持的路由都可以的。


## Controller

- 分类
    - base: 所有controller的基类
      - [BaseController](https://github.com/sletjs/BaseController)
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

if (ctrl['all']) 就执行ctrl['all'](req, res, next)

// only {verb} filter
ctrl.{verb}_filter = []

// execute {verb}()，比如get(){}、post(){}
ctrl.{verb}(req, res, next)

// after
ctrl.after()
```

- before 是{verb}()之前调用的
- after 是{verb}()之后调用的
- ctrl.{verb}_filter = [] 利用koa 2.x中间件原理，拦截{verb}()请求前和请求后

all方法适用于处理所有verb的请求，如果有all方法，也有对应的verb请求，此种情况下，只会执行all()

### 重写生命周期

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

  before() {
    ...
  }

  after() {
    ...
  }
}
```

