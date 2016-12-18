title: 渲染
---

目前支持2种

- 基本类型
- view(模板引擎)

即


```js
this.renderType = 'view' | 'default'
```

## 基本类型

```js
this.renderType = 'default'
```

### text

```js
  get() {
    var a = this.query.a
    console.log(a)
    return `${a}`
```

### html

```js
  get() {
    var a = this.query.a
    console.log(a)
    return `<h1>${a}</h1>`
  }
```


### json

```js
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

这里主要展示2类4种渲染方法

- this.render三种
- 自定义设置this.renderType = 'view'一种

### 安装slet模块

```js
$ npm i -S slet
$ npm i -S slet-viewcontroller
$ npm i -S nunjucks
```

### 从app.js开始

```js
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

```js
const app = new Slet({
    root: __dirname,
    debug: true,
    views:{
       path: '/', 
       extension: 'html'
    }
});

```

### 编写base.js类

```
'use strict';

const ViewController = require('slet-viewcontroller')

module.exports = class MyViewController extends ViewController {
  compile (tpl, data) {
    const vt = require('nunjucks')
    let self = this
    
    return new Promise(function(resolve, reject){
      vt.render(self.getTplPath(tpl), data, function(err, str){
          // str => Rendered HTML string
          if (err) {
            console.log(err)
            reject(err)
          }
         
          resolve(str)
      })
    })
  }
}

```

要点：

- 继承slet-viewcontroller，并实现compile方法
- compile内部使用nunjucks或其他模板引擎的进行编译
- 最后将编译结果，以Promise对象的形式返回

### 编写viewctrl.js：方式一

```js
'use strict';

const BaseViewController = require('./base')

module.exports = class MyViewController extends BaseViewController {

  get() { 
    var a = this.query.a
      
    // use default
    this.tpl = 'index'
    this.data =  {
      title: 'ssddssdd a= '+a
    }
    return this.render()
  } 
}

```

要点

- 设置tpl
- 设置data
- 调用渲染方法（无参数）

### 编写viewctrl.js：方式二

```js
'use strict';

const BaseViewController = require('./base')

module.exports = class MyViewController extends BaseViewController {

  get() { 
    var a = this.query.a

    // use tpl
    this.data = {
      title: 'ssddssdd a= '+a
    }
    return this.render('tpl')
  } 
}

```

要点

- 设置data
- 调用渲染方法（参数tpl）

如果render只有一个参数的话，它只能是tpl。

### 编写viewctrl.js：方式三

```js
'use strict';

const BaseViewController = require('./base')

module.exports = class MyViewController extends BaseViewController {

  get() { 
    var a = this.query.a
    
    // use tpl && data
    return this.render('tpl', {
      a:1
    })
  } 
}
```

要点

- 调用渲染方法（参数tpl，参数data）

如果render有二个参数的话，分别是tpl和data。

### 编写viewctrl.js：方式四

以上三种都是render方法使用说，其实也可以不用调用render就可以实现的。这就是我们要将的第四种：

```js
'use strict';

const BaseViewController = require('./base')

module.exports = class MyViewController extends BaseViewController {

  get() { 
    var a = this.query.a
    
    // custom
    this.renderType = 'view'

    return {
      tpl: 'index',
      data: {
        title: 'ssddssdd a= '+a
      }
    }
  } 
}
```

这里展示的最基本的用法。

> 一定要注意this.renderType = 'view'，否则会返回json api

如果想玩的更高级一些，可以在BaseViewController里的构造函数里配置，只要集成BaseViewController就会已模板引擎渲染。

### 创建index.html

```js
<h1>{{title}}</h1>
```

如果不熟悉nunjucks模板引擎，请查看https://mozilla.github.io/nunjucks/

### 启动server

最后，执行app.js，启动server

```js
$ node app.js
```

### 查验结果

在浏览器中打开 http://127.0.0.1:3000/?a=2

## 扩展

```js
this.renderType = 'view'
```

根据renderType，以及其他方法

- this.write
- this.end


## 使用ejs作为模板引擎

重写compile方法

```
  compile (tpl, data) {
    const ejs = require('ejs')

    let self = this

    return new Promise(function(resolve, reject){
      ejs.renderFile(tpl, data, {}, function(err, str){
          // str => Rendered HTML string
          if (err) {
            console.log(err)
            reject(err)
          }

          resolve(str)
      })
    })
  }
```