title: 第一个例子
---

## 安装slet模块

```sh
$ npm i -S slet
```

## 从app.js开始

```js
'use strict';

const Slet = require('slet');
const app = new Slet({
    root: __dirname,
    debug: true
});

app.router('/', './basicctrl')  

app.start(3000)
```

## 编写basicctrl.js

```js
'use strict';

const BasicController = require('slet').BasicController

module.exports = class MyBasicController extends BasicController {
  constructor(app, ctx, next) {
    super(app, ctx, next)
  }
  
  get() { 
    let a = this.query.a
    // this.renderType='view'
    return {
      a: 'this is a',
      b: {
        c: 'ssddssdd a= ' + a
      }
    }
  } 
}

```

## 启动server

最后，执行app.js，启动server

```sh
$ node app.js
```

## 查验结果

在浏览器中打开 http://127.0.0.1:3000/?a=2

## 更多示例

- [example-basic](https://github.com/sletjs/example-basic)
- [example-basic-autorouter](https://github.com/sletjs/example-basic-autorouter)
- [example-view](https://github.com/sletjs/example-view)
- [example-upload](https://github.com/sletjs/example-upload)
- [example-upload-view](https://github.com/sletjs/example-upload-view)