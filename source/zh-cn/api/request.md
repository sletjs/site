title: 请求（Request）
---

The req object represents the HTTP request and has properties for the request query string, parameters, body, HTTP headers, and so on. In this documentation and by convention, the object is always referred to as req (and the HTTP response is res) but its actual name is determined by the parameters to the callback function in which you’re working.

For example:

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = '/user/:id'
  }
  
  get (req, res) {
    res.send('user ' + req.params.id)
  } 
}

// express
app.get('/user/:id', function(req, res) {
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
    response.send('user ' + request.params.id)
  } 
}

// express
app.get('/user/:id', function(request, response) {
  response.send('user ' + request.params.id);
});
```

The req object is an enhanced version of Node’s own request object and supports all [built-in fields and methods](https://nodejs.org/api/http.html#http_class_http_incomingmessage).

# Properties

未实现

- baseUrl

## req.baseUrl

无此属性，可使用req.url或originUrl，稍后看看是否有必要加

## req.body

Contains key-value pairs of data submitted in the request body. By default, it is undefined, and is populated when you use in post method.

The following example shows how to use post method to populate req.body.

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = '/profile'
  }
  
  post (req, res) {
    console.log(req.body);
    res.body = req.body
  } 
}

// express
app.post('/profile', function (req, res, next) {
  console.log(req.body);
  res.json(req.body);
});
```

## req.cookies

