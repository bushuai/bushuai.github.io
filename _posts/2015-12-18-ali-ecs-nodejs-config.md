---
layout: post
title: "aliyun ECS with Node"
date: 2015-12-18 17:48
---

aliyun ECS的学生优惠好像已经进行很久了，今天才看到，注册了试用帐号，接下来说一说在aliyun ECS下安装 Node 环境。主要包括以下内容:

1. Node
2. Express forever
3. MongoDB
4. vsftpd

## connect aliyun server

在 aliyun 注册成功并开启服务器之后，会得到一个服务器的 IP 地址，我们在 terminal 直接使用 ssh 进行连接。

```
$ ssh root@your-ip-address
root@your-ip-address's password:
```

输入密码之后就成功登录服务器了。

![aliyun-login-success](/assets/postimages/15-12-18/aliyun-1.png)

## install node

### 安装

```
apt-get install  -y gcc g++ make automake
wget http://nodejs.org/dist/v4.2.3/node-v4.2.3.tar.gz
tar -xvzf node-v0.10.29.tar.gz 
cd  node-v0.10.29.tar.gz 
./configure 
make
make install
```

可能會遇到提示gcc版本過低。

1.) Press Ctrl+Alt+T on your keyboard to open terminal. When it opens, run below commands to add the ppa:
```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
```

2.) Then install gcc 4.8 and g++ 4.8:
```
sudo apt-get update; 
sudo apt-get install gcc-4.8 g++-4.8
```

3.) Once installed, run following commands one by one to use gcc 4.8 instead of previous version.
```
sudo update-alternatives --remove-all gcc 
sudo update-alternatives --remove-all g++
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 20
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.8 20
sudo update-alternatives --config gcc
sudo update-alternatives --config g++
```

Now you have the gcc 4.8 with c++11 complete feature in your system. Check out by:
```
gcc --version
```

安装完成之后，在 terminal 检查一下看是否安装成功

```
$ node -v
v4.2.3
$ npm -v
3.5.2
```

```
ln -s /opt/node/bin/node /usr/bin/node
ln -s /opt/node/bin/npm /usr/bin/npm
```


能够显示版本号就说明安装成功了。

## install express 

### 安装

接下来安装express，如果 npm 下载特别慢的话可以在命令后面加上 `--registery=registery.npm.taobao.org` 使用淘宝的源来下载。

```
$ npm install -g express-generator forever --registery=registery.npm.taobao.org
```

等待完成之后就可以用 express 来初始化一个项目了,forever 能够一直保持 Node 程序处于运行状态，当程序需要重启的时候会自动重启。

```
$ express express-demo -e
```

![express-demo](/assets/postimages/15-12-18/aliyun-2.png)

这里我们创建了一个名称为express-demo并使用 ejs 作为模板引擎的项目（ejs 是一个前端模板引擎，点[这里](http://www.embeddedjs.com/getting_started.html)了解更多）。

### 运行

接下来进入目录并安装依赖包， 

```
$ cd express-demo && npm install
``` 

之后运行程序

```
$DEBUG=express-demo:* npm start
```
这个时候可能执行命令了，但是没有任何反应。这是因为我们创建文件之后并没有给它们可执行的权限，所以在 terminal 输入

![terminal-privillage](/assets/postimages/15-12-18/aliyun-3.png)

重新运行，打开浏览器，输入你的服务器 IP 地址，就可以看到 express 的初始页面了。

![exprss-success](/assets/postimages/15-12-18/aliyun-4.png)

这个时候在 terminal 里面会看到一条 GET 请求。

![exprss-success](/assets/postimages/15-12-18/aliyun-5.png)

Node.js Express 安装完成，接下来安装 MongoDB。

## MongoDB

### 安装

在 terminal 输入

```
$ apt-get install mongodb
```

等待MongoDB安装完成。安装成功后，默认是启动的， 可以通过 `pgrep mongo -l` 查看运行状态，默认端口是27017。

```
sudo service mongodb start|stop|restart
```

启动目录：/usr/bin/mongod

日志目录：/var/log/mongodb/mongodb.log

数据库目录：/var/lib/mongodb/

### 连接

在 terminal 输入 `mongo` 连接 MongoDB

![mongo](/assets/postimages/15-12-18/aliyun-6.png) 

关于 MongoDB 的使用，请点击[这里](https://docs.mongodb.org/manual/)了解。

## vsftpd

为了方便使用 FTP 在本地上传文件到服务器，我们安装 vsftpd 来提供 FTP 服务。

### 安装

```
$ apt-get install vsftpd
```

安装完成之后，我们需要更改配置文件禁止匿名用户登录。

```
$ vi /etc/vsftpd
```

将该文件里面的配置项目按照下面修改

> 
anomonyous_enable=NO
listen=YES
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
anon_upload_enable=YES
anon_mkdir_write_enable=YES
dirmessage_enable=YES
use_localtime=YES
xferlog_enable=YES
connect_from_port_20=YES
secure_chroot_dir=/var/run/vsftpd/empty
pam_service_name=ftp
rsa_cert_file=/etc/ssl/private/vsftpd.pem

修改vsftpd的pam配置，允许用户通过FTP登录
 
 ```
 $ vi /etc/pam.d/vsftpd
 ```

```
auth required /lib64/security/pam_listfile.so item=user sense=deny file=/etc/ftpusers onerr=succeed 
auth required /lib64/security/pam_unix.so shadow nullok 
auth required /lib64/security/pam_shells.so 
account required /lib64/security/pam_unix.so 
session required /lib64/security/pam_unix.so 
```

之后保存退出。

### 启动

设置FTP用户的账号，例如账号名称为 **test** ，目录为 **/home/username** ，且设置不允许通过ssh登录。

 ```
$ useradd -m -d /home/username -s /sbin/nologin username
 ```

为username设置密码

```
$ passwd username
```

重启vsftpd，这个时候我们就可以用这个帐号登录 FTP 服务进行文件的操作了。

## End

小伙伴们也可以去 [aliyun](http://www.aliyun.com/) 申请一个ECS服务器来熟悉一下，现在注册购买的话还有学生优惠 。😋 