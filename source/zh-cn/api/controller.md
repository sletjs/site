title:  Controller
---
「Controller」是 Sletjs 用来处理http请求的MVC架构中的控制器，在 Controller 中实现类似于[tornado](http://www.tornadoweb.org/en/stable/)等api，由于Slet采用ES6的面对对象写法，所以它有很多个带有继承关系的Controller

## BaseController

所有controller的基类，必须继承它

- [BaseController](https://github.com/sletjs/BaseController)

## 处理器（Processor）

处理器（Processor）是 Box 中非常重要的元素，它用于处理文件，您可以使用上述的路径对比来限制该处理器所要处理的文件类型。使用 `addProcessor` 来添加处理器。

``` js
box.addProcessor('posts/:id', function(file){
  //
});
```

Box 在处理时会把目前处理的文件内容（`file`）传给处理器，您可以通过此参数获得该文件的数据。

属性 | 描述
--- | ---
`source` | 文件完整路径
`path` | 文件相对于 Box 的路径
`type` | 文件类型。有 `create`, `update`, `skip`, `delete`。
`params` | 从路径对比中取得的信息

Box 还提供了一些方法，让您无须手动处理文件 I/O。

方法 | 描述
--- | ---
`read` | 读取文件
`readSync` | 同步读取文件
`stat` | 读取文件状态
`statSync` | 同步读取文件状态
`render` | 渲染文件
`renderSync` | 同步渲染文件

[util.Pattern]: https://github.com/hexojs/hexo-util#patternrule