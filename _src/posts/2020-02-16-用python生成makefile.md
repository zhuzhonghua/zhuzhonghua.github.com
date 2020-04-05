    Title: 用Python生成CMake
    Date: 2020-02-16T23:14:31
    Tags: Python Makefile cmake automake xmake make

CMake不支持shell命令输出，中间加一层python，可以方便的解决这个问题  

比如sdl2-config --cflags 和 sdl2-config --lib 在mac和linux 下输出不一样，在cmake里使用就不太方便  

这一部分使用python 动态生成就解决了这个问题  


