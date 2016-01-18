---
layout: post
title: "Node.js 微信开发（一）"
date: 2015-12-19 13:51
---

两天前，一个朋友注册了一个微信公众号，让我给她投稿，我把之前骑行川藏的照片发给她，不一会儿她的公众号就推送了。我突然想起来在二〇一四年的时候也注册过一个公众号，看了一下微信开发者文档，想试着做一个什么东西出来，做了一天，做了个用豆瓣API查询图书和电影信息的功能，也了解了一下微信的文档。记录一下过程。

## 环境

1. Node
2. express wechat
3. MongoDB

## 准备

打开微信官网，注册

![wechat-dev](/assets/postimages/15-12-21/wechat-dev-1.png)

个人职能注册订阅号，还不能认证。这里要吐槽一下腾讯给未认证的订阅号的权限太低了，本来以为可以自定义菜单项目，乐呵呵的去做了后台API，然而并没有任何用。

过程忘记了，跟着提示一步一步来。

## 微信 Token 验证

注册完成后，进入主界面，点击左边菜单最下面一项开发，基础配置。

![wechat-dev](/assets/postimages/15-12-21/wechat-dev-2.png)

- URL 写你的服务器接口的地址，这个地址会接收到微信服务器发送的验证请求
- Token 微信服务器用来做身份验证
- EncodingAESKey 消息加密解密密钥

当然，在填写这三项之前，你需要在你的服务器接口上写好验证相关模块，在这里我们使用 express router 在后台接收请求。

初始化 express 项目之前的已经讲过，现在我们直接看接口验证相关的一些参数。

微信服务器会发送一条GET请求到你填写的 服务器URL，并会带着几个参数，其中我们需要用的有

1. signature
2. nonce
3. timestamp

```
@ controller/wechat.js

var express = require('express'),
    crypto  = require('crypto'),
    router  = express.Router();

router.get('/',function(req,res){

  if(!checkSignature(req)){
      res.writeHead(401);
      res.end('signature not matched.');
      return;
  }

  res.writeHead(200);
  res.end(req.query.echostr);
  
  function checkSignature(req){
      var token = 'your-token-here';
      timestamp = req.query.timestamp,
      nonce     = req.query.nonce,
      signature = req.query.signature;

    var sha1 = crypto.createHash('sha1');
    var arr = [token, timestamp, nonce].sort();
    sha1.update(arr.join(''));

    return sha1.digest('hex') === signature
  }
});
```

在 **app.js** 中加入 wechat Router

```
@ app.js

var wechat = require('./controller/wechat');

app.get('/wechat',wechat);

```

一切写好之后，在微信公众号基础配置页面点击提交，等待服务器返回结果即配置成功。


