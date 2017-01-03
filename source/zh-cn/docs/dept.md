title: 依赖
---

slet依赖模块说明

## 核心

- Koa 2.x
- koa-compose是Koa 2.x的组成模块，用于拼装中间件
- debug 基础模块
- utils-merge 基础模块
- methods 基础HTTP模块
- content-disposition 基础HTTP模块
- encodeurl 基础HTTP模块
- mime 基础HTTP模块
- range-parser 基础HTTP模块
- qs 基础HTTP模块
- vary 基础HTTP模块

## 使用的Koa 2.x 插件

- koa-router 
- koa-bodyparser
- koa-pino-logger
- koa-compress
- koa-conditional-get && koa-etag
- koa-favicon
- slet-send (base on koa-send)

## slet模块依赖

```
    "debug": "^2.3.3",
    "koa": "next",
    "koa-bodyparser": "next",
    "koa-compose": "next",
    "koa-pino-logger": "^2.1.0",
    "koa-router": "next",
    "methods": "^1.1.2",
    "parsecontroller": "^1.0.1",
    "require-dir": "^0.3.1",
    "slet-basecontroller": "^1.4.4"
```

## slet-basecontroller模块依赖

```
    "content-disposition": "^0.5.2",
    "debug": "^2.4.5",
    "encodeurl": "^1.0.1",
    "koa-compress": "^2.0.0",
    "koa-conditional-get": "^2.0.0",
    "koa-etag": "^3.0.0",
    "koa-favicon": "^2.0.0",
    "mime": "^1.3.4",
    "qs": "^6.3.0",
    "range-parser": "^1.2.0",
    "slet-send": "^3.2.0",
    "utils-merge": "^1.0.0",
    "vary": "^1.1.0"
```