---
layout: post
title : GNash解析swf文件
category : essays
tags : [essays gnash gameswf flash swf]
---
{% include JB/setup %}

---

![frame](http://cu.nj02.dl2.baidupcs.com/file/c3e267ea94cf28be474326b8b6e6fe70?bkt=p2-nj-787&fid=83981061-250528-944239277629096&time=1415452078&sign=FDTAXERB-DCb740ccc5511e5e8fedcff06b081203-1aJqQW5PSMte4qeyZvOpYpcbvgA%3D&to=ncp&fm=Nan,B,U,nc&newver=1&newfm=1&flow_ver=3&expires=8h&rt=sh&r=233673108&mlogid=3223764340&vuk=83981061&vbdid=1144638747&fin=gnash-swf-read.png&fn=gnash-swf-read.png)


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


