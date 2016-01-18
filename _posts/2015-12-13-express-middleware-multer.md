---
layout: post
title: "express multer"
date: 2015-12-13 21:16
---

[multer](https://www.npmjs.com/package/multer) 是 Express 中用来处理页面中 multipart form 的中间件，因为在做的一个项目需要用到文件上传，于是自己使用和学习的过程记录下来。

## installation

通过 npm 下载到项目中

```
  > npm install --save multer
```

## 使用

multer会在 request 对象上面附加 body 和 file(s) 对象，body对象包含了 form 中的文本信息，file(s) 对象包含表单中的上传的文件。

```
var express = require('express');
var multer  = require('multer');
var upload = multer({ dest: 'uploads/' });
var app = express();
```

multer 接收一个 option 对象，可以如上例一样，直接定义 dest 属性指定上传到服务器的目录，如果配置中不传 option 对象，所上传的文件将一直保存在内存中，不会写在硬盘上。

```
{
  dest:        'path/to/destination'[,
  storage:     'path/to/destination',]
  fileFilter:  控制文件上传的函数
  limits:      上传文件大小限制
}
```

dest 为基本的 multer 上传目录，如果想要对上传的文件进行更多操作，可以使用 storage 属性。

在处理请求时，可以指定接收上传的文件名称、文件数量等：

- single(filename)

```
app.post('/profile', upload.single('avatar'), function (req, res, next) {
  // req.file is the `avatar` file 
  // req.body will hold the text fields, if there were any 
})
```

- array(fieldname[,maxCount])

```
app.post('/photos/upload', upload.array('photos', 12), function (req, res, next) {
  // req.files is array of `photos` files 
  // req.body will contain the text fields, if there were any 
})
```

- fields(fields)

```
var cpUpload = upload.fields([{ name: 'avatar', maxCount: 1 }, { name: 'gallery', maxCount: 8 }])
app.post('/cool-profile', cpUpload, function (req, res, next) {
  // req.files is an object (String -> Array) where fieldname is the key, and the value is array of files 
  // 
  // e.g. 
  //  req.files['avatar'][0] -> File 
  //  req.files['gallery'] -> Array 
  // 
  // req.body will contain the text fields, if there were any 
})
```

- any()

接收所有类型的文件。

如果要处理 纯文本（text-only） 的 multipart form ，可以使用上述的任何一种方法。

## 文件信息

form中上传的所有文件都将包含以下信息：

- fieldname       ---form中指定的名称
- originalname    ---本地名称
- encoding        ---编码
- mimetype        ---MIME
- size            ---大小
- destination     ---服务器存储目录 DiskStorage
- filename        ---在服务器存储目录中的文件名 DiskStorage
- path            ---上传后的全路径 DiskStorage
- buffer          ---A Buffer of the entire file MemoryStorage

## storage 配置

DiskStorage 可以指定两个参数 destination 和 filename ，前者用来指定上传目录，后者用来对文件名称进行操作，需要注意的是，multer 对于上传之后的文件不添加任何后缀，当前用户必须对指定的上传目录具有必要的权限。

```
var storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, '/tmp/my-uploads')
  },
  filename: function (req, file, cb) {
    cb(null, file.fieldname + '-' + Date.now())
  }
})
 
var upload = multer({ storage: storage })
```

MemoryStorage 将上传的文件以 缓冲 的方式保存在内存中，所以在处理过程中(文件特别大)可能出现内存溢出的情况。

## limits

- fieldNameSize   最长文件名限制 (100 bytes)
- fieldSize       文件大小 (1MB)
- fields          非文件数目
- fileSize        最大文件大小 （multipart form 无限制)
- files           最大文件个数 （multipart form 无限制)
- parts           最大文本+文件个数 （multipart form 无限制)
- headerPairs     最大解析header 键值对个数（200）

## fileFilter

用来控制哪些文件能够上传。

```
function fileFilter (req, file, cb) {
 
  // The function should call `cb` with a boolean 
  // to indicate if the file should be accepted 
 
  // To reject this file pass `false`, like so: 
  cb(null, false)
 
  // To accept the file pass `true`, like so: 
  cb(null, true)
 
  // You can always pass an error if something goes wrong: 
  cb(new Error('I don\'t have a clue!'))
 
}
```

## 错误处理

```
var upload = multer().single('avatar')
 
app.post('/profile', function (req, res) {
  upload(req, res, function (err) {
    if (err) {
      // An error occurred when uploading 
      return
    }
 
    // Everything went fine 
  })
});
```

## Example

[Github](https://github.com/bushuai/crud/blob/master/routes/index.js#L4)



