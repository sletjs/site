title: 扩展
---


## controller扩展

- db
- session
- upload
- uploadCdn

每个都继承相应的Controller即可，最好是作为单独模块，发布到npm上。可以非常好的复用。

## filter扩展

通过filter方式，实现扩展。所有的扩展都是Koa 2.x标准中间件，可以更好的复用已有的中间件


## plugin

采用mix的方式将插件挂载进去
