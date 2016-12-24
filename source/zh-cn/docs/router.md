title: Router
---


## 手动指定

（controller内部不需要path）

```js
app.router('/', require('./ctrl') )  
```

or

```js
app.router('/', './ctrl')  
```

已测

## 将path写到controller里

### 2.1 通过对象（可以实现，但不如通过路径方便）

如果依赖已安装，即`app.defineController(require('slet-viewcontroller'))`，这样才加载的时候才不会报错。

```js
app.router(require('./pathctrl') )  
```

如果没有装依赖，那么需要在asyncRouter里注册这样的路由。asyncRouter会自动检测root下面的所有依赖，并提前注入。


```js
app.asyncRouter(function () {
   app.router('/2', require('./viewctrl') )  
})
```


### 2.2 通过路径（推荐）

```js
app.router('./pathctrl')  
```

已测

pathctrl代码

```js
'use strict';

const ApiController = require('..').Base

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

PathController.path = '/b'

module.exports = PathController
```

### 优先级

> app.router('/d', './controller/a')  >　this.path= '/ｃ' > Controller.path='/b'

亦即

> '/d' > '/c' > '/b


## 指定路径，自动挂载

```js
app.routerDir('app/controller' )  
```

此种情况会默认加载某个目录下的controller，请确保你的controller里有path，无论是属性，还是static属性方式都行。

已测



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

## Route paths

Here are some examples of route paths based on strings.

This route path will match requests to the root route, /.

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = '/'
  }
  
  get () {
    return 'root'
  } 
}

// express
app.get('/', function (req, res) {
  res.send('root')
})
```

This route path will match requests to /about.

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = '/about'
  }
  
  get () {
    return 'about'
  } 
}

// express
app.get('/about', function (req, res) {
  res.send('about')
})
```

This route path will match requests to /random.text.

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = '/random.text'
  }
  
  get () {
    return 'random.text'
  } 
}

// express
app.get('/random.text', function (req, res) {
  res.send('random.text')
})
```

Here are some examples of route paths based on string patterns.

This route path will match acd and abcd.

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = '/ab?cd'
  }
  
  get () {
    return 'ab?cd'
  } 
}

// express
app.get('/ab?cd', function (req, res) {
  res.send('ab?cd')
})
```

This route path will match abcd, abbcd, abbbcd, and so on.

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = '/ab+cd'
  }
  
  get () {
    return 'ab+cd'
  } 
}

// express
app.get('/ab+cd', function (req, res) {
  res.send('ab+cd')
})
```

This route path will match abcd, abxcd, abRANDOMcd, ab123cd, and so on.

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = '/ab*cd'
  }
  
  get () {
    return 'ab*cd'
  } 
}

// express
app.get('/ab*cd', function (req, res) {
  res.send('ab*cd')
})
```

This route path will match /abe and /abcde.

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = '/ab(cd)?e'
  }
  
  get () {
    return 'ab(cd)?e'
  } 
}

// express
app.get('/ab(cd)?e', function (req, res) {
  res.send('ab(cd)?e')
})
```

Examples of route paths based on regular expressions:

This route path will match anything with an “a” in the route name.

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = /a/
  }
  
  get () {
    return '/a/'
  } 
}

// express
app.get(/a/, function (req, res) {
  res.send('/a/')
})
```

This route path will match butterfly and dragonfly, but not butterflyman, dragonflyman, and so on.

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = /.*fly$/
  }
  
  get () {
    return '/.*fly$/'
  } 
}

// express
app.get(/.*fly$/, function (req, res) {
  res.send('/.*fly$/')
})
```

### 参考资料

只要https://github.com/pillarjs/path-to-regexp支持的路由都可以的。