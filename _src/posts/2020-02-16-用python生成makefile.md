    Title: 用Python生成Makefile
    Date: 2020-02-16T23:14:31
    Tags: Python Makefile cmake automake xmake make

用Python生成Makefile，更灵活，更直接。


	#python3
	
	import sys
	import os
	import re
	
	#all src dirs
	dirs = [".", "xxx"]
	temp_dir = "temp"
	target = "yourtarget"
	cpp_flags = os.popen("xxx shell command").read().replace("\n","")+" -S -Wall -Wextra -Wpedantic -Dxxx -Ixx -ixxx"	
	cpp_libs = os.popen("shell command").read().replace("\n","")+" -lxxx -Lxxx"
	
	
dirs包含了目录列表，也可以更直接的递归获取所有目录，还可以添加别的非当前目录。   

temp_dir 临时文件的目录，主要是中间文件，xxx.o之类的。   

target 生成的目标    

cpp_flags和cpp_libs分别是编译期和链接期需要用到的，可以使用shell command结果作为参数。   


	############################
	#create tempdir
	if not os.path.exists(temp_dir):
		os.make_dirs(temp_dir)
	

创建临时目录    


	xxx_src = """
	xxx/abc.c
	xxx/def.c
	"""
	
	#########################################
	#split line
	cpp_files = []
	for one_dir in dirs:
		cpp_files = cpp_files + [one_dir+"/"+cpp for cpp in os.listdir(one_dir) if cpp.endswith('.cpp') or cpp.endswith('.c') or cpp.endswith('.cc')]
	
	cpp_files = cpp_files + xxx_src.split()


获取到所有的cpp文件列表，包括遍历得到文件和单独指定的文件    


	
	get_temp_o = lambda cpp:temp_dir+"/"+cpp.replace("./","").replace(".cpp",".o").replace(".cc",".o").replace(".c",".o").replace("/",".")
		
	objs = list(map(get_temp_o, cpp_files))


把“./”去掉，再把“.cpp”,“.cc”,“.c”等后缀换成“.o”，再把中间目录的“/”换成“.”，便于生成中间文件    


	phony = """
	.PHONY: clean all
	all:$(TARGET)
	""".replace("$(TARGET)", target)
	
	flags = """
	CPPFLAGS := $(cpp_flags)
	CPPLIBS := $(cpp_libs)
	
	""".replace("$(cpp_flags)", cpp_flags).replace("$(cpp_libs)", cpp_libs)


字符串替换Makefile指令    


	def get_gen_temp_o(cpp):
		cmd = "g++ -MM $(CPPFLAGS) "+cpp
		return os.popen(cmd.replace("$(CPPFLAGS)", cpp_flags)).read()+"\tg++ -c $< -o $@ $(CPPFLAGS)\n".replace("$(CPPFLAGS)", cpp_flags)
		
	def write_temp_o(make_file, cpp_files):
		for cpp in cpp_files:		
			temp_o = get_temp_o(cpp)		
			make_file.write(re.sub(r'^.*\.o\s*:', temp_o+":", get_gen_temp_o(cpp)))
			make_file.write("\n")
			print(temp_o+":"+cpp)
	
	
get_gen_temp_o辅助函数获取cpp文件的依赖文件    


	target_cmd = """
	$(TARGET):$(OBJS)
		g++ $(OBJS) -o $@ $(CPPLIBS)
	""".replace("$(TARGET)", target).replace("$(OBJS)", " ".join(objs))
	
	clean_cmd = """
	clean:
		rm -f *.d; \
		rm -f *.o; \
		rm -f $(TARGET)
	""".replace("$(TARGET)", target)
	
	
makefile指令生成目标和清理动作    


	with open("Makefile", "w") as make_file:
		print("start write makefile")
		make_file.write(phony)
		print(phony)
		make_file.write(flags)
		print(flags)
		write_temp_o(make_file, cpp_files)
		make_file.write(target_cmd)
		print(target_cmd)
		make_file.write(clean_cmd)
		print(clean_cmd)
		print("end write makefile")
	


最后把所有指令写入Makefile中    



