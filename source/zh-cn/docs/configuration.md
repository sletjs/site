title: 配置
---

```js
module.exports = {
    "debug": false,
    "prefix": "",
    "views":{
        "path": "/", 
        "extension": "html"
    },
    "automount": {
        "path": "controllers",
        "option": {
            "recurse": true
        }
    },
    "mockCtx": {
        "request":{
            "body": {

            }
        }
    },
    "logger": {
      // Define a custom request id function
      genReqId: function (req) { return req.id },

      // Logger level is `info` by default
      useLevel: 'trace'
    }
}
```

## 日志

采用[koa-pino-logger](https://github.com/pinojs/koa-pino-logger/tree/v2)

- res.log.info("xxx")
- req.log.error("xxx")
- this.ctx.log.debug("xxx")