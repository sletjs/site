title: HTTP基础
---
## 处理请求参数的3种方法

### QueryString

当curl命令发起Post测试请求，带有name参数

```
curl http://127.0.0.1:6001/posts?a=1&b=2
```

此时，你可以通过

```js
get () {
  let a = this.query.a
  let b = this.query.b
}

post () {
  let a = this.query.a
  let b = this.query.b
}
```

QueryString适用于任何情况，所以this.query常常和另外的this.params和this.body组合使用。

### 路由参数

``` js
posts/:id => posts/89
```

此时，你可以通过

```js
get () {
  let id = this.params.id
  // 此时id = 89
}

```

这个只有在路由path里有占位（具名路由）才有用。

### Body

对于Post、Put、Patch、Delete、Link、Unlink等需要解析表单内容的verb，你需要是使用this.body

当curl命令发起Post测试请求，带有name参数

```
curl -d "name='foo'" http://127.0.0.1:6001/c
```

处理如下

```js
post () {
  let _name = this.body.name
  // 此时_name = 'foo'
}

```

## Post

首先，先了解一下关于http协议里定义的四种常见数据的post方法，分别是： 

- application/www-form-ulrencoded 
- multipart/form-data 
- application/json 
- text/xml


## 上传

- UploadController
- UploadViewController

### UploadController

```js
$ npm i -S slet
$ npm i -S slet-uploadcontroller
```

编写uploadctrl.js

```js
'use strict';

const UploadController = require('slet').UploadController

module.exports = class MyUploadController extends UploadController {
  constructor(app, ctx, next) {
    super(app, ctx, next)
    
    this.post_filter = [this.upload.single('avatar')]
  }
  
  post() { 
    return {
      msg: 'this is a upload'
    }
  } 
}
```

这里使用了post_filter拦截器，具体参加文档。

这里使用的单一文件，更多方法参见

- https://github.com/expressjs/multer
- https://github.com/koa-modules/multer

### UploadViewController

```js
$ npm i -S slet
$ npm i -S slet-viewcontroller
$ npm i -S nunjucks
$ npm i -S slet-uploadviewcontroller
```

编写uploadctrl.js

```js
'use strict';

const UploadViewController = require('slet').UploadViewController

module.exports = class MyUploadController extends UploadViewController {
  constructor(app, ctx, next) {
    super(app, ctx, next)
    
    this.post_filter = [this.upload.single('avatar')]
  }
  
  post() { 
    return {
      tpl: 'index',
      data: {
        title: 'this a upload view'
      }
    }
  } 
}

```

如果通过`this.renderType = 'default'`也可以实现UploadController的效果

### 示例

- [example-upload](https://github.com/sletjs/example-upload)
- [example-upload-view](https://github.com/sletjs/example-upload-view)


## 总结



