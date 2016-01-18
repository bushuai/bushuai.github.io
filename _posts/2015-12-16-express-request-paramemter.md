---
layout: post
title: "express request"
date: 2015-12-16 09:50
---

刚才在向后端传递参数的时候，遇到参数获取不到的情况，查了 [express api](http://expressjs.com/en/4x/api.html#express) 之后才明白，还是自己对 express 的不熟悉，现整理一下 express 获取参数的方法和注意点。

express 在处理请求时，会将参数以键值对的形式封装在 request 对象中，而不同的访问方式会将数据封装在不同的属性中，大致有一下几种。

## req.params

当路由定义为 `/route/:key` 的形式时，我们就可以通过`req.params.key`获取所传递的`key`的值，需要注意的是，req.params 只保存由 express 路由传递的参数，不能保存其他形式传递的数据，如 GET，POST 以及 query 。

URL：`localhost:3000/user/tj`
路由：`/user/:name`

```
req.params.name 
//=> "tj"
```

同时在路由中也可以使用正则。

URL:`localhost:3000/file/javascripts/jquery.js`
路由:`/file/*` 

```
req.params[0]
//=> "javascripts/jquery.js"
```

[Example](https://github.com/bushuai/crud/blob/master/routes/index.js#L229)

## req.body

上面说 req.params 只能接收路由参数，那么当我们需要访问表单 POST 提交数据时该怎么办呢？express 提供了 req.body 属性，借助 [body-parser](https://www.npmjs.com/package/body-parser) 这个中间件我们就能完成访问了。


URL：`localhost:3000/user/profile`
路由：`/user/profile`
method: `POST`

```
var express = require('express');,
    //导入body-parser
    bodyParser = require('body-parser'), 
    app = express();

//配置body-parser
app.use(bodyParser.json()),
app.use(bodyParser.urlencoded({
    extended:true
}));

app.post('/user/profile',function(req,res){
  //在terminal显示req.body
  console.log(req.body);

  //如果提交表单有input[name='username']的文本框时，我们就可以通过下面方式访问

  console.log(req.body.username);
});
```

[Example](https://github.com/bushuai/crud/blob/master/routes/index.js#L186)

## req.query

介绍过 `POST` 请求数据之后，现在了解一下 `GET` 请求的数据如何获取。

`GET` 请求会将传递的数据以键值的方式在 `URL` 中显示，`req.query` 可以特别方便的获取到。

```
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

当URL中没有传递任何数据，`req.query` 默认为 `{}`

[Example](https://github.com/bushuai/crud/blob/master/routes/index.js#L130)

## req.param()

req.param('key') 支持在上述三种方式中获取参数，但是 express 4.x 版本已经弃用。