koa uses the [cookies](https://github.com/jed/cookies) module where options are simply passed.

> req.cookies.get(name, [options])

Get cookie name with options:

- signed the cookie requested should be signed


```js
// Cookie: name=tj
req.cookies.name
// => "tj"

```

> ctx.cookies.set(name, value, [options])

Set cookie name to value with options:

- maxAge a number representing the milliseconds from Date.now() for expiry
- signed sign the cookie value
- expires a Date for cookie expiration
- path cookie path, /' by default
- domain cookie domain
- secure secure cookie
- httpOnly server-accessible cookie, true by default
- overwrite a boolean indicating whether to overwrite previously set cookies of the same name (false by default). If this is true, all cookies set during the same request with the same name (regardless of path or domain) are filtered out of the Set-Cookie header when setting this cookie

## req.fresh

Indicates whether the request is “fresh.” It is the opposite of req.stale.

It is true if the cache-control request header doesn’t have a no-cache directive and any of the following are true:

- The if-modified-since request header is specified and last-modified request header is equal to or earlier than the modified response header.
- The if-none-match request header is *.
- The if-none-match request header, after being parsed into its directives, does not match the etag response header.

```js
req.fresh
// => true
```

For more information, issues, or concerns, see fresh.

## req.hostname

Contains the hostname derived from the Host HTTP header.

When the trust proxy setting does not evaluate to false, this property will instead have the value of the X-Forwarded-Host header field. This header can be set by the client or by the proxy.

```js
// Host: "example.com:3000"
req.hostname
// => "example.com"
```

## req.ip

Contains the remote IP address of the request.

When the trust proxy setting does not evaluate to false, the value of this property is derived from the left-most entry in the X-Forwarded-For header. This header can be set by the client or by the proxy.

```js
req.ip
// => "127.0.0.1"
```

## req.ips

When the trust proxy setting does not evaluate to false, this property contains an array of IP addresses specified in the X-Forwarded-For request header. Otherwise, it contains an empty array. This header can be set by the client or by the proxy.

For example, if X-Forwarded-For is client, proxy1, proxy2, req.ips would be ["client", "proxy1", "proxy2"], where proxy2 is the furthest downstream.

## req.method

Contains a string corresponding to the HTTP method of the request: GET, POST, PUT, and so on.

## req.originalUrl

> req.url is not a native Express property, it is inherited from Node’s http module.

This property is much like req.url; however, it retains the original request URL, allowing you to rewrite req.url freely for internal routing purposes. For example, the “mounting” feature of app.use() will rewrite req.url to strip the mount point.

```js
// GET /search?q=something
req.originalUrl
// => "/search?q=something"
```

In a middleware function, req.originalUrl is a combination of req.baseUrl and req.path, as shown in the following example.

此例子需要确认

```js
app.use('/admin', function(req, res, next) {  // GET 'http://www.example.com/admin/new'
  console.log(req.originalUrl); // '/admin/new'
  console.log(req.baseUrl); // '/admin'
  console.log(req.path); // '/new'
  next();
});
```

## req.params

This property is an object containing properties mapped to the named route “parameters”. For example, if you have the route /user/:name, then the “name” property is available as req.params.name. This object defaults to {}.

```js
// GET /user/tj
req.params.name
// => "tj"
```

When you use a regular expression for the route definition, capture groups are provided in the array using req.params[n], where n is the nth capture group. This rule is applied to unnamed wild card matches with string routes such as /file/*:

```js
// GET /file/javascripts/jquery.js
req.params[0]
// => "javascripts/jquery.js"
```

## req.path

Contains the path part of the request URL.

```js
// example.com/users?sort=desc
req.path
// => "/users"
```

## req.protocol

Contains the request protocol string: either http or (for TLS requests) https.

When the trust proxy setting does not evaluate to false, this property will use the value of the X-Forwarded-Proto header field if present. This header can be set by the client or by the proxy.

```js
req.protocol
// => "http"
```

## req.query

This property is an object containing a property for each query string parameter in the route. If there is no query string, it is the empty object, {}.

```js
// GET /search?q=tobi+ferret
req.query.q
// => "tobi ferret"

// GET /shoes?order=desc&shoe[color]=blue&shoe[type]=converse
req.query.order
// => "desc"

req.query.shoe.color
// => "blue"

req.query.shoe.type
// => "converse"
```

## req.route

Contains the currently-matched route, a string. For example:

```js
// slet
class MyController extends BaseController {
  constructor (ctx, next) {
    super(ctx, next)
    
    this.path = '/user/:id'
  }
  
  get (req, res) {
    console.log(req.route)
    return 'GET'
  } 
}

// express
app.get('/user/:id?', function userIdHandler(req, res) {
  console.log(req.route);
  res.send('GET');
});
```

这个和express里的req.route类似，但返回的内容不一样。

## req.secure

A Boolean property that is true if a TLS connection is established. Equivalent to:

```js
'https' == req.protocol;
```

## req.signedCookies

未实现此方法，原因是通过req.cookies可以非常简单的实现

```js
req.cookies.set( "signed", "bar", { signed: true } )
```

更多用法，见https://github.com/pillarjs/cookies

## req.stale

Indicates whether the request is “stale,” and is the opposite of req.fresh. For more information, see req.fresh.

```js
req.stale
// => true
```

## req.subdomains
An array of subdomains in the domain name of the request.

```js
// Host: "tobi.ferrets.example.com"
req.subdomains
// => ["ferrets", "tobi"]
```

The application property subdomain offset, which defaults to 2, is used for determining the beginning of the subdomain segments. To change this behavior, change its value using app.set.

## req.xhr

新增，原来koa的request上并无

A Boolean property that is true if the request’s X-Requested-With header field is “XMLHttpRequest”, indicating that the request was issued by a client library such as jQuery.

```js
req.xhr
// => true
```

# Methods

目前express的req方法8个，都已经支持了

新增

- req.throw && res.throw （从koa上移植过来的）

## req.accepts(types)

Checks if the specified content types are acceptable, based on the request’s Accept HTTP header field. The method returns the best match, or if none of the specified content types is acceptable, returns false (in which case, the application should respond with 406 "Not Acceptable").

The type value may be a single MIME type string (such as “application/json”), an extension name such as “json”, a comma-delimited list, or an array. For a list or array, the method returns the best match (if any).

```js
// Accept: text/html
req.accepts('html');
// => "html"

// Accept: text/*, application/json
req.accepts('html');
// => "html"
req.accepts('text/html');
// => "text/html"
req.accepts(['json', 'text']);
// => "json"
req.accepts('application/json');
// => "application/json"

// Accept: text/*, application/json
req.accepts('image/png');
req.accepts('png');
// => undefined

// Accept: text/*;q=.5, application/json
req.accepts(['html', 'json']);
// => "json"
```

You may call this.accepts() as many times as you like, or use a switch:

```js
switch (req.accepts('json', 'html', 'text')) {
  case 'json': break;
  case 'html': break;
  case 'text': break;
  default: req.throw(406, 'json, html, or text only');
}
```

For more information, or if you have issues or concerns, see [accepts](https://github.com/expressjs/accepts?_ga=1.265802703.1312427988.1477612421).

## request.acceptsEncodings(encodings)

Check if encodings are acceptable, returning the best match when true, otherwise false. Note that you should include identity as one of the encodings!

```js
// Accept-Encoding: gzip
req.acceptsEncodings('gzip', 'deflate', 'identity');
// => "gzip"

req.acceptsEncodings(['gzip', 'deflate', 'identity']);
// => "gzip"
```

When no arguments are given all accepted encodings are returned as an array:

```js
// Accept-Encoding: gzip, deflate
req.acceptsEncodings();
// => ["gzip", "deflate", "identity"]
```

Note that the identity encoding (which means no encoding) could be unacceptable if the client explicitly sends identity;q=0. Although this is an edge case, you should still handle the case where this method returns false.

## request.acceptsCharset(charsets)

新增，兼容expressapi,用法同acceptsCharsets

## request.acceptsCharsets(charsets)

Check if charsets are acceptable, returning the best match when true, otherwise false.

```js
// Accept-Charset: utf-8, iso-8859-1;q=0.2, utf-7;q=0.5
req.acceptsCharsets('utf-8', 'utf-7');
// => "utf-8"

req.acceptsCharsets(['utf-7', 'utf-8']);
// => "utf-8"
```js

When no arguments are given all accepted charsets are returned as an array:

```js
// Accept-Charset: utf-8, iso-8859-1;q=0.2, utf-7;q=0.5
req.acceptsCharsets();
// => ["utf-8", "utf-7", "iso-8859-1"]
```

## request.acceptsLanguages(langs)

Check if langs are acceptable, returning the best match when true, otherwise false.

```js
// Accept-Language: en;q=0.8, es, pt
req.acceptsLanguages('es', 'en');
// => "es"

req.acceptsLanguages(['en', 'es']);
// => "es"
```

When no arguments are given all accepted languages are returned as an array:

```js
// Accept-Language: en;q=0.8, es, pt
req.acceptsLanguages();
// => ["es", "pt", "en"]
```

## req.get(field)

Returns the specified HTTP request header field (case-insensitive match). The Referrer and Referer fields are interchangeable.

```js
req.get('Content-Type');
// => "text/plain"

req.get('content-type');
// => "text/plain"

req.get('Something');
// => undefined
```

Aliased as req.header(field).

## req.is(type)

Returns true if the incoming request’s “Content-Type” HTTP header field matches the MIME type specified by the type parameter. Returns false otherwise.

```js
// With Content-Type: text/html; charset=utf-8
req.is('html');
req.is('text/html');
req.is('text/*');
// => true

// When Content-Type is application/json
req.is('json');
req.is('application/json');
req.is('application/*');
// => true

req.is('html');
// => false
```

For more information, or if you have issues or concerns, see [type-is](https://github.com/expressjs/type-is?_ga=1.36035168.1312427988.1477612421).

## req.param(name [, defaultValue])

Deprecated. Use either req.params, req.body or req.query, as applicable.

Returns the value of param name when present.

```js
// ?name=tobi
req.param('name')
// => "tobi"

// POST name=tobi
req.param('name')
// => "tobi"

// /user/tobi for /user/:name
req.param('name')
// => "tobi"
```

Lookup is performed in the following order:

- req.params
- req.body
- req.query

Optionally, you can specify defaultValue to set a default value if the parameter is not found in any of the request objects.

## req.range(size[, options])

新增，Koa中无此法

Range header parser.

The size parameter is the maximum size of the resource.

The options parameter is an object that can have the following properties.


An array of ranges will be returned or negative numbers indicating an error parsing.

- -2 signals a malformed header string
- -1 signals an unsatisfiable range

```js
// parse header from request
var range = req.range(1000)

// the type of the range
if (range.type === 'bytes') {
  // the ranges
  range.forEach(function (r) {
    // do something with r.start and r.end
  })
}
```

## req.throw([msg], [status], [properties])

新增，不在express的req种

Helper method to throw an error with a .status property defaulting to 500 that will allow Koa to respond appropriately. The following combinations are allowed:

```js
req.throw(403);
req.throw('name required', 400);
req.throw(400, 'name required');
req.throw('something exploded');
```
For example this.throw('name required', 400) is equivalent to:

```js
var err = new Error('name required');
err.status = 400;
throw err;
```

Note that these are user-level errors and are flagged with err.expose meaning the messages are appropriate for client responses, which is typically not the case for error messages since you do not want to leak failure details.

You may optionally pass a properties object which is merged into the error as-is, useful for decorating machine-friendly errors which are reported to the requester upstream.

```js
req.throw(401, 'access_denied', { user: user });
req.throw('access_denied', { user: user });
```

koa uses [http-errors](https://github.com/jshttp/http-errors) to create errors.

# Extra

koa中

## request.length

Return request Content-Length as a number when present, or undefined.


## request.origin

Get origin of URL, include protocol and host.

```js
req.origin
// => http://example.com
```

## request.href

Get full request URL, include protocol, host and url.

```js
req.href
// => http://example.com/foo/bar?q=1
```

## request.querystring

Get raw query string void of ?.

> req.query = req.querystring

## request.search

Get raw query string with the ?.

## request.host

Get host (hostname:port) when present. Supports X-Forwarded-Host when app.proxy is true, otherwise Host is used.


## request.type

Get request Content-Type void of parameters such as "charset".

```js
var ct = req.type;
// => "image/png"
```

## request.charset

Get request charset when present, or undefined:

```js
req.charset
// => "utf-8"
```

## request.idempotent

Check if the request is idempotent.

## request.socket

Return the request socket.

