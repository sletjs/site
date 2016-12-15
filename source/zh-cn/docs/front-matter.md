title: Front-matter
---


<!--## Other



Route patterns may include named parameters, accessible via the params hash:

```
'use strict'

const BasicController = require('../../../').BasicController

module.exports = class MyParamsController extends BasicController {
  constructor(app, ctx, next) {
    super(app, ctx, next)

    this.path = '/hello/:name'
  }

  alias () {
    this.params = this.ctx.params
  }

  get () {
    // matches "GET /hello/foo" and "GET /hello/bar"
    // params['name'] is 'foo' or 'bar'
    return `Hello ${this.params['name']}!`
  }
}
```

You can also access named parameters via block parameters:

get '/hello/:name' do |n|
  # matches "GET /hello/foo" and "GET /hello/bar"
  # params['name'] is 'foo' or 'bar'
  # n stores params['name']
  "Hello #{n}!"
end
Route patterns may also include splat (or wildcard) parameters, accessible via the params['splat'] array:

get '/say/*/to/*' do
  # matches /say/hello/to/world
  params['splat'] # => ["hello", "world"]
end

get '/download/*.*' do
  # matches /download/path/to/file.xml
  params['splat'] # => ["path/to/file", "xml"]
end
Or with block parameters:

get '/download/*.*' do |path, ext|
  [path, ext] # => ["path/to/file", "xml"]
end
Route matching with Regular Expressions:

get /\A\/hello\/([\w]+)\z/ do
  "Hello, #{params['captures'].first}!"
end
Or with a block parameter:

get %r{/hello/([\w]+)} do |c|
  # Matches "GET /meta/hello/world", "GET /hello/world/1234" etc.
  "Hello, #{c}!"
end
Route patterns may have optional parameters:

get '/posts.?:format?' do
  # matches "GET /posts" and any extension "GET /posts.json", "GET /posts.xml" etc.
end
Routes may also utilize query parameters:

get '/posts' do
  # matches "GET /posts?title=foo&author=bar"
  title = params['title']
  author = params['author']
  # uses title and author variables; query is optional to the /posts route
end
By the way, unless you disable the path traversal attack protection (see below), the request path might be modified before matching against your routes. -->