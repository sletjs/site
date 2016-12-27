title: 测试
---

supertest是express和koa里常用的api测试工具，非常简单，方便，如果在slet中想集成supertest也是非常简单的，原因是slet的listen和run方法返回的koa的app.listen，所以集成方式是一样的。为了简单，这里提供slettest简单封装。

## sletTest

```js
$ npm i -D slettest
```

## app.js

```js
'use strict';

const Slet = require('slet');
const app = new Slet({
    root: __dirname,
    debug: true
});

// response
app.use(ctx => {
  ctx.body = 'Hello Koa'
})

module.exports = app
```

## 测试代码

```js
import test from 'ava'

var app = require('./app')
var superkoa = require('.')

test.cb('superkoa()', t => {
  superkoa(app)
    .get('/')
    .expect(200, function (err, res) {
      t.ifError(err)
      t.is(res.text, 'Hello Koa', 'res.text == Hello Koa')
      t.end()
    })
})

test('yield superkoa()', function * (t) {
  let res = yield superkoa(app)
    .get('/')

  t.is(200, res.status)
  t.is(res.text, 'Hello Koa', 'res.text == Hello Koa')
})

test('async superkoa()', async (t) => {
  let res = await superkoa(app)
    .get('/')

  t.is(200, res.status)
  t.is(res.text, 'Hello Koa', 'res.text == Hello Koa')
})

```

这里使用的ava测试框架，其实mocha、jest等也都是可以的。

## 更多文档

参见 http://visionmedia.github.io/superagent/
