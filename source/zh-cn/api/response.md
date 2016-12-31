title: 响应（Response）

---


The res object represents the HTTP response that an Express app sends when it gets an HTTP request.

In this documentation and by convention, the object is always referred to as res (and the HTTP request is req) but its actual name is determined by the parameters to the callback function in which you’re working.

For example:

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = '/user/:id'
  }
  
  get (req, res) {
    res.send('user ' + req.params.id);
  } 
}

// express
app.get('/user/:id', function(req, res){
  res.send('user ' + req.params.id);
});
```

But you could just as well have:

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = '/user/:id'
  }
  
  get (request, response) {
    response.send('user ' + request.params.id);
  } 
}

// express
app.get('/user/:id', function(request, response){
  response.send('user ' + request.params.id);
});
```

The res object is an enhanced version of Node’s own response object and supports all built-in fields and methods.

# Properties

## res.app

暂时未实现

This property holds a reference to the instance of the Express application that is using the middleware.

res.app is identical to the req.app property in the request object.


## res.headersSent

Check if a response header has already been sent. Useful for seeing if the client may be notified on error.


Boolean property that indicates if the app sent HTTP headers for the response.

```js
get(req, res) {
  console.log(res.headersSent); // false
  res.send('OK');
  console.log(res.headersSent); // true
}

```

## res.locals

express里推荐用locals，而koa中推荐state，故而，将slet的req.locals转为koa的state，但是api保持不变

An object that contains response local variables scoped to the request, and therefore available only to the view(s) rendered during that request / response cycle (if any). Otherwise, this property is identical to app.locals.

This property is useful for exposing request-level information such as the request path name, authenticated user, user settings, and so on.

```js
class MyController extends Slet.BaseController {
  get (req, res) { 
    var a = req.query.a

     res.locals.user = {a:1}
    // return res.body = `hello world ${a}`
    res.send(`hello world ${a}`)
  }
}
```

此时的res.locals.user = this.ctx.state.user

# Methods

## res.append(field [, value])

Appends the specified value to the HTTP response header field. If the header is not already set, it creates the header with the specified value. The value parameter can be a string or an array.

Note: calling res.set() after res.append() will reset the previously-set header value.

```js
res.append('Link', ['<http://localhost/>', '<http://localhost:3000/>']);
res.append('Set-Cookie', 'foo=bar; Path=/; HttpOnly');
res.append('Warning', '199 Miscellaneous warning');
```

## res.attachment([filename])

Sets the HTTP response Content-Disposition header field to “attachment”. If a filename is given, then it sets the Content-Type based on the extension name via res.type(), and sets the Content-Disposition “filename=” parameter.

```js
res.attachment();
// Content-Disposition: attachment

res.attachment('path/to/logo.png');
// Content-Disposition: attachment; filename="logo.png"
// Content-Type: image/png
```

## res.cookie(name, value [, options])

Sets cookie name to value. The value parameter may be a string or object converted to JSON.

The options parameter is an object that can have the following properties.

参见http://expressjs.com/en/4x/api.html#res.cookie

> req.cookies（侧重于取） = res.cookie（侧重于设置）

而在koa里都是ctx.cookies，故而koa的api更精简，而express更富有表现力

## res.download(path [, filename] [, fn])
Transfers the file at path as an “attachment”. Typically, browsers will prompt the user for download. By default, the Content-Disposition header “filename=” parameter is path (this typically appears in the browser dialog). Override this default with the filename parameter.

When an error ocurrs or transfer is complete, the method calls the optional callback function fn. This method uses res.sendFile() to transfer the file.

```js
res.download('/report-12345.pdf');

res.download('/report-12345.pdf', 'report.pdf');
```



```
class MyController extends Slet.BaseController {
  get (req, res) { 
    var a = req.query.a

    return res.download('/package.json')
  }
}
```

## res.sendFile(path [, options] )

- 移除了callback
- 返回值是promise

