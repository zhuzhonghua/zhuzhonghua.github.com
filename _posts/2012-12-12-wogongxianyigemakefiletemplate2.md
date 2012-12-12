---
layout: post
title : 我贡献一个makefile.template<2>
category : essays
tags : [essays]
---
{% include JB/setup %}

以前写过一个版本，在[这里](http://zhuzhonghua.github.com/essays/2012/07/17/wogongxianyigemakefiletemplate/)

使用的过程中发现一些问题，作一些修改。而且也参考了automake，还有《跟我一起写Makefile》，当然不能没有伟大的G。

1. 在MINGW下面的make和Linux环境下面的Make关于使用$时会有一些差别。如果在Linux下面使用$，那必须使用$$不管在""，还是''里，或者其他地方。  
2. automake 会把文件之间的依赖关系生成在build/.deps下面，然后基本上一个中间文件对应一个Po文件。
3. 实在不想使用好几个命令来完成一件事情，所以就折腾一下Makefile，只是使用make命令。  

> Demo如下：

    SRC=abc.cpp abc/abc2.cpp ../abc3.cpp  
    TMP=$(subst ..,up,$(SRC))  
    TMP2=$(subst /,-,$(TMP))  
    OBJS=$(TMP2: %.cpp=build/%.o)  

> 使用subst函数做各种替换，生成唯一的.o文件


	include $(TMP2:%.cpp=build/.deps/%.Po)   

	
> 类似于automake，把中间文件.Po都生成在build/.deps下面


    build/.deps/%.Po:
    	if ! test -d build; then \
    	   mkdir build; \
    	fi; \
    	if ! test -d build/.deps; then \
    	   mkdir build/.deps; \
    	fi; \
		g++ -MM $(subst -,/,$(subst up,..,$*)).cpp $(CFLAGS) $(DEFINE) > $@; \
		mv -f $@ $@.$$; \
		sed '1s/[^:]*.o/build\/$*.o $(subst /,\/,$@)/' $@.$$ > $@; \
		mv -f $@ $@.$$; \
		sed '$$a\\tg++ -c -o build\/$*.o $$< $(CFLAGS) $(DEFINE)' $@.$$ > $@; \
		rm -f $@.$$;


解释一下：


> build/.deps/%.Po这个东西就是我们在include $(TMP2:%.cpp=build/.deps/%.Po)时的每个存储依赖关系的文件。


   	if ! test -d build; then \
   	   mkdir build; \
   	fi; \
   	if ! test -d build/.deps; then \
   	   mkdir build/.deps; \
   	fi; \

> 这里是因为我们把中间文件都存在build目录下，build/xxx.o，依赖文件都存储在build/.deps目录下，build/.deps/xxx.Po，所以这里判断一下，保证两个目录都存在。

> $* 就是%.Po里的%。

	$(subst -,/,$(subst up,..,$*)

> 作两个替换，恢复cpp文件格式，用来生成依赖关系。

	sed '1s/[^:]*.o/build\/$*.o $(subst /,\/,$@)/' $@.$$ > $@;

> 这个用来作替换，使用g++ -MM abc-xxx.cpp生成的格式是：

	xxx.o: abc-xxx.cpp yyy.h
	
> 这里要把xxx.o替换为build/abc-xxx.o build/.deps/abc-xxx.Po。之所以要把依赖文件也加进去，是为了发现最新的依赖关系。比如添加了头文件之类的。

	sed '$$a\\tg++ -c -o build\/$*.o $$< $(CFLAGS) $(DEFINE)' $@.$$ > $@;

> 之所以要使用自己的生成语句，是为了定制。比如我使用一些特殊标记之类的。注意这里的$$<，在MINGW只需要$<就可以，而在Linux下面则需要$$<。  

> 这里的$@就是build/.deps/%.Po。

其实对于每一个Po文件，最终的makefile有两个依赖关系的，第一个是依赖空，就是在此Makefile写的。第二个是在Po文件里本身定义的，依赖其他代码文件的。第一个是在第一次生成时调用，第二个是在已经生成需要更新时使用。

