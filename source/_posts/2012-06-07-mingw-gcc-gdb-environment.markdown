---
layout: post
title: "在mingw环境下编译最新版本gcc gdb"
date: 2012-06-07 13:40
comments: true
categories: 
---

由于比较喜欢GCC的东西，又必须做WIN下的程序，但是考虑到效率问题    
1，使用LINUX交叉编译MINGW下的程序    
2，直接使用MINGW和MSYS编译WIN程序    

使用交叉编译的方法，在网上搜到的比较麻烦，而且又不是做嵌入式开发，必须用交叉编译;    
所以决定使用第二种方法。   

目前已经成功编译了GDB7.4.1和最新的BINUTILS，还有MPC，MPFR，等GCC需要依赖的库。    
晚上回去看一下GCC4.7.0的编译情况。   

不过在公司已经使用MINGW-GET拿到了GDB7.4和GCC4.6.2,而且使用正常。

目前是使用VIM做编辑器，使用EMACS做调试器（是不是有点小奢侈啊），幸好这两个神器都有WIN版本。

感谢GNU，感谢上帝。
