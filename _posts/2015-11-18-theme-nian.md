---
layout: post
title: "Theme nian"
date: 2015-11-18 22:57:11
---

从WordPress到Hexo，再到前一阵子接触Jekyll，之间相隔了4年。这几年时间其实也没有好好的写一个博客，现在想来也有几分惭愧。

接触Jekyll之后第一件事请就是去找一个自己钟意的Theme，但是找了很长时间也没有满意的，于是决定自己写一个。于是便做了如下计划：

1. Skecth中画出原型
2. Jekyll new新的site
3. 学习liquid标记
4. 整合网站发布到Github

当时画出的效果是这样：

![image](/assets/postimages/theme-nian-pic-prepare.png)

写出来后的样子：

![image](/assets/postimages/theme-nian-pic-old.png)

最后经过几次修改优化之后是这样的，中间加了左边用于导航的小红条，红色和主内容区域的白色以及木纹背景的颜色反差比较大，个人感觉反而看着更舒服。

![image](/assets/postimages/theme-nian-pic-new.png)

写完之后就是调试的过程，在Google Chrome下显示没有问题，但是在IE上的表现却不尽如人意，归根结底是因为IE 9以下的版本几乎不支持HTML5，而我写的时候却大量使用了HTML5的新标签。为了解决HTML5的新标签的兼容问题，引入了 [html5shiv](https://github.com/afarkas/html5shiv) ，同时引入 [Response.js](https://github.com/scottjehl/Respond) 让IE9以下的版本支持 `media query`，所以对于手机和平板等移动设备也有同步的优化。

如果你喜欢这个主题，欢迎到 [我的Github](http://github.com/bushuai/nian) star并将其作为你的博客主题，也可以按照自己的想法修改直到满意。`nian`是一个功能上来讲堪称简朴的主题，后续我会加上其他模块，仅最大努力做到功能和使用上的结合。
