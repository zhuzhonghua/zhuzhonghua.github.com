---
layout: post
title : 转换vs工程到Makefile
category : essays
tags : [essays 杂谈 vs2010 vcxproj makefile automake configure]
---
{% include JB/setup %}

---

对于在Windows下开发，正式环境部署在Linux下的项目，需要有一个工具，把VS的工程转换为一个Makefile，方便出版本。

以vs2010的工程为例。

vs2010的工程文件后缀是.vcxproj，假设是helloworld.vcxproj。

用编辑器(如notepad++)打开，找到“ClCompile Include”那些行，发现所有我们的CPP文件。

接下来只需要把cpp文件分析出来，放到makefile.am里，然后使用automake工具，就可以编译项目了。

    sed -n '/ClCompile Include=/p' helloworld.vcxproj | sed -n 's/\\/\//gp' | awk -F= '{print $2}' | awk -F\" '{print $1}' | sed ':label;N;s/\n/ /g;b label'
    
第一段   

    sed -n '/ClCompile Include=/p' helloworld.vcxproj
    
把包含cpp文件的那些行都筛选出来

接下来

    sed -n 's/\\/\//gp'
    
转换反斜杠

再通过awk，使用=和"来把真正的文件名取出来

    awk -F= '{print $2}' | awk -F\" '{print $1}'
    
到这里，输出的每行都是一个单独的文件。

之后有两种处理方式，1. 在除了最后一行外的每一行末，都加反斜杠\，2. 把输出合并为一行，通过空格分开。

这里采用的第2种方式。具体label,N代表什么，搜索 "sed 合并行"。
    
    sed ':label;N;s/\n/ /g;b label'
    
一个大工程，肯定会分多个目录。用类似的方式，也可以分析依赖目录到makefile.am中的INCLUDE中

完整脚本以及automake所需要的configure.in参考如下

[configure.in](https://gist.github.com/zhuzhonghua/84a680e49ad9088731a7)

[make_version.sh](https://gist.github.com/zhuzhonghua/5d8e9796eb8e8664468e)

---

update 2014/11/11

在vs下开发完成后，需要打包

	import os
	
	import tarfile
	
	def is_exclude(fullpath):
		if -1 == fullpath.find(".svn") and \
			-1 == fullpath.find(".opensdf") and \
			-1 == fullpath.find(".obj") and \
			-1 == fullpath.find(".log") and \
			-1 == fullpath.find("Debug") and \
			-1 == fullpath.find("ReadMe.txt") and \
			-1 == fullpath.find(".vcxproj.filters") and \
			-1 == fullpath.find(".vcxproj.user") and \
			-1 == fullpath.find("libprotobuf") and \
			-1 == fullpath.find("node_modules") and \
			-1 == fullpath.find("build") and \
			-1 == fullpath.find("ipch") and \
			-1 == fullpath.find(".zip") and \
			-1 == fullpath.find(".py") and \
			-1 == fullpath.find(".bat") and \
			-1 == fullpath.find("\\test\\") and \
			-1 == fullpath.find(".sdf"):
			return False
		else:
			return True
	
	def add_tar(path, tarfile):
		for root,dir,files in os.walk(path):
			for file in files:
				fullpath = os.path.join(root,file)		
				if False == is_exclude(fullpath):
					print fullpath
					try:
						tarfile.add(fullpath)
					except Exception, e:
						print e
	
	raw_input("click to start")
	
	tar = tarfile.open("server.tar.gz","w:gz")
	
	add_tar(".\\Server", tar)
	
	raw_input("click to close")