```
class MyController extends Slet.BaseController {
  get (req, res) { 
    var a = req.query.a

    return res.sendFile('/package.json')
  }
}
```

## res.end([data] [, encoding])

Ends the response process. This method actually comes from Node core, specifically the response.end() method of http.ServerResponse.

Use to quickly end the response without any data. If you need to respond with data, instead use methods such as res.send() and res.json().

```js
res.end();
res.status(404).end();
```

## res.status(code)

Sets the HTTP status for the response. It is a chainable alias of Node’s response.statusCode.

```js
res.status = 403
res.end();

res.status = 400
res.send('Bad Request');

res.status = 404
res.sendFile('/absolute/path/to/404.png');
```

不在支持链式写法,但可以使用setStatus进行链式写法

## res.vary(field)

Adds the field to the Vary response header, if it is not there already.

```js
res.vary('User-Agent').render('docs');
```

## res.type(type)

Sets the Content-Type HTTP header to the MIME type as determined by mime.lookup() for the specified type. If type contains the “/” character, then it sets the Content-Type to type.

```js
res.type('.html');              // => 'text/html'
res.type('html');               // => 'text/html'
res.type('json');               // => 'application/json'
res.type('application/json');   // => 'application/json'
res.type('png');                // => image/png:
```

## res.sendStatus(statusCode)
Sets the response HTTP status code to statusCode and send its string representation as the response body.

```js
res.sendStatus(200); // equivalent to res.status(200).send('OK')
res.sendStatus(403); // equivalent to res.status(403).send('Forbidden')
res.sendStatus(404); // equivalent to res.status(404).send('Not Found')
res.sendStatus(500); // equivalent to res.status(500).send('Internal Server Error')
```

If an unsupported status code is specified, the HTTP status is still set to statusCode and the string version of the code is sent as the response body.

```js
res.sendStatus(2000); // equivalent to res.status(2000).send('2000')
```

More about HTTP Status Codes

## res.redirect(url, [alt])

Perform a [302] redirect to url.

The string "back" is special-cased to provide Referrer support, when Referrer is not present alt or "/" is used.

this.redirect('back');
this.redirect('back', '/index.html');
this.redirect('/login');
this.redirect('http://google.com');
To alter the default status of 302, simply assign the status before or after this call. To alter the body, assign it after this call:

this.status = 301;
this.redirect('/cart');
this.body = 'Redirecting to shopping cart';

## res.links(links)

Joins the links provided as properties of the parameter to populate the response’s Link HTTP header field.

For example, the following call:

res.links({
  next: 'http://api.example.com/users?page=2',
  last: 'http://api.example.com/users?page=5'
});
Yields the following results:

Link: <http://api.example.com/users?page=2>; rel="next",
      <http://api.example.com/users?page=5>; rel="last"
      
      
## res.location(path)

Sets the response Location HTTP header to the specified path parameter.

res.location('/foo/bar');
res.location('http://example.com');
res.location('back');

A path value of “back” has a special meaning, it refers to the URL specified in the Referer header of the request. If the Referer header was not specified, it refers to “/”.

## res.json([body])

Sends a JSON response. This method sends a response (with the correct content-type) that is the parameter converted to a JSON string using JSON.stringify().

The parameter can be any JSON type, including object, array, string, Boolean, or number, and you can also use it to convert other values to JSON, such as null, and undefined (although these are technically not valid JSON).

res.json(null);
res.json({ user: 'tobi' });
res.status(500).json({ error: 'message' });


## res.jsonp([body])

所有的express测试都已经重写了，完全兼容

Sends a JSON response with JSONP support. This method is identical to res.json(), except that it opts-in to JSONP callback support.

```js
res.jsonp(null);
// => null

res.jsonp({ user: 'tobi' });
// => { "user": "tobi" }

res.status(500).jsonp({ error: 'message' });
// => { "error": "message" }
```

另外有3个配置项

- jsonp_callback_name
- json_spaces
- json_replacer

### jsonp_callback_name

这里为了精简，使用class上下文的jsonp_callback_name

