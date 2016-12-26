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

暂时未实现

Boolean property that indicates if the app sent HTTP headers for the response.

```js
app.get('/', function (req, res) {
  console.log(res.headersSent); // false
  res.send('OK');
  console.log(res.headersSent); // true
});

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
Sends a JSON response with JSONP support. This method is identical to res.json(), except that it opts-in to JSONP callback support.

res.jsonp(null);
// => null

res.jsonp({ user: 'tobi' });
// => { "user": "tobi" }

res.status(500).jsonp({ error: 'message' });
// => { "error": "message" }


说明，express支持

```
app.set('jsonp callback name', 'cb');
```

这里为了精简，使用class上下文的jsonp_callback_name

```
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