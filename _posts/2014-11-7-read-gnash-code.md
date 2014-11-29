---
layout: post
title : 阅读gnash的代码
category : essays
tags : [essays gnash gameswf flash swf]
---
{% include JB/setup %}

---

###GNash是什么？
[http://www.gnu.org/software/gnash/](http://www.gnu.org/software/gnash/)
[http://en.wikipedia.org/wiki/Gnash](http://en.wikipedia.org/wiki/Gnash)
[http://blog.csdn.net/situzhuge/article/details/6186562](http://blog.csdn.net/situzhuge/article/details/6186562)
简单来讲，GNash是flash player的GNU的替代品

###关于GNash相关的问题
[FAQ](http://www.gnashdev.org/?q=node/25)

###swf相关的文档
[swf_file_format_v9.pdf](http://pan.baidu.com/s/1i3BpNI9)
[avm2overview.pdf](http://pan.baidu.com/s/1eQna3kA)

###下载编译GNash
最开始使用简单粗暴的编译策略，将来熟悉了之后，再细细的去定制
环境为xubuntu
使用git clone git://git.savannah.gnu.org/gnash.git下载它的源代码
    
    sh autogen.sh
    ./configure

这里会提示很多依赖的库找不到，使用apt-get 一个一个安装，或者

    sh deb-attempt-install-dependencies.sh
    
来安装所有的依赖

如果还有其他的相关依赖问题，自行Google吧
GNash依赖的库还是很多的，环境不同遇到的问题也会很多

当所有的依赖都解决之后，就可以

    make
    
###调试
阅读代码的一个很好的方式就是调试
由于GNash本身编译方式的原因，不能简单的使用gdb xxx的方式调试
参考[debug-gnash](http://wiki.gnashdev.org/Debugging_Tips)

我自己写了一个sh文件，来帮助我调试

    #!/bin/sh
    libtool --mode=execute gdb --args gui/sdl-gnash
    
---
###补充

2014/11/26

使用Emacs24调试 

    gdb -i=mi gui/.libs/lt-sdl-gnash
    
###补充2

2014／11／29

使用

    make CXXFLAGS="-g -std=c++11 -fPIC"

编译一个不优化，方便调试的版本

在使用gdb调试前，需要先使用上面的libtool来生成一下对应的lt-sdl-gnash
