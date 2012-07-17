---
layout: post
title: "我贡献一个makefile.template"
date: 2012-07-17 09:54
comments: true
categories: 
---

伟大的Makefile，伟大的Make， 伟大的GNU    

只不过规则前面的Tab有时很让人蛋疼，不知道是只能这样？还是有其他更好的替代方案？不知道，留待以后更聪明的人解答吧。    

Make会生成一堆的中间文件，如果和原文件混杂在一起，就会比较混乱，所以一般期望的是中间文件单独放在一起。    

在网上也查了很多资料，当然会查到云风的博客上，我这里提供另外一种思路。    

原来依赖*.o的，现在一律依赖build/*.o      

原来的*.o生成规则，现在一律是build/*.o生成规则。    

    TMP=build    
    
    sources:=main.cpp    
    
    OBJS=$(sources:%.cpp=$(TMP)/%.o)
    
    DEST:$(OBJS)
    
接下来是一些依赖关系，使用sed流操作，把依赖关系生成到单独的文件中    
    #.o:.cpp
    include $(sources:%.cpp=$(TMP)/%.d1)
    #.d:.cpp
    include $(sources:%.cpp=$(TMP)/%.d2)
    
    $(TMP)/%.d1:%.cpp
    	set -e;rm -f $@;\
        g++ -MM $(CPPFLAGS) $< > $*.$$$$;\
    	sed 's,\($*\)\.o[ :]*,$(TMP)/$*.o : ,g' < $*.$$$$ > $*.$$$$.$$$$;\
        rm -f $*.$$$$; \
        sed '$$a\	g++ -c $< -o $(TMP)/$*.o $(CPPFLAGS)' < $*.$$$$.$$$$ > $@; \
        rm -f $*.$$$$.$$$$
    
    $(TMP)/%.d2:%.cpp
    	set -e;rm -f $@;\
        g++ -MM $(CPPFLAGS) $< > $@.$$$$;\
        sed 's,\($*\)\.o[ :]*,$(TMP)/$*.d1 $@ : ,g' < $@.$$$$ > $@;\
        rm -f $@.$$$$; \
    
这样，所有的中间文件都在build/下面，也不会污染代码目录了