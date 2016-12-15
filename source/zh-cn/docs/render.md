title: 渲染
---

目前支持2种

- 基本类型
- view(模板引擎)

即


```
this.renderType = 'view' | 'default'
```

## 基本类型

```
this.renderType = 'default'
```

### text

```
  get() {
    var a = this.query.a
    console.log(a)
    return `${a}`
```

### html

```
  get() {
    var a = this.query.a
    console.log(a)
    return `<h1>${a}</h1>`
```


### json

```
  get() {
    var a = this.query.a
    console.log(a)
    return {
      dddd:1,
      b: a
    }
  } 
```

## View (模板引擎)

结合模板引擎来渲染view的示例


```
this.renderType = 'view'
```

### 安装slet模块

```
$ npm i -S slet
$ npm i -S slet-viewcontroller
$ npm i -S nunjucks
```

### 从app.js开始

```
'use strict';

const Slet = require('slet');
const app = new Slet({
    root: __dirname,
    debug: true
});

app.defineController(require('slet-viewcontroller'))

app.router('/', require('./viewctrl') )  

app.start(3000) 
```

如果想定制，可以通过配置项来实现，这里采用系统默认的nunjucks作为模板引擎

```
const app = new Slet({
    root: __dirname,
    debug: true,
    "views" :{
        "path" : ".",
        "option": { "map": {"html": "nunjucks" }}
    },
});

```

### 编写viewctrl.js

```
'use strict';

const ViewController = require('slet').ViewController

module.exports = class MyViewController extends ViewController {
  constructor(app, ctx, next) {
    super(app, ctx, next)
  }
  
  get() { 
    var a = this.query.a
    // this.renderType='view'
    return {
      tpl: 'index',
      data: {
        title: 'ssddssdd a= '+a
      }
    }
  } 
}

```

### 创建index.html

```
<h1>{{title}}</h1>
```

如果不熟悉nunjucks模板引擎，请查看https://mozilla.github.io/nunjucks/

### 启动server

最后，执行app.js，启动server

```
$ node app.js
```

### 查验结果

在浏览器中打开 http://127.0.0.1:3000/?a=2

## 扩展

```
this.renderType = 'view'
```

根据renderType，以及其他方法

- this.write
- this.end