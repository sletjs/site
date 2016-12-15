title: 过滤器
---

过滤器(Filter)是用来拦截请求用的，一般在Controller里我们只需定义verb方法即可处理请求。但如何对请求前、请求后进行二次处理呢？

此时就需要增加过滤器。

在Sletjs中，过滤器用法非常简单，但功能却非常强大。

## 分类

- app.use 和koa的use用法是一样的
- global_filter 从全局的middlewares里按照名字取，一般定义在base类里，如果需要子类也可以重写
  - 示例this.global_filter = ['koa-bodyparser', 'koa-views']
- {verb}_filter 是特定请求verb之的拦截器，仅限于当前verb
  - 示例this.get_filter = [this.log]

## 加载顺序

> app.use > global_filter（父类默认继承） > {verb}_filter （当前文件） > {verb}() （当前文件）

## app.use 

和Koa 2.x的use用法是一样的，参数Koa 2.x的中间件，只能用于加载app级别的filter

参见 https://github.com/koajs/koa/tree/v2.x

##  global_filter说明

通过app.defineMiddleware()方法定义custom_filter（也可以写到配置里，稍后实现）

```js
app.defineMiddleware('custom_filter', function(ctx, next){
    console.log('a before')
    return next().then(function(){
      console.log('a after')
    })    
})
```

或者

```js
app.middlewares['koa-views'] = views(this.viewPath, this.opts.views.option)
```

然后在对应的类中

```js
class PathController extends BaseController {
  constructor(ctx, next) {
    super(ctx, next)
    
    this.path = '/c'
    this.global_filter.push('custom_filter')
  }

  ...
}
```

至此，完成了自定义global filter功能。

其实this.global_filter是数组，如果想调整顺序也可以的。

## {verb}_filter说明

在构造函数里指定filter，所谓的filter即Koa 2.x的中间件，规则是verb + '_filter'

举例：

- this.get_filter = [] 已测
- this.post_filter = [] 已测

此filter是请求发起之后才生效，所以测试请发起http请求。

```js
'use strict';

const ApiController = require('..').Base

class PathController extends ApiController {
  constructor(ctx, next) {
    super(ctx, next)
    
    this.get_filter = [this.log]
  }
  
  log(ctx, next){
    console.log('before')
    return next().then(function(){
      console.log('after')
    })
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

这里log即Koa 2.x中间件经典写法

```js
 log(ctx, next){
    console.log('before')
    return next().then(function(){
      console.log('after')
    })
  }
```

在处理get之前，会走next()方法之前的内容。在处理完get之后，会有next后的then部分。可以有效的拦截前后2种，所以filter无需分前后，一个即可。
