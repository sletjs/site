title: 控制器
---


## 处理请求参数的3种方法

### QueryString

当curl命令发起Post测试请求，带有name参数

```
curl http://127.0.0.1:6001/posts?a=1&b=2
```

此时，你可以通过

```
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

```
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

```
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

