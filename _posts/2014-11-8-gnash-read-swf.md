---
layout: post
title : GNash解析swf文件
category : essays
tags : [essays gnash gameswf flash swf]
---
{% include JB/setup %}

---

overview 

![frame](/assets/gnash-swf-read.png)


从gui/gnash.cpp中的main函数进入
在playerFile函数中调用gnash::Player::run方法针对每个参数文件(swf文件)，解析，显示

swf文件由文件头和后续一系列的标签构成，具体格式参考之前的文档
[http://www.zhuzhonghua.com/essays/2014/11/07/read-gnash-code/](http://www.zhuzhonghua.com/essays/2014/11/07/read-gnash-code/)

每个标签由对应的函数读取
gnash::Player::run方法调用addDefaultLoaders填充各个标签以及读取函数

addDefaultLoaders这个函数在defaulttagloaders.cpp中实现

继续调用load_movie进行真正的加载

在load_movie中调用MovieFactory::makeMovie，返回一个movie_def

MovieFactory::makeMovie中调用createSWFMovie加载swf文件

在createSWFMovie中先解析header，之后根据情况，决定是否使用单独的线程使用SWFMovieDefinition::read_all_swf加载后续标签

在swfparser.cpp中调用SWFParser::read，这里就会用到前面用到tag标签以及对应的读取函数