```js
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.jsonp_callback_name = 'cb'
    this.path = '/user/:id'
  }
  
  get (req, res) {
    res.jsonp({a:1});
  } 
}

```

当然也可以通过callback作为querystring来设置。

### json_spaces

使用class上下文的json_spaces，空格数量

```js
module.exports = class MyController extends BaseController {
  constructor (app, ctx, next) {
    super(app, ctx, next)

    this.path = '/14'
    this.json_spaces = 2
  }
  
  get (req, res) {
    res.jsonp({ name: 'tobi', age: 2 });
  }
}
```

### json_replacer

使用class上下文的json_replacer：对json进行过滤替换

```js
module.exports = class MyController extends BaseController {
  constructor (app, ctx, next) {
    super(app, ctx, next)

    this.path = '/13'
    this.json_replacer = function(key, val){
      return '_' == key[0]
        ? undefined
        : val;
    }
  }
  
  get (req, res) {
    res.jsonp({ name: 'tobi', _id: 12345 });
  }
}
```

## res.get(field)

Returns the HTTP response header specified by field. The match is case-insensitive.

res.get('Content-Type');
// => "text/plain"

## res.format(object)

Performs content-negotiation on the Accept HTTP header on the request object, when present. It uses req.accepts() to select a handler for the request, based on the acceptable types ordered by their quality values. If the header is not specified, the first callback is invoked. When no match is found, the server responds with 406 “Not Acceptable”, or invokes the default callback.

The Content-Type response header is set when a callback is selected. However, you may alter this within the callback using methods such as res.set() or res.type().

