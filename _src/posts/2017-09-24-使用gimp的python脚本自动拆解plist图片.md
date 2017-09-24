    Title: 使用gimp的python脚本自动拆解plist图片
    Date: 2017-09-24T13:02:31
    Tags: gimp, python


需要用到的函数 

pdb.gimp_file_load 打开文件  
pdb.gimp_image_crop 裁剪某一区域  
pdb.gimp_image_duplicate 拷贝副本，在副本上操作，不影响原图，可以重复拷贝  
pdb.gimp_file_save 保存操作的图像  


```python

filename="somepngfile"
filexml="someplistfile"
outpath="someoutputpath"

from gimpfu import * # 导入 gimp 里需要操作图像的库
import plistlib # 操作 plist文件
import re # 正则表达式

num = re.compile(r'\d+') # 只要数字，width, height, offsetx, offsety，都是整型

image = pdb.gimp_file_load(filename, filename, run_mode=RUN_NONINTERACTIVE) # 加载原texture图像

pl = plistlib.readPlist(filexml) # 读取plist文件
frames = pl['frames'] # 只需要frames里的字典
print "key count "+str(len(frames.keys())) 

count = 0
for k in frames.keys():
	count = count +1
    try:
		v = frames[k] # k 是PNG名, v就是它对应的width,height,offsetx,offsety
	    outname = outpath+k
		frame = v['frame'] # 具体存储对应信息的字段
		whxy = num.findall(frame)
		offx = int(whxy[0])
		offy = int(whxy[1])
		width = int(whxy[2])
		height = int(whxy[3])
		new_img = pdb.gimp_image_duplicate(image) # 创建图像副本
		pdb.gimp_image_crop(new_img, width, height, offx, offy) # 裁剪副本里对应的区域
		drawable=pdb.gimp_image_get_active_drawable(new_img) 
		pdb.gimp_file_save(new_img, drawable, outname, outname) # 保存对应的文件
		print str(count)+" success "+k
	except Exception as e: # 不知道什么原因，有些文件会出错，加上异常保护，不影响后面其他的文件拆解
		print str(count)+" fail "+k
		print e
  
```


