title: 插件
---


已有

- [slet-plugin-static](https://github.com/sletjs/slet-plugin-static)

### 方法1


```
'use strict'

const StaticPlugin = require('slet-plugin-static')

const Slet = require('slet')

// 经典用法
Slet.plugin(StaticPlugin)

const app = new Slet({
  root: __dirname,
  debug: false
})

// lazy load plugin
app.static()

// app.start(3005)
module.exports = app

```

步骤

- 1）const StaticPlugin = require('.')
- 2）Slet.plugin(StaticPlugin)
- 3) app.static()
### 方法2

```
'use strict';

const StaticPlugin = require('slet-plugin-static')

const Slet = require('slet');

// 经典用法
app.plugin(StaticPlugin)

const app = new Slet({
    root: __dirname,
    debug: true
});

app.static()

app.start(3000)
```

### 插件定义方法

```
module.exports = class StaticPlugin {

  static () {
    if (!this.opts.static) {
      this.opts.static = {
        path: this.opts.root + '/public',
        opts: {} // https://github.com/koajs/static#options
      }
    }
    
    this.use(require('koa-static')(this.opts.static.path, this.opts.static.opts));
  }
}
```

说明：

- this.opts是从slet的构造函数里传入的，可以多处改写
- this.use即koa 2.x的use方法，传中间件即可
- 只有用到static()方法的时候，才加载require('koa-static')，懒加载