The following example would respond with { "message": "hey" } when the Accept header field is set to “application/json” or “*/json” (however if it is “*/*”, then the response will be “hey”).


res.format({
  'text/plain': function(){
    res.send('hey');
  },

  'text/html': function(){
    res.send('<p>hey</p>');
  },

  'application/json': function(){
    // curl -i -H "Accept: application/json" -H "Content-Type: application/json"   http://127.0.0.1:3000/detail\?a\=1\&b\=3
    res.send({ message: 'hey' });
  },

  'default': function() {
    // log the request and respond with 406
    res.status(406).send('Not Acceptable');
  }
});

In addition to canonicalized MIME types, you may also use extension names mapped to these types for a slightly less verbose implementation:

res.format({
  text: function(){
    res.send('hey');
  },

  html: function(){
    res.send('<p>hey</p>');
  },

  json: function(){
    res.send({ message: 'hey' });
  }
});

## res.render(view [, locals])

Renders a view and sends the rendered HTML string to the client. Optional parameters:

- locals, an object whose properties define local variables for the view.

```
// send the rendered view to the client
res.render('index');

// if a callback is specified, the rendered HTML string has to be sent explicitly
res.locals = { name: 'Tobi' }
res.render('index');

// pass a local variable to the view
res.render('user', { name: 'Tobi' });
```

# Koa

## response.header

Response header object.

## response.headers

Response header object. Alias as response.header.

## response.socket

Request socket.

## response.status

Get response status. By default, response.status is not set unlike node's res.statusCode which defaults to 200.

## response.status=

Set response status via numeric code:

- 100 "continue"
- 101 "switching protocols"
- 102 "processing"
- 200 "ok"
- 201 "created"
- 202 "accepted"
- 203 "non-authoritative information"
- 204 "no content"
- 205 "reset content"
- 206 "partial content"
- 207 "multi-status"
- 300 "multiple choices"
- 301 "moved permanently"
- 302 "moved temporarily"
- 303 "see other"
- 304 "not modified"
- 305 "use proxy"
- 307 "temporary redirect"
- 400 "bad request"
- 401 "unauthorized"
- 402 "payment required"
- 403 "forbidden"
- 404 "not found"
- 405 "method not allowed"
- 406 "not acceptable"
- 407 "proxy authentication required"
- 408 "request time-out"
- 409 "conflict"
- 410 "gone"
- 411 "length required"
- 412 "precondition failed"
- 413 "request entity too large"
- 414 "request-uri too large"
- 415 "unsupported media type"
- 416 "requested range not satisfiable"
- 417 "expectation failed"
- 418 "i'm a teapot"
- 422 "unprocessable entity"
- 423 "locked"
- 424 "failed dependency"
- 425 "unordered collection"
- 426 "upgrade required"
- 428 "precondition required"
- 429 "too many requests"
- 431 "request header fields too large"
- 500 "internal server error"
- 501 "not implemented"
- 502 "bad gateway"
- 503 "service unavailable"
- 504 "gateway time-out"
- 505 "http version not supported"
- 506 "variant also negotiates"
- 507 "insufficient storage"
- 509 "bandwidth limit exceeded"
- 510 "not extended"
- 511 "network authentication required"

NOTE: don't worry too much about memorizing these strings, if you have a typo an error will be thrown, displaying this list so you can make a correction.

## response.message

Get response status message. By default, response.message is associated with response.status.

## response.message=

Set response status message to the given value.

## response.length=

Set response Content-Length to the given value.

## response.length

Return response Content-Length as a number when present, or deduce from this.body when possible, or undefined.

## response.body

Get response body.

## response.body=

Set response body to one of the following:

- string written
- Buffer written
- Stream piped
- Object json-stringified
- null no content response

If response.status has not been set, Koa will automatically set the status to 200 or 204.

## response.remove(field)

Remove header field.

## response.type

Get response Content-Type void of parameters such as "charset".

var ct = this.type;
// => "image/png"

## response.type=

Set response Content-Type via mime string or file extension.

this.type = 'text/plain; charset=utf-8';
this.type = 'image/png';
this.type = '.png';
this.type = 'png';
Note: when appropriate a charset is selected for you, for example response.type = 'html' will default to "utf-8", however when explicitly defined in full as response.type = 'text/html' no charset is assigned.

## response.is(types...)

Very similar to this.request.is(). Check whether the response type is one of the supplied types. This is particularly useful for creating middleware that manipulate responses.

For example, this is a middleware that minifies all HTML responses except for streams.

var minify = require('html-minifier');

app.use(function *minifyHTML(next){
  yield next;

  if (!this.response.is('html')) return;

  var body = this.body;
  if (!body || body.pipe) return;

  if (Buffer.isBuffer(body)) body = body.toString();
  this.body = minify(body);
});

## response.redirect(url, [alt])

Perform a [302] redirect to url.

The string "back" is special-cased to provide Referrer support, when Referrer is not present alt or "/" is used.

this.redirect('back');
this.redirect('back', '/index.html');
this.redirect('/login');
this.redirect('http://google.com');
To alter the default status of 302, simply assign the status before or after this call. To alter the body, assign it after this call:

this.status = 301;
this.redirect('/cart');
this.body = 'Redirecting to shopping cart';


## response.lastModified

Return the Last-Modified header as a Date, if it exists.

## response.lastModified=

Set the Last-Modified header as an appropriate UTC string. You can either set it as a Date or date string.

this.response.lastModified = new Date();

## response.etag=

Set the ETag of a response including the wrapped "s. Note that there is no corresponding response.etag getter.

this.response.etag = crypto.createHash('md5').update(this.body).digest('hex');

# Extra

## res.write(content)

当chunks的时候，支持连续写入浏览器，然后end，此时需要使用write方法，而不是像express使用send

## res.setHeader

别名，为了兼容vary

## res.getHeader

别名，为了兼容vary

## res.setStatus

不在支持链式写法,但可以使用setStatus进行链式写法

```js
class MyController extends Slet.BaseController {
  get (req, res) { 
    var a = req.query.a

    //  res.locals.user = {a:1}
    // return res.body = `hello world ${a}`
    // res.send(`hello world ${a}`)
    // return res.download('/lerna.json')
    // res.status = 201
    return res.setStatus(201).end('sss')
  }
}
```