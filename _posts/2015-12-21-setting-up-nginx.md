---
layout: post
title: "Nginx"
date: 2015-12-21 19:33
---

Nginx是一个高性能的HTTP和反向代理服务器，也是一个IMAP/POP3/SMTP 服务器。一直想熟悉一下 Nginx，但是因为没有应用场景一直没动，最近一时兴起在本地配置一个 Nginx Server,准备测试一些功能。


## 安装

通过Homebrew安装 Nginx

```
$ brew install nginx
```

等待安装完成之后，Homebrew 会提示一大堆操作，当然也可以使用

```
$ brew info nginx
```

查看一些配置信息。

![nginx-settings](/assets/postimages/15-12-21/nginx-1.png)

## 测试

目录：/usr/local/Cellar/nginx/

www： /usr/local/var/www/

配置文件： usr/local/etc/nginx/nginx.conf

打开浏览器 localhost:8080 出现 403 forbidden

看了一下配置文件

![nginx-conf](/assets/postimages/15-12-21/nginx-2.png)

配置的root目录下并没有 index.html 文件，我更改了一个目录，然后

```
$ nginx -s reload 
```

重启之后，正常访问。

## 其他

查询nginx主进程号:

```
$ ps -ef | grep nginx

从容停止
$ kill -QUIT 主进程号

快速停止
$kill -TERM 主进程号

强制停止
$ kill -9 nginx
```

若nginx.conf配置了pid文件路径，如果没有，则在logs目录下

```
$ kill -信号类型 '/usr/local/nginx/logs/nginx.pid'
```

遇到403 forbidden 错误分两种情况，一是由于配置有误，二是没有权限。所以在保证配置无误的情况下，保证你对所要访问文以及父级目录都有权限。开始折腾。