title:  扩展controller
---

定义自己的controller，

- 一定要注意名字，它会被用于子类继承，所以一定要见名之意。
- 继承自BaseController
- 可选实现上面的生命周期方法

```
'use strict';

const BaseController = require('slet-basecontroller')

module.exports = class BasicController extends BaseController{
  constructor(app, ctx, next) {
    super(app, ctx, next)
    
    this.query = ctx.query

    this.global_filter = ['koa-bodyparser']
  }
}
```

在app.js中使用defineController方法加载这个控制器

```
app.defineController(require('slet-basiccontroller'))
```

然后，在自己的类中

```
'use strict';

const BasicController = require('../../../').BasicController

module.exports = class SomeController extends BasicController {
  constructor(app, ctx, next) {
    super(app, ctx, next)
  }
  
  get() { 
    var a = this.query.a
    
    return {
      a:1,
      b: a
    }
  } 
}

```

Slet会自动注入依赖，所以请遵守以下约定

```
// 约定：
// 
// - 所有的模块必须以slet-开头
// - 对外暴露的class的小写，作为后面的名字
//    - 比如：BasicController，对应的模块为slet-basiccontroller
// 
```

