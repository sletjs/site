title: Router
---


## 手动知道path和controller

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

2.1 通过对象（可以实现，但不如通过路径方便）

如果依赖已安装，即`app.defineController(require('slet-viewcontroller'))`，这样才加载的时候才不会报错。

```js
app.router(require('./pathctrl') )  
```

如果没有装依赖，那么需要在asyncRouter里注册这样的路由。asyncRouter会自动检测root下面的所有依赖，并提前注入。

```js
app.asyncRouter(function(){
   app.router('/2', require('./viewctrl') )  
}) 
``` 

or

2.2 通过路径（推荐）

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

优先级

> app.router('/d', './controller/a')  >　this.path= '/ｃ' > Controller.path='/b'

亦即

> '/d' > '/c' > '/b


## 指定路径，自动挂载

```js
app.routerDir('app/controller' )  
```

此种情况会默认加载某个目录下的controller，请确保你的controller里有path，无论是属性，还是static属性方式都行。

已测

