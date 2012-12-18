---
layout: post
title : 我贡献一个makefile.template(3)
category : essays
tags : [essays]
---
{% include JB/setup %}

makefile 的升级版本，上一个版本在[这里](http://zhuzhonghua.github.com/essays/2012/12/12/wogongxianyigemakefiletemplate2/)


Note:
这里有一些细微的BUG，所以复制粘贴过去之后，记得做一些小调整。   


这一版本主要解决的问题：


1. 依赖文件也要跟着CPP文件更新。
解决方法，类似.o文件依赖关系一样，建立一个新的文件，PoD，存储.Po文件的依赖关系。   

2. 为了方便书写，引入了命令包。
在命令包里，$*是空的。所以使用$?，然后做一些替换操作。
使用$$随机数字时也出现了问题，前后两个命令的$$输出是不一致的。
所以改成了固定文件名。


复制一下上一版本的部分重要命令，把Po文件单独拆出去了：  


    build/.deps/%.Po:
    	if ! test -d build; then \
    	   mkdir build; \
    	fi; \
    	if ! test -d build/.deps; then \
    	   mkdir build/.deps; \
    	fi; \
		g++ -MM $(subst -,/,$(subst up-,../,$*)).cpp $(CFLAGS) $(DEFINE) > $@; \
		mv -f $@ $@.$$; \
		sed '1s/[^:]*.o/build\/$*.o/' $@.$$ > $@; \
		mv -f $@ $@.$$; \
		sed '$$a\\tg++ -c -o build\/$*.o $$< $(CFLAGS) $(DEFINE)' $@.$$ > $@; \
		rm -f $@.$$;


新加一个命令包，用于在Po文件的依赖变化时调用，在命令包里的命令，末尾不能有\和分号：


	define gMMDepend
		g++ -MM $? $(CFLAGS) $(DEFINE) > $@.tmp
		mv -f $@.tmp $@.tmp2
		sed '1s/[^:]*.o/build\/$(subst /,-,$(subst ..,up,$(subst .cpp,.o,$?)))/' $@.tmp2 > $@.tmp
		mv -f $@.tmp $@.$$.tmp2
		sed '$$a\\tg++ -c -o $$@ $$< $(CFLAGS) $(DEFINE)' $@.tmp2 > $@
		rm -f $@.tmp2
	endef


> 这里的命令和前面的有细微差别，大体相似，可以参考上一版本的解释。


    build/.deps/%.PoD:
    	if ! test -d build; then \
    	   mkdir build; \
    	fi; \
    	if ! test -d build/.deps; then \
    	   mkdir build/.deps; \
    	fi; \
		echo '$(subst .PoD,.Po,$@): $(subst -,/,$(subst up-,..-,$(subst .PoD,.cpp,$@)))' > $@;\
		echo '$$(gMMDepend)' >> $@;

		
末尾别忘了include 一下PoD文件。


所以最终形成的makefile文件应该是这个样子的：  


	0, xxx.o:xxx.cpp xxx.h
	       g++ -c -o -g xxx.cpp xxx.h
		   
    1, xxx.Po:
		   g++ -MM xxx

	2, xxx.Po:xxy.cpp
	       $(gMMDepend)

    ......


> 0的依赖关系是在Po文件里存放的。
1的依赖关系是在Makefile里明白写着的。
2的依赖关系是在Pod文件里存放的。
这样Po文件不存在时，使用1规则。Po文件存在时，使用2规则。


这里写的Makefile只为了一个目的，就是使用一个make命令，只使用这一个命令。
有时候发现我特别爱钻牛角尖，就为了这一点，折腾。